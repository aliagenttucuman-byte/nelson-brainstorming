# Expreso Bisonte Dinamic Analyzer PoC — Brainstorming Pipeline

## Resumen Ejecutivo
Este brainstorming consolida el pipeline funcional y la hoja de ruta de **Expreso Bisonte Dinamic Analyzer PoC**, basado en lo ya construido:
- carga multiarchivo Excel/CSV,
- objetivo en lenguaje natural,
- plan sugerido automático,
- ejecución de cruces y transformaciones,
- y reutilización de procedimientos guardados.

El enfoque sigue siendo no técnico para usuario final: pedir resultado en lenguaje natural y obtener salida accionable.

## Objetivo
Formalizar un plan de evolución del pipeline actual para pasar de PoC funcional a operación repetible de negocio.

## Estado actual (base validada)
- Wizard: Objetivo → Plan sugerido → Resultado.
- Endpoint de sugerencia: `POST /api/v1/excel/plan/suggest`.
- Ejecución: `POST /api/v1/excel/merge`.
- Biblioteca de procedimientos (guardar, usar, ordenar, duplicar, eliminar).
- Gestión de archivos (cargar, listar, borrar, vaciar, samples).
- Frontend responsive mobile.

## Objetivos próximos
1. Estabilidad de ejecución (errores controlados + mensajes claros).
2. Confianza operativa (procedimientos reutilizables y consistentes).
3. Escalabilidad funcional (más casos de negocio sin romper UX simple).

## Documentos de esta carpeta
- `PLAN.md` — plan operativo por fases.
- `PIPELINE.md` — flujo técnico E2E backend + frontend.
- `FRONTEND.md` — diseño UX no técnico y roadmap UI.
- `BACKEND-API.md` — contratos API y reglas de robustez.
- `PROCEDURES.md` — estrategia de biblioteca de prompts/procedimientos.
- `PITCH-INTERNO.md` — guion interno para presentar avance y próximos pasos.
- `SDD-FRONTEND-LIB-POLICY.md` — encastre SDD de librerías frontend y skills obligatorias.

## Criterio de éxito
- Usuario no técnico obtiene resultado útil en menos de 5 minutos.
- Errores complejos no rompen pantalla ni flujo.
- Procedimientos guardados reducen tiempo de ejecución repetida.
