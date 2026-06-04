# Evaluación Ejecutiva: MLflow

**Repo:** https://github.com/mlflow/mlflow  
**Stars:** 26.3k | **Forks:** 5.8k | **Issues abiertos:** 2.1k  
**Licencia:** Apache 2.0 | **Versión actual:** v3.13.0 (jun 2026)  
**Lenguaje principal:** Python

---

## ¿Qué es?

Plataforma open-source de MLOps/LLMOps que cubre todo el ciclo de vida:
- **Tracing / Observability** de LLMs y agents (basado en OpenTelemetry)
- **Evaluación** de LLMs (50+ métricas + LLM judges)
- **Prompt Registry** con versionado y optimización automática
- **AI Gateway** (API unificada para proveedores LLM, rate limits, fallbacks)
- **Experiment Tracking + Model Registry** para ML tradicional
- Integraciones con 60+ frameworks (LangChain, DSPy, CrewAI, PydanticAI, etc.)

---

## Veredicto: NO por ahora. Overkill para nuestra etapa.

### Por qué NO hoy:

1. **Peso de infraestructura:** requiere servidor MLflow + UI + backend de tracking. Para PoCs es otra pieza Docker que mantener.
2. **Duplicación:** ya tenemos `nelson-eval-harness` para evaluación y `nelson-monitoring-observability` para logging mínimo en PoCs.
3. **No usamos sus integraciones:** no estamos con LangChain, DSPy, ni frameworks de agents pesados. MLflow brilla instrumentando esos stacks.
4. **Gateway propio:** ya tenemos patrón `nelson-api-gateway-pattern` con nginx reverso. El AI Gateway de MLflow sería redundante.
5. **Prompt registry:** hoy versionamos prompts en código + git. El registry de MLflow suma valor cuando hay 10+ prompts en producción con A/B testing.

### Cuándo SÍ:

- Tengamos 3+ modelos LLM en producción simultáneos
- Necesitemos tracing distribuido cross-service (OpenTelemetry nativo)
- Hagamos A/B testing de prompts a escala
- Stakeholder pida "dashboard de evaluación" visible sin código

### Piezas sueltas que podrían reutilizarse sin todo MLflow:

- `mlflow.evaluate()` para benchmarks rápidos de LLM judges
- `mlflow.tracing` para tracear llamadas OpenAI si el meta-orchestrator crece mucho

---

## Conclusión

**Radar: SÍ. Integrar hoy: NO.**  
Es la plataforma MLOps más madura del mercado (60M descargas/mes), pero para el equipo I+D+I de AlegentAI en etapa de PoCs, agrega más peso que valor. Re-evaluar en 6-12 meses si escala a producción real con múltiples modelos.
