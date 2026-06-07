# Headroom — Evaluación para el equipo Nelson

**Repo:** https://github.com/chopratejas/headroom
**Fecha:** 2026-06-06
**Stars:** 15.831 | **Forks:** 1.007 | **License:** Apache 2.0
**Último commit:** 2026-06-06 (activo, commits diarios)
**PyPI:** `headroom-ai` | **npm:** `headroom-ai` | **HF model:** `chopratejas/kompress-base`

---

## TL;DR

Capa de compresión de contexto para agentes de IA. Ahorra 60-95% de tokens
comprimiendo tool outputs, logs, RAG chunks, archivos e historial antes de
llegar al LLM. Cuatro formas de uso: library, proxy, wrap-agent, MCP server.

**Decisión para el equipo:** NO integrar al pipeline. Posible spike corto
para validar la pieza standalone **Kompress-base** (modelo HF).

---

## Veredicto ejecutivo

| Aspecto | Valoración |
|---|---|
| Calidad del código | Buena (CI verde, cobertura vía Codecov) |
| Documentación | Completa (docs online, llms.txt, enterprise guide) |
| Madurez | Media-alta (v1.x estable, integrations con Anthropic/OpenAI SDK, Vercel AI SDK, LiteLLM, LangChain, Agno, Strands) |
| Actividad | Muy activa (commits diarios, Discord) |
| Licencia | Apache 2.0 (uso comercial OK) |
| Riesgos | Wrapper de agentes (suma complejidad) |

**Recomendación:** Archivado. Spike opcional en Kompress-base.

---

## Qué hace

```
 Your agent / app
   (Claude Code, Cursor, Codex, LangChain, Agno, Strands, your own code…)
        │   prompts · tool outputs · logs · RAG results · files
        ▼
    ┌────────────────────────────────────────────────────┐
    │  Headroom   (runs locally — your data stays here)  │
    │  ────────────────────────────────────────────────  │
    │  CacheAligner  →  ContentRouter  →  CCR            │
    │                    ├─ SmartCrusher   (JSON)        │
    │                    ├─ CodeCompressor (AST)         │
    │                    └─ Kompress-base  (text, HF)    │
    │                                                    │
    │  Cross-agent memory  ·  headroom learn  ·  MCP     │
    └────────────────────────────────────────────────────┘
        │   compressed prompt  +  retrieval tool
        ▼
 LLM provider  (Anthropic · OpenAI · Bedrock · …)
```

### Modos de uso

1. **Library** — `compress(messages, model=…)` inline en Python o TS
2. **Proxy** — `headroom proxy --port 8787`, drop-in para cualquier cliente OpenAI-compatible
3. **Wrap agent** — `headroom wrap claude|codex|cursor|aider|copilot` con un comando
4. **MCP server** — `headroom_compress`, `headroom_retrieve`, `headroom_stats` para MCP

### Componentes clave

- **CacheAligner** — estabiliza prefijos para que los KV caches de Anthropic/OpenAI peguen
  (CRÍTICO: sin esto, comprimir ahorra tokens pero rompe cache y sube el costo real)
- **SmartCrusher** — JSON universal (arrays, nested, mixed types)
- **CodeCompressor** — AST-aware para Python, JS, Go, Rust, Java, C++
- **Kompress-base** — modelo en HuggingFace entrenado en traces de agentes
- **CCR (Compress-Compress-Retrieve)** — reversible: el LLM pide el original con
  `headroom_retrieve` si lo necesita
- **Cross-agent memory** — compartida entre Claude/Codex/Gemini con auto-dedup
- **`headroom learn`** — minera de sesiones fallidas → escribe correcciones a
  `CLAUDE.md` / `AGENTS.md`

### Integraciones oficiales

| Stack | Hook |
|---|---|
| Python app | `compress(messages, model=…)` |
| TypeScript app | `await compress(messages, { model })` |
| Anthropic / OpenAI SDK | `withHeadroom(new Anthropic())` / `withHeadroom(new OpenAI())` |
| Vercel AI SDK | `wrapLanguageModel({ model, middleware: headroomMiddleware() })` |
| LiteLLM | `litellm.callbacks = [HeadroomCallback()]` |
| LangChain | `HeadroomChatModel(your_llm)` |
| Agno | `HeadroomAgnoModel(your_model)` |
| Strands | Guide dedicado |
| ASGI apps | `app.add_middleware(CompressionMiddleware)` |
| Multi-agent | `SharedContext().put / .get` |
| MCP clients | `headroom mcp install` |

---

## Métricas publicadas

### Savings (workloads reales)

| Workload | Antes | Después | Ahorro |
|---|---:|---:|---:|
| Code search (100 results) | 17,765 | 1,408 | **92%** |
| SRE incident debugging | 65,694 | 5,118 | **92%** |
| GitHub issue triage | 54,174 | 14,761 | **73%** |
| Codebase exploration | 78,502 | 41,254 | **47%** |

### Accuracy preservada

| Benchmark | Categoría | N | Baseline | Headroom | Delta |
|---|---|---:|---:|---:|---|
| GSM8K | Math | 100 | 0.870 | 0.870 | **±0.000** |
| TruthfulQA | Factual | 100 | 0.530 | 0.560 | **+0.030** |
| SQuAD v2 | QA | 100 | — | 97% | 19% compression |
| BFCL | Tools | 100 | — | 97% | 32% compression |

Reproducible con `python -m headroom.evals suite --tier 1`.

---

## Lo que más vale para el equipo

### 1. CacheAligner (la pieza más interesante)

El verdadero costo de los LLMs está en los tokens que **entran al cache**.
Si comprimís el contexto sin alinear prefijos, ahorrás tokens outbound pero
**rompés el cache de Anthropic/OpenAI** y pagás full price por cada turno.

Headroom resolvió esto con CacheAligner — estabiliza los prefijos para que
los KV caches peguen. Esto se podría portar a nuestro pipeline sin necesidad
de instalar headroom entero.

**Aplicación directa:** para nuestros hooks `nelson-llm-generation` y
`nelson-lean-ctx`, ver si podemos alinear prefijos en FreeLLMAPI proxy.

### 2. Kompress-base (modelo standalone)

`chopratejas/kompress-base` en HuggingFace — modelo entrenado en traces
de agentes para comprimir texto. Se puede descargar y usar sin el resto de
headroom. Si funciona bien, podría ser un módulo de nuestro pipeline.

**Aplicación directa:** spike corto (1-2 días) en un proyecto chico para
medir latencia vs ahorro.

### 3. CCR (reversible compression) — patrón a copiar

El patrón de guardar el original localmente y darle al LLM una herramienta
`retrieve` para pedirlo on-demand es la forma correcta de comprimir contexto.
Mejor que truncado o summary que pierde información.

**Aplicación directa:** ya hacemos algo similar con `ctx_read` (modos
`map|signatures|diff|full`). CCR es más formal — guardar originales con hash
y un retrieve que confirma el hash.

### 4. `headroom learn` — auto-mejora de agentes

Minea sesiones fallidas y escribe correcciones a `CLAUDE.md` / `AGENTS.md`.
Esto automatiza lo que hoy hacemos manualmente con la memoria de nelson-
brainstorming y los sync-to-repo.

**Aplicación directa:** interesante para nuestro flow de skill curation
(ver skill `hermes-agent-skill-authoring`).

---

## Lo que no vale la pena para nosotros

### 1. Wrapper de agentes (headroom wrap)

Sumaría una capa más entre Hermes Agent y los providers. Ya tenemos
FreeLLMAPI como proxy, y Hermes tiene su propio `auxiliary` system.
Complejidad sin retorno claro.

### 2. Cross-agent memory (Claude/Codex/Gemini)

No corremos esos agentes en paralelo. JARVIS (yo) y los agentes del
equipo (Julián, Mercedes) ya comparten contexto via nelson-brainstorming
y nelson-context-handoff.

### 3. CodeCompressor (AST-aware)

Útil para análisis de codebase, pero solo aplica a casos puntuales. Para
nuestros flujos (PoCs, dashboards, RAG), la compresión de JSON y prose
ya cubre 90% de los casos.

### 4. Image compression (40-90%)

No es relevante — nuestros flujos mandan imágenes directo al modelo o
via vision_analyze, no las almacenamos para retrieval.

---

## Comparativa con lo que ya tenemos

| Capacidad | Headroom | lean-ctx (nuestro) | FreeLLMAPI |
|---|---|---|---|
| Comprimir tool output | ✅ 6 algoritmos | ✅ ctx_shell con pattern compression | ⚠️ parcial (cooldowns, routing) |
| Comprimir RAG chunks | ✅ Kompress-base | ✅ ctx_read con 10 modos | ❌ |
| Comprimir archivos | ✅ CodeCompressor AST | ✅ ctx_read (map/signatures/diff) | ❌ |
| Cache alignment | ✅ CacheAligner | ❌ | ❌ |
| Reversible | ✅ CCR + retrieve | ⚠️ ctx_read cambia modo pero no recupera original | ❌ |
| Cross-agent memory | ✅ shared store | ❌ | ❌ |
| Library + Proxy + MCP | ✅ los 3 | ⚠️ solo MCP | ✅ solo proxy |
| Ya integrado | ❌ | ✅ (cargado en este server) | ✅ (corriendo) |
| Customizable al equipo | ❌ genérico | ✅ pensado para Nelson | ✅ con nuestras keys |

**Conclusión:** lean-ctx + FreeLLMAPI cubren 70% del valor de headroom
con la ventaja de estar integrados. El 30% restante (CacheAligner,
Kompress-base, CCR formal) vale la pena como spike aislado.

---

## Si decidimos hacer el spike (1-2 días)

### Setup mínimo

```bash
# 1. Instalar headroom con todas las deps
pip install "headroom-ai[all]"

# 2. Levantar el proxy en un puerto libre
headroom proxy --port 8787

# 3. Apuntar un cliente OpenAI-compatible ahí y medir:
# - Tokens ahorrados
# - Latencia agregada
# - Accuracy en tareas reales del equipo
```

### Métricas a capturar

- **Tokens in/out antes vs después** en 5 sesiones reales (un proyecto ForestAI
  y uno Bisonte es suficiente)
- **Latencia promedio** — cuánto tarda la compresión (incluye inferencia si
  usa Kompress-base)
- **VRAM/CPU usado** — el modelo local necesita recursos
- **Cache hit rate** en Anthropic — esto es el dato clave
- **Errores o degradación** — ¿contesta igual de bien?

### Criterios de éxito

- ≥ 50% ahorro de tokens en workloads reales del equipo
- < 200ms latencia agregada por request
- Cache hit rate de Anthropic **mejora o se mantiene**
- Accuracy en tareas críticas (code review, RAG) sin degradación

Si falla cualquiera → archivar. Si pasa → evaluar integración a FreeLLMAPI
como `headroom_middleware` opcional.

---

## Decisión

**Archivado.** No integrar al pipeline del equipo Nelson.

Razones principales:
1. Ya tenemos lean-ctx + FreeLLMAPI cubriendo 70% del valor
2. Wrapper de agentes suma complejidad sin ROI claro
3. El modelo Kompress-base necesita evaluación en workloads reales nuestros
4. Los benchmarks publicados (92%) son en workloads específicos, no garantizan
   los mismos números en nuestro contexto

**Próximos pasos posibles (NO comprometidos):**
- Spike Kompress-base aislado en un proyecto chico (1-2 días)
- Portar CacheAligner a FreeLLMAPI proxy
- Evaluar CCR como patrón formal para lean-ctx

---

## Referencias

- Repo: https://github.com/chopratejas/headroom
- Docs: https://headroom-docs.vercel.app/docs
- Architecture: https://headroom-docs.vercel.app/docs/architecture
- Benchmarks: https://headroom-docs.vercel.app/docs/benchmarks
- Model card: https://huggingface.co/chopratejas/kompress-base
- llms.txt: https://headroom-docs.vercel.app/llms.txt
- Discord: https://discord.gg/yRmaUNpsPJ
- Enterprise: https://github.com/chopratejas/headroom/blob/main/ENTERPRISE.md

## Skills/skills relacionadas del equipo

- `nelson-lean-ctx` — ya integrado, cubre compresión básica
- `nelson-llm-generation` — FreeLLMAPI, donde se podría portar CacheAligner
- `nelson-context-handoff` — protocolo de paso de contexto entre agentes
- `nelson-embeddings` — RAG, donde Kompress-base podría aplicarse
- `hermes-agent-skill-authoring` — patrón similar a `headroom learn`
