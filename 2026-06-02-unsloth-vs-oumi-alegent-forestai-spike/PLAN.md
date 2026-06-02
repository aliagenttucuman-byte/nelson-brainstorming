# PLAN — Spike comparativo Unsloth vs Oumi (48h)

## Alcance
- Sin migración productiva.
- Solo prueba técnica controlada.
- Caso representativo de Alegent ForestAI (dataset chico y reproducible).

## Día 1
1. Setup entorno aislado para Unsloth.
2. Setup entorno aislado para Oumi.
3. Correr "time-to-first-result" en ambos.
4. Registrar fricciones (dependencias, GPU/CPU, formato de datos).

## Día 2
1. Repetir corrida con mismos datos y objetivo.
2. Medir estabilidad/repetibilidad.
3. Evaluar capacidad de evaluación/reporte.
4. Consolidar matriz de decisión y recomendación.

## Métricas mínimas a registrar
- Tiempo de setup inicial.
- Tiempo hasta primer output útil.
- Complejidad operativa (baja/media/alta).
- Facilidad de integración al stack actual.
- Claridad de evaluación y trazabilidad.

## Riesgos del spike
- Diferencias de entorno (CUDA/drivers).
- Dataset no representativo.
- Comparación sesgada por configuración desigual.

## Mitigaciones
- Mismo dataset y objetivo para ambos.
- Mismo hardware base para corridas.
- Registro detallado de pasos y parámetros.