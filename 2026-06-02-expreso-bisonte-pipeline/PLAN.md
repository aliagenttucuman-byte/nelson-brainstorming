# PLAN — Expreso Bisonte Pipeline

## Fase 0 — Base actual (completada)
- Wizard no técnico implementado.
- Plan automático desde prompt.
- Ejecución de merge/concat + prompt LLM.
- Procedimientos persistidos.

## Fase 1 — Hardening (corto plazo)
Objetivo: cero pantallas blancas y errores comprensibles.

Tareas:
1. Estandarizar manejo de errores backend (400 funcional, evitar 500 inesperado).
2. Mostrar errores amigables en frontend sin romper render.
3. Registrar errores críticos para diagnóstico rápido.
4. Pruebas E2E con datasets mixtos (columnas incompatibles, schemas rotos).

## Fase 2 — Productividad de usuario
Objetivo: repetir procesos con menos fricción.

Tareas:
1. Guardado manual opcional de prompt (además del auto-guardado por ejecución exitosa).
2. Etiquetas/categorías de procedimientos.
3. Filtro/buscador de biblioteca.
4. Historial básico de ejecuciones recientes.

## Fase 3 — Escalado comercial
Objetivo: habilitar demos y adopción con mayor volumen.

Tareas:
1. Plantillas de objetivos por industria (logística, retail, operaciones).
2. Exportes adicionales (CSV + XLSX + resumen JSON).
3. Métricas de uso y éxito de procedimientos.
4. Guía de onboarding para usuarios nuevos.

## Entregables de control
- Checkpoint semanal técnico.
- Demo funcional por fase.
- Lista de bloqueos y decisiones abiertas.
