# PLAN — Spike Local Deep Research (24-48h)

## Alcance
- Validación funcional para tareas de investigación.
- Sin tocar pipelines productivos de ForestAI.

## Día 1
1. Setup local mínimo.
2. Prueba con 3 consultas reales del contexto Nelson:
   - benchmark de herramientas,
   - análisis de alianza/comercial,
   - scouting técnico de repos.
3. Revisar calidad de fuentes y citabilidad.

## Día 2
1. Medir utilidad práctica en flujo JARVIS.
2. Evaluar posible integración al meta-orchestrator (como módulo de research).
3. Definir recomendación final.

## Métricas de evaluación
- Tiempo a primer reporte útil.
- Calidad de síntesis (precisión + estructura).
- Trazabilidad de fuentes.
- Fricción operativa (setup/uso diario).

## Riesgos
- Sobrecarga operativa si setup es pesado.
- Ruido en resultados si prompts no están bien diseñados.

## Mitigación
- Casos de prueba bien definidos.
- Prompt templates cortos y comparables.