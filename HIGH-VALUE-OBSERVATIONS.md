# Observaciones Destacadas — Sesiones de Evaluación

Este archivo guarda las observaciones puntuales de JARVIS que merecen
seguimiento futuro, sin ser tareas concretas todavía. Son ideas filtradas
durante la evaluación de librerías/herramientas.

---

## 2026-06-06 — CacheAligner de Headroom → Portar a FreeLLMAPI

**Contexto:** Evaluación de [headroom (chopratejas/headroom)](2026-06-06-headroom-eval/README.md).
Headroom en sí fue archivado (lean-ctx + FreeLLMAPI cubren 70% del valor).

**Observación destacada de JARVIS:**

La pieza **CacheAligner** de headroom es lo más interesante para nosotros.
Resuelve un problema real y específico: cuando comprimís el contexto, los
prefijos cambian y los KV caches de Anthropic/OpenAI no pegan. Resultado
neto: ahorrás tokens outbound pero pagás full price inbound porque el
cache rompe.

**Por qué nos sirve:** FreeLLMAPI proxy (skill `nelson-llm-generation`)
actualmente no alinea prefijos. Si comprimimos logs o tool outputs en el
adaptador, podríamos estar rompiendo el cache de Anthropic sin saberlo.

**Por qué no se hizo hoy:** Headroom completo es overkill. CacheAligner
es ~50-100 líneas de código Python. Spike aislado de 1-2 días.

**Próximo paso natural (cuando haya bandwidth):**
1. Medir cache hit rate actual en FreeLLMAPI con Anthropic
2. Implementar CacheAligner mínimo: hash del prefijo, descartar chunks
   que cambian el hash, agrupar requests con mismo prefijo
3. Medir de nuevo y comparar

**Skills/skills relacionadas:**
- `nelson-llm-generation` (FreeLLMAPI proxy — dónde se implementaría)
- `nelson-lean-ctx` (ctx_shell ya hace pattern compression — efecto colateral
  potencial)
- `nelson-llm-generation/references/freellmapi-deploy-and-usage.md`

**Status:** NO COMPROMETIDO. Anotado para futuro.

---
