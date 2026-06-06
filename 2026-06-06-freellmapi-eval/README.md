# FreeLLMAPI — Evaluación rápida

**Fecha:** 2026-06-06
**Fuente:** https://github.com/tashfeenahmed/freellmapi
**Estado:** Pendiente de PoC / decisión de deploy
**Trigger:** Nelson preguntó qué opino sobre el repo

## TL;DR

Proxy OpenAI-compatible que apila los free tiers de 16 LLM providers
detrás de un solo `/v1/chat/completions`. MIT, TypeScript, single-user.
7.9k stars, 1.3k forks, activo (último push ayer).
**Brutal para uso personal y para JARVIS/Hermes.**
**No apto para producción comercial multi-tenant.**

## Ficha técnica

| Campo | Valor |
|---|---|
| Autor | tashfeenahmed |
| License | MIT |
| Lenguaje | TypeScript (Express + React/Vite) |
| Runtime | Node 20+ |
| Stars / Forks | 7,945 / 1,293 |
| Issues abiertos | 29 |
| Activo | push 2026-06-05 (ayer) |
| Multi-arch | linux/amd64 + linux/arm64 (RPi) |
| Imagen | ghcr.io/tashfeenahmed/freellmapi |
| RSS idle | ~40 MB |
| Tamaño repo | 3.1 MB |

## Providers soportados (16)

Google (Gemini 2.5/3.x), Groq, Cerebras, SambaNova, Mistral, OpenRouter
(21 modelos), GitHub Models (GPT-4.1/o), Cloudflare Workers AI, Cohere,
Z.ai (Zhipu), NVIDIA NIM, HuggingFace Router, Ollama Cloud, Kilo,
Pollinations, LLM7. Más un **custom provider** para cualquier endpoint
OpenAI-compatible (llama.cpp, LM Studio, vLLM, Ollama local).

**Capacidad agregada reportada:** ~1.7B tokens/mes.

## Capacidades

- **OpenAI-compatible** — drop-in: cambiás `base_url` y listo
- **Responses API** — shim traductor para Codex CLI actual
- **Streaming + non-streaming** — SSE estándar
- **Tool calling** — OpenAI-style round-trip entre providers
- **Embeddings** — family-based routing (failover no cruza modelos →
  vectores compatibles)
- **Vision** — auto-routing a modelos con capacidad de imagen
- **Automatic failover** — hasta 20 intentos en cadena
- **Sticky sessions 30 min** — evita hallucination spike por switch
  mid-conversation
- **Encrypted key storage** — AES-256-GCM, scrypt para admin
- **Unified API key** — un solo bearer token para clients
- **Health checks** — periodic probe (healthy / rate_limited / invalid / error)
- **Per-key rate tracking** — RPM/RPD/TPM/TPD por (platform, model, key)
- **Analytics** — latency, tokens, success rate, breakdowns
- **Admin dashboard** — React + Vite, dark mode

## Arquitectura (resumida)

```
OpenAI SDK / curl / any client
    │   Bearer freellmapi-…
    ▼
Express proxy (:3001)  →  /v1/chat/completions
    │
    ▼
Router: pick highest-priority model que
        (a) tenga key healthy y
        (b) esté bajo todos los rate limits.
        Decrypt key → call provider SDK.
        On 429/5xx → cooldown + retry.
    │
    ▼
16 providers (Google, Groq, Cerebras, …)
```

Archivos clave:
- `server/src/services/router.ts`
- `server/src/services/ratelimit.ts`
- `server/src/providers/*.ts` (uno por provider)
- `server/src/services/health.ts`

## Lo bueno (concreto)

1. **Drop-in replacement** — `base_url` apuntás a `localhost:3001/v1`
2. **Failover real** — si un provider te banea la key, el siguiente toma
3. **Embeddings cuidadosos** — no mezcla espacios vectoriales
4. **Multi-arch** — corre en RPi (40MB RSS)
5. **Encrypted at rest** + scrypt para admin
6. **Vision routing** inteligente
7. **ToS review** en el README — autor es consciente del riesgo
8. **Active development** — push de ayer
9. **MIT** — podés forkear/portar
10. **Sticky sessions** — feature que muchos proxies no tienen

## Lo malo (de verdad)

1. **ToS gris** — la mayoría de los providers dicen "no revender / no
   automatizar" en sus free tiers. Cada uno puede banearte.
2. **Single-user only** — no multi-tenant. Si querés exponerlo a clientes
   de AlegentAI, tenés que forkear.
3. **Uptime no garantizado** — depende 100% de free tiers manteniéndose
4. **Sin image/audio/moderation** — solo chat + embeddings
5. **Sin legacy `/v1/completions`** — solo chat
6. **n=1 no soportado** — un completion por request
7. **Trust_remote_code equivalente** — dependés de las SDKs de cada
   provider

## Match con stack Nelson

| Proyecto | Veredicto | Razón |
|---|---|---|
| Hermes/JARVIS personal | ✅ **Excelente** | Drop-in en base_url, failover gratis, 16 modelos |
| nelson-llm-generation | ✅ **Útil** | Reemplaza OpenRouter directo, agregás resiliencia |
| Agents de equipo (Julián/Mercedes) | ✅ **Excelente** | Mismo drop-in, evita un solo provider como SPOF |
| ForestAI | ⚠️ PoC interno OK, producción no | ToS riesgo, sin SLA |
| Expreso Bisonte | ❌ No | Necesita SLA, no free tiers |
| AlegentAI (clientes) | ❌ No | Multi-tenant requerido + ToS |

## Quick start (Docker Compose)

```bash
git clone https://github.com/tashfeenahmed/freellmapi.git
cd freellmapi

ENCRYPTION_KEY="$(openssl rand -hex 32)"
printf "ENCRYPTION_KEY=%s\nPORT=3001\n" "$ENCRYPTION_KEY" > .env

docker compose up -d
```

UI: http://localhost:3001

Default: bind a 127.0.0.1. Para LAN:
```bash
HOST_BIND=0.0.0.0 docker compose up -d
```

## Próximos pasos (decisión)

1. [ ] **Decidir:** ¿deploy en ai-server esta semana para uso de Hermes?
2. [ ] Si sí, generar ENCRYPTION_KEY y guardar en .env seguro
3. [ ] Agregar keys de los providers que ya usás (Groq, Gemini, Cerebras)
4. [ ] Configurar fallback chain priorizando NVIDIA NIM + Ollama local
5. [ ] Probar vision routing con imagen de muestra
6. [ ] Evaluar si vale forkear para versión multi-tenant (AlegentAI)

## Riesgos a monitorear

- Provider X cambia ToS → perdés acceso
- Free tier se discontinúa (ya pasó con varios en 2024-2025)
- Bug en router → requests van al modelo equivocado
- Rate limits muy agresivos → cascade de cooldowns

## Referencias

- Repo: https://github.com/tashfeenahmed/freellmapi
- Imagen: ghcr.io/tashfeenahmed/freellmapi
- Demo UI: corre local en :3001
- License: MIT
