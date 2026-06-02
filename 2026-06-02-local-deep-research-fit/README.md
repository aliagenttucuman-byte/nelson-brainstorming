# Local Deep Research Fit — Spike para JARVIS + Meta-Orchestrator

## Resumen Ejecutivo
Este brainstorming propone evaluar `LearningCircuit/local-deep-research` como motor de investigación profunda para el stack de Nelson.

Foco principal: usarlo como copiloto de research para JARVIS (mercado, benchmarking, análisis comercial/legal/técnico), NO como reemplazo del pipeline de visión de Alegent ForestAI.

Repo evaluado:
- https://github.com/LearningCircuit/local-deep-research

## Hipótesis (formato Nelson)
CREEMOS QUE integrar Local Deep Research como capa de investigación para JARVIS,
RESULTARÁ en mejor velocidad y calidad de análisis comparativo/multifuente con control local de datos,
CRITERIOS DE ACEPTACION:
- respuestas útiles y citables para casos reales de negocio,
- integración operable con flujo actual del meta-orchestrator,
- costo/operación razonable en entorno actual.

## Dónde nos sirve (JARVIS)
- Research de mercado y competencia.
- Due diligence rápida de tecnologías/repos/startups.
- Síntesis multi-fuente para decisiones de producto.
- Insumos para docs ejecutivos (brainstorming, business plan, benchmark).

## Dónde NO aplica como core
- Entrenamiento principal de modelos de visión ForestAI.
- Reemplazo directo de pipeline DeepForest + SAM3 + NetFlora.

## Documentos de esta carpeta
- `PLAN.md`
- `CASOS-DE-PRUEBA.md`
- `DECISION.md`

## Criterio de éxito
Definir en 24-48h si conviene adopción:
- GO research-assistant,
- NO-GO,
- o uso acotado por tipo de tarea.