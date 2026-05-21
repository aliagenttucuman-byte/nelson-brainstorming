# Fleet Optimizer - Brainstorming 2026-05-13

**Proyecto:** Plataforma de gestión de flotas con IA para transporte
**Brainstorming:** Tony Stark + JARVIS
**Fecha:** 2026-05-13

## Resumen Ejecutivo

Sistema integral para empresas de transporte que permite:
- Tracking GPS en tiempo real
- Cálculo automático de costos por vehículo/flota
- Métricas financieras (VAN, TIR, Payback)
- Asistente de IA conversacional

## Diferenciador Único
Ningún competidor (Samsara, Geotab, Wialon) ofrece VAN/TIR + asistente IA en español.

## Documentos

| Archivo | Descripción |
|---------|-------------|
| `SDD.md` | Software Design Document completo (Domain Model, OpenAPI, Arquitectura) |

## Decisiones Clave

1. Stack: FastAPI + PostgreSQL + React 19 + Qdrant + Ollama
2. Modelo SaaS: 3 planes desde $50K ARS/mes
3. Roadmap: 6 fases, ~17 semanas

## Próximos Pasos

- [ ] Validar SDD con Pablo (COO)
- [ ] Revisión financiera con Luigi
- [ ] Asignar agentes: Beto (spec), Ricky (backend), Nico (frontend), Diego (infra)
- [ ] Definir flota piloto de prueba

---
**Estado:** 🟡 En definición
**Siguiente acción:** Reunión de validación
