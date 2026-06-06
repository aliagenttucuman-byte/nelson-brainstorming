# PoC con IA — Template de README (formato Nelson + I+D+I)

**Uso:** copiar este template a `~/brainstorming/YYYY-MM-DD-nombre-poc/README.md` al arrancar una PoC que use IA/LLM. Cargar la skill `nelson-poc-ai-quickstart` antes.

---

# [Nombre corto de la PoC]

**Fecha:** YYYY-MM-DD
**Estado:** [idea | spec | implementing | demo | archived]
**Stack:** Python (FastAPI) + React (Vite) + LLM (FreeLLMAPI proxy)
**Trigger:** [一句话: qué motivó esta PoC]

## Hipótesis (formato Nelson)

**CREEMOS QUE** al [construir X],
**RESULTARÁ EN** [output concreto verificable].

## TL;DR

[2-3 oraciones: qué hace, para quién, por qué importa]

## Stack

- **Backend:** Python 3.12 + FastAPI + SQLAlchemy + Pydantic
- **Frontend:** React 19 + Vite + TypeScript + shadcn/ui
- **LLM:** FreeLLMAPI proxy (`http://100.110.8.13:3101/v1`, key en `.env` como `FREELLMAPI_KEY`)
- **Modelo default:** `gpt-4o-mini` (cambiar a `auto` o `claude-sonnet-4-6` si la tarea lo amerita)
- **Docker:** compose con backend (:8000) + frontend (:3000) + postgres (:5432)
- **Tunnel:** Cloudflare (si hay demo externa)

## User Stories (formato Nelson)

### HU-01 — [Título]

**CREEMOS QUE** al [acción],
lograremos [beneficio].

**RESULTARÁ EN** [artefacto concreto].

**CRITERIOS DE ACEPTACIÓN:**
- [ ] Dado [contexto], cuando [acción], entonces [resultado]
- [ ] Dado [contexto], cuando [acción], entonces [resultado]
- [ ] [Criterio no-funcional: tiempo/límite/comportamiento]
- [ ] [Criterio de resiliencia: qué pasa si falla]

### HU-02 — [Título]
[...]

## Plan (3-5 tareas, 2-3 días máx)

| # | Tarea | Asignado | Tiempo | Estado |
|---|---|---|---|---|
| 1 | Scaffolding FastAPI + OpenAI client | Beto / JARVIS | 1h | ⬜ |
| 2 | Implementar endpoint principal | Beto | 2h | ⬜ |
| 3 | Frontend mínimo (form + display) | Nico / JARVIS | 2h | ⬜ |
| 4 | Smoke test con proxy + 5 prompts | Tony | 30min | ⬜ |
| 5 | Demo en vivo | Tony | 15min | ⬜ |

## Smoke test (5 minutos)

```bash
# 1. Verificar proxy accesible
curl -sS http://100.110.8.13:3101/api/ping
# HTTP 200

# 2. Test con Python
python3 -c "
from openai import OpenAI
client = OpenAI(
    base_url='http://100.110.8.13:3101/v1',
    api_key='\${FREELLMAPI_KEY}',
)
resp = client.chat.completions.create(
    model='gpt-4o-mini',
    messages=[{'role':'user','content':'hola en una palabra'}],
    max_tokens=10,
)
print('OK:', resp.choices[0].message.content)
print('Routed via:', resp._routed_via)
"
```

## Criterios de éxito (verificables)

- [ ] [Criterio 1: medible y binario]
- [ ] [Criterio 2]
- [ ] [Criterio 3]
- [ ] [Criterio 4]

## Logs / Resultados

_(llenar post-demo con outputs reales, latencias, costos)_

## Próximos pasos (si funciona)

- [ ] Promover a `nelson-spec-driven-workflow` (flujo completo de 8 fases)
- [ ] Estimar con `nelson-pricing-model`
- [ ] Integrar al `nelson-llm-generation` skill
- [ ] Crear `references/` con detalle técnico

## Próximos pasos (si no funciona)

- [ ] Archivar con nota "no válido" y razón
- [ ] [Pivotar a X variante]

## Referencias

- `nelson-poc-ai-quickstart` skill (reglas de routing)
- `nelson-llm-generation/references/freellmapi-deploy-and-usage.md`
- `nelson-spec-driven-workflow` (flujo I+D+I)
- [Otras refs relevantes]
