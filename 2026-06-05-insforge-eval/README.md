# InsForge — Evaluación Ejecutiva

| Campo | Valor |
|-------|-------|
| **Repo** | https://github.com/InsForge/InsForge |
| **Autor** | InsForge Inc. |
| **Licencia** | Apache-2.0 |
| **Stars** | 10,956 |
| **Forks** | 947 |
| **Lenguaje** | TypeScript |
| **Edad** | ~10 meses (created 2025-07-29) |

## Qué es

**Backend open-source todo-en-uno pensado para coding agents** (Claude Code, Cursor, Windsurf, etc.). La propuesta es ser el "Supabase para agents":

- **DB** (PostgreSQL)
- **Auth** (OAuth, email, JWT)
- **Storage** (S3-compatible)
- **Compute** (edge functions)
- **Hosting**
- **AI gateway** (LLM proxy)
- **MCP server** para que coding agents operen el backend como backend engineers

## Comparativa con Supabase

| Aspecto | Supabase | InsForge |
|---------|----------|----------|
| Target devs humanos | ✓ | — |
| Target coding agents | — | ✓ |
| MCP server nativo | experimental | first-class |
| CLI + Skills | — | ✓ |
| Madurez | 5+ años, postgres maduro | 10 meses, greenfield |

## Veredicto

### ❌ No para nuestros PoCs activos

- **Expreso Bisonte** (FastAPI + React) ya funciona; reemplazarlo por InsForge es tirar valor.
- **ForestAI** (FastAPI + PostGIS + YOLO/SAM) ya funciona; PostGIS no es el fuerte de InsForge.
- **Regla de oro del equipo**: Python backend / React frontend. InsForge es TypeScript puro end-to-end → colisión de stack.

### 🤔 Considerar en 6-12 meses

- **Si la tracción se mantiene o crece** (>20k stars, sponsors, empresas usándolo en prod): re-evaluar como **alternativa más ágil a Supabase** para MVPs nuevos.
- **Si surge un PoC donde un coding agent arma un backend solo** (sin nuestro stack, sin PostGIS, sin Python) → InsForge puede brillar.
- **Watch out for:** cambios de licencia, pivot del producto, si abandonan el proyecto.

### ✅ Para qué SÍ nos sirve hoy

- **Benchmark conceptual**: ver cómo hacen el "AI gateway" y el "MCP server" para tomar ideas para nuestro meta-orchestrator.
- **Comparar UX del MCP server** vs cómo los agentes operan nuestro stack (scripts, skills, custom_tools).
- **Referencia arquitectónica** para cuando tengamos que hacer un backend-as-a-service nuestro.

## Riesgos

- **Madurez**: 10 meses es muy poco para infraestructura crítica. Ni lo pensaría para un cliente en producción hoy.
- **Single-vendor**: fundador único conocido (InsForge Inc.). ¿Qué pasa si cierran o pivotean?
- **TypeScript-only**: bloquea adopción en nuestro stack Python.
- **Lock-in de la AI gateway**: si lo usamos para abstraer LLMs propios (Anthropic, OpenAI, Azure), y luego InsForge cambia pricing o cierra, migrar es caro.

## Comparativa con stack alternativo

Si en algún momento necesitamos un BaaS para un PoC rápido sin nuestro stack Python:

| Opción | Madurez | Agente-friendly | Self-host | Costo |
|--------|---------|-----------------|-----------|-------|
| **Supabase** | ✓ alta | parcial | ✓ | free + pay |
| **InsForge** | ⚠ baja (10m) | ✓ first-class | ✓ | por definir |
| **Appwrite** | ✓ media | bajo | ✓ | free + pay |
| **Firebase** | ✓ alta | bajo | ❌ | pay-as-you-go |

## Conclusión

**Radar.** Guardamos la URL y la propuesta de valor, pero **no lo integramos**. Si en 6-12 meses tiene tracción, sponsors corporativos, y un módulo de PostGIS decente, lo re-evaluamos como alternativa a Supabase para MVPs donde no importe el stack Python.

**Mismo status que MLflow y Molmo2:** evaluación guardada, no adopción.

## Links

- [Repo](https://github.com/InsForge/InsForge)
- [Sitio oficial](https://insforge.dev)
- [Discord](https://discord.com/invite/MPxwj5xVvW)
