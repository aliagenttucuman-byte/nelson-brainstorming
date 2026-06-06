# FreeLLMAPI + Reglas de Routing LLM — Setup Final

**Fecha:** 2026-06-06
**Estado:** Activo en producción (ai-server)
**Trigger:** Nelson pidió "como regla que uses Anthropic, y para PoCs cascada con el orden dado"

## Reglas de routing LLM (Nelson, 2026-06-06)

### Regla 1 — JARVIS / uso personal
**Siempre Anthropic Claude.**
- Endpoint: `https://yizlafclc001.services.ai.azure.com/anthropic/`
- Model: `claude-sonnet-4-6`
- Env var: `AZURE_ANTHROPIC_API_KEY`
- Wire format: `anthropic_messages`

### Regla 2 — Para PoCs y otros casos
**Cascada de fallback en este orden:**
1. **OpenAI directo** (custom provider)
   - gpt-4o-mini
   - gpt-4o
   - o4-mini
2. **Groq**
   - llama-3.3-70b-versatile
3. **OpenRouter free** (21 modelos)
   - Qwen3 Coder, GLM-4.5 Air, Nemotron 3 Super 120B, Qwen3-Next 80B,
     GPT-OSS 120B, Llama 3.3 70B, GPT-OSS 20B, Nemotron 3 Nano 30B,
     Gemma 4 31B, Liquid LFM 2.5 1.2B, Poolside Laguna M.1/XS.2,
     Gemma 4 26B-A4B, Nemotron 3 Nano 30B Reasoning, Nemotron Nano 9B v2,
     Liquid LFM 2.5 1.2B Thinking, Hermes 3 405B, Owl Alpha (OR-house)
4. **Keyless trial / anónimos**
   - Kilo Gateway (keyless, 200/hr per IP)
   - OpenCode Zen ❌ DESCARTADO (key sk-EASBo2t...5bEtHu da Cloudflare 1010)
   - Pollinations
   - LLM7

## Stack desplegado

### Router: FreeLLMAPI
- **Puerto interno:** 3001 (dentro del container)
- **Puerto externo:** 3101 (3001 ocupado por WhatsApp Gateway)
- **URL Tailscale:** `http://100.110.8.13:3101`
- **URL local:** `http://127.0.0.1:3101`
- **Dashboard:** `http://100.110.8.13:3101` (login + keys management)
- **Unified key:** `freellmapi-0b0b33d6a9c82a2b15ec6e2006867256e26e7b244e71a57d`
- **Admin:** `nelsongacosta@gmail.com` / `123456789` (CAMBIAR EN PRODUCCIÓN)
- **Repo:** `/home/server/proyectos/freellmapi/`
- **Imagen:** `ghcr.io/tashfeenahmed/freellmapi:latest` (build local con provider Anthropic agregado)
- **Data volume:** `freellmapi_freellmapi-data` (DB SQLite persistente)

### Provider Anthropic nativo (nuestro, contribución pendiente)
**Archivos modificados/creados:**
1. `server/src/providers/anthropic.ts` (nuevo, ~440 líneas) — adapter Anthropic nativo con chat, streaming, tool use
2. `server/src/providers/index.ts` — registro + `resolveProvider('anthropic', baseUrl)`
3. `server/src/services/router.ts` — usa `resolveProvider` con `base_url` del row
4. `server/src/routes/keys.ts` — schema acepta `baseUrl` opcional
5. `shared/types.ts` — `'anthropic'` agregado al `Platform` union

**Features del adapter:**
- Chat no-stream + streaming SSE
- Tool use / tool result mapping (OpenAI ↔ Anthropic)
- System prompt como top-level field (no en messages)
- Stop reason mapping: end_turn/max_tokens/tool_use → stop/length/tool_calls
- `withBaseUrl()` para per-key endpoints
- Headers: `x-api-key` + `anthropic-version: 2023-06-01`
- Timeout 60s, streaming stall 90s

### Keys cargadas en el router

| Provider | Key (masked) | baseUrl | Modelos |
|---|---|---|---|
| anthropic | `2uIp...QHTb` | `https://yizlafclc001.services.ai.azure.com/anthropic` | claude-sonnet-4-6 |
| custom (OpenAI) | `sk-p...cQEA` | `https://api.openai.com/v1` | gpt-4o-mini, gpt-4o, o4-mini |
| groq | `gsk_...Tw0p` | (default) | llama-3.3-70b-versatile |
| openrouter | `sk-o...2c8a` | (default) | 21 modelos free autodetectados |

## Tests pasados (post-deploy)

| Test | Routing | Latencia | Resultado |
|---|---|---|---|
| `model=claude-sonnet-4-6` | anthropic | ~3s | "Hallo!" / "París" ✅ |
| `model=gpt-4o-mini` | custom (OpenAI) | <1s | "París." ✅ |
| `model=llama-3.3-70b-versatile` (Groq) | groq | <1s | "Konnichiwa" ✅ |
| `model=meta-llama/llama-3.3-70b-instruct:free` (OR) | openrouter (fallback x2) | - | failover real ✅ |
| `model=auto` | openrouter (Nemotron 3 Super 120B) | - | router eligió top del chain ✅ |

## Configuración de la sesión (Hermes/JARVIS)

`/home/server/.hermes/config.yaml` ya tiene `custom_providers` configurado:
```yaml
- name: azure-claude
  base_url: https://yizlafclc001.services.ai.azure.com/anthropic/
  model: claude-sonnet-4-6
  key_env: AZURE_ANTHROPIC_API_KEY
  api_mode: anthropic_messages
```

`/home/server/.hermes/.env` tiene `AZURE_ANTHROPIC_API_KEY=2uIp0J...QHTb` exportado.

## Bugs encontrados y resueltos durante el setup

1. **Puerto 3001 ocupado** por WhatsApp Gateway (Node, no Docker). Resuelto: usar 3101.
2. **OpenCode key inválida** — `sk-EASBo2t...5bEtHu` da Cloudflare 1010. Resuelto: descartar, no cargar.
3. **Azure Anthropic requiere path `/anthropic`** — el endpoint de Azure Foundry no es root, hay que incluir el prefijo. Resuelto: documentar en el adapter y usar el baseUrl completo del usuario.
4. **OpenAI directo NO funciona para Claude en Azure** — el endpoint `/openai/v1/chat/completions` no soporta Claude. Resuelto: provider Anthropic nativo separado.
5. **Modelo `claude-sonnet-4-6` (prefijo Azure Foundry)** vs `claude-sonnet-4-5-20250929` — su recurso Azure solo tiene el modelo corto `claude-sonnet-4-6` deployado. Opus 4.7 y Haiku 4.5 no existen en su recurso.

## Próximos pasos

1. **Hacer PR upstream** del provider Anthropic a `tashfeenahmed/freellmapi` — es contribución limpia
2. **Cambiar admin password** del dashboard FreeLLMAPI (actualmente `123456789`)
3. **Cambiar `base_url` de un agente** (Julián/Mercedes) para que use FreeLLMAPI
4. **Construir el módulo "Alegent LLM Router"** v0.1 con capability routing encima

## Referencias

- Repo upstream: https://github.com/tashfeenahmed/freellmapi
- Local: `/home/server/proyectos/freellmapi/`
- Brainstorming: `~/brainstorming/2026-06-06-freellmapi-eval/README.md`
- Demo: `http://100.110.8.13:3101`
