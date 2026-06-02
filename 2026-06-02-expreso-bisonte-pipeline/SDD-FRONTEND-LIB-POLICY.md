# SDD — Frontend Library Policy (Expreso Bisonte)

## Objetivo
Definir dónde y cómo se deciden las librerías frontend dentro del flujo SDD, evitando decisiones ad-hoc.

## Librerías propuestas (baseline)
- zod → validaciones de esquema.
- date-fns → operaciones de fecha/hora.
- tanstack-table → tablas complejas y configurables.
- better-auth → autenticación/autorización (si aplica).
- motion → animaciones de interfaz.
- fontsource → tipografías embebidas y controladas.
- chart.js → visualizaciones (permitido por proyecto).
- zustand → estado global liviano.
- pragmatic-drag-and-drop → interacciones drag-and-drop robustas.
- hotkeys-js → atajos de teclado para productividad.

## Mapeo SDD (obligatorio)

### Fase 1 — CONSTITUCIÓN
- Definir librerías permitidas y estándar del proyecto.
- Dejar explícitas excepciones (ej: Chart.js en lugar de Recharts).

### Fase 2 — SPECIFY
- Cada librería debe estar justificada por user stories/criterios.
- Ejemplo: "reordenar filas" justifica drag-and-drop.

### Fase 4 — PLAN
- Documentar decisión final por librería.
- Registrar alternativa descartada y riesgo técnico.

### Fase 7 — CHECKLIST
- Validar: accesibilidad, performance, testing, mantenimiento.
- Confirmar que no haya solapamiento de responsabilidades.

## Skills involucradas
- `nelson-spec-driven-workflow`
- `spec-driven-development`
- `nelson-frontend-stack`
- `nelson-frontend-testing`
- `nelson-code-quality`
- `requesting-code-review`
- `nelson-react-doctor`

## Regla de gobernanza
No se incorpora ninguna librería al código productivo sin trazabilidad SDD: necesidad funcional + decisión de arquitectura + criterios de aceptación.
