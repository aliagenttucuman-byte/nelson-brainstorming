# MATRIZ DE EVALUACIÓN — Unsloth vs Oumi

## Escala
- 1 = muy débil
- 3 = aceptable
- 5 = muy fuerte

## Criterios
1. Time-to-first-result
2. Facilidad de setup
3. Integración con pipeline Alegent ForestAI
4. Repetibilidad de experimentos
5. Evaluación y trazabilidad
6. Mantenibilidad operativa

## Plantilla de scoring
| Criterio | Peso (%) | Unsloth | Oumi | Notas |
|---|---:|---:|---:|---|
| Time-to-first-result | 25 |  |  |  |
| Facilidad de setup | 15 |  |  |  |
| Integración pipeline actual | 20 |  |  |  |
| Repetibilidad | 15 |  |  |  |
| Evaluación y trazabilidad | 15 |  |  |  |
| Mantenibilidad | 10 |  |  |  |
| **Total ponderado** | **100** |  |  |  |

## Regla de decisión
- Si uno supera al otro por >=15% en total ponderado: recomendación directa.
- Si la diferencia <15%: recomendar estrategia híbrida por etapas.

## Salidas posibles
- GO Unsloth (iteración rápida dominante)
- GO Oumi (gobernanza/pipeline dominante)
- GO Híbrido (Unsloth para R&D + Oumi para estandarización)