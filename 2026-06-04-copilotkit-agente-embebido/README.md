# Brainstorming: CopilotKit para App con Agente Embebido

**Fecha:** 2026-06-04  
**Origen:** Solicitud de Nelson (Tony Stark) — investigar si CopilotKit sirve para el stack AlegentAI.  
**Decisión:** Reservar para futura app nativa con agente embebido. NO integrar en ForestAI / Expreso Bisonte / Orchestrator actual.

---

## Qué es CopilotKit

SDK open source (32K ⭐, MIT) para construir aplicaciones full-stack con agentes AI embebidos en el frontend.

**Autores:** CopilotKit Inc.  
**Protocolo:** AG-UI (Agent-User Interaction Protocol) — adoptado por Google, LangChain, AWS, Microsoft, Mastra, PydanticAI.  
**Licencia:** MIT  
**Repo:** https://github.com/CopilotKit/CopilotKit

---

## Stack Tecnológico

| Capa | Tecnología |
|------|------------|
| Frontend SDK | React / Next.js / Angular / Vue / React Native |
| Backend Runtime | Node.js (GraphQL) + Python SDK (LangGraph/LangChain/CrewAI) |
| Protocolo | AG-UI (event-based, SSE/WebSockets) |
| Infra | Self-hosted (tu servidor) o CopilotKit Cloud (SaaS) |

---

## Features Clave

1. **Chat UI nativa** — Componentes React listos para usar con streaming de mensajes, tool calls y agent responses.
2. **Generative UI** — El agente puede generar y actualizar componentes UI en runtime según intención del usuario.
3. **Backend Tool Rendering** — Herramientas del backend que retornan componentes UI renderizados directo en el cliente.
4. **Shared State** — Estado sincronizado bidireccional entre agente y UI en tiempo real (`useAgent` hook).
5. **Human-in-the-Loop** — El agente pausa ejecución para pedir input, confirmación o ediciones antes de continuar.
6. **AG-UI Protocol** — ~16 tipos de eventos estandarizados para interoperabilidad entre agentes y apps.

---

## Análisis de Integración vs Stack Nelson

### Meta-Orchestrator Actual
- Task Memory (SQLite + REST), Agent Router, Gates, Handoffs, Timeline, WS events.
- **Veredicto:** NO reemplazar. El orchestrator es un sistema de control, no un copilot embebido en una app. CopilotKit no cubre routing, gates de calidad ni task graph.

### ForestAI (FastAPI + React + PostgreSQL/PostGIS)
- **Veredicto:** NO integrar ahora. Añadiría una capa GraphQL/runtime entre FastAPI y React, duplicando backend. Overkill para un PoC de inventario forestal.

### Expreso Bisonte (Pipeline Excel 1:1)
- **Veredicto:** NO. Human-in-the-loop sería útil para aprobación de pares CDO/PF, pero un pipeline ETL batch no necesita chat UI ni generative UI.

### Futura App: "App con Agente Embebido"
- **Veredicto:** ✅ CASO IDEAL. Si Nelson decide construir una app donde el usuario interactúa con un agente como parte nativa de la experiencia (ej: app de campo para técnicos forestales, app de logística con asistente operativo), CopilotKit es de lo mejor del mercado.

---

## Requisitos para Futuro Uso

- **Frontend:** React 18+ con TypeScript (ya está en el stack Nelson).
- **Backend:** Python FastAPI con LangGraph (CopilotKit Python SDK soporta esto).
- **Self-hosting:** El runtime GraphQL se puede correr localmente o en Docker (sin depender de su cloud).
- **Conectividad:** AG-UI funciona sobre SSE/WebSockets, compatible con la infra de eventos que ya maneja el orchestrator.

---

## Notas Técnicas Relevantes

- Python SDK requiere: `langgraph>=0.3.25`, `fastapi>=0.115.0`, `ag-ui-langgraph`.
- No hay lock-in de cloud: el `CopilotRuntimeClient` se configura con `url` propia (ej: `http://localhost:3000` o tunnel).
- La versión runtime frontend/backend se chequea automáticamente para evitar incompatibilidades.

---

## Próximos Pasos (Sujetos a Prioridad de Nelson)

1. Spike de 2-4 horas: crear una mini app React + FastAPI + LangGraph con CopilotKit.
2. Evaluar latencia de streaming en Tailscale/tunnel.
3. Validar si `useAgent` + shared state pueden conectarse a nuestro task-memory existente.
4. Si pasa validación, armar SDD para nueva app de campo/logística con agente embebido.

---

## Criterios de Activación (¿Cuándo revisitar este doc?)

- Nelson dice: "quiero hacer una app donde el usuario chatee con un agente como si fuera parte de la UI".
- Se necesita Generative UI (el agente dibuja/modifica componentes en pantalla).
- Se necesita Human-in-the-Loop nativo sin reinventar la rueda.
- Se descarta usar el meta-orchestrator para una nueva línea de producto orientada a usuarios finales.
