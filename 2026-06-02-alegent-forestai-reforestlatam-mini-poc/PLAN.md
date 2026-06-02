# PLAN — Mini PoC Alegent ForestAI × ReforestLatam

## 1) Alcance cerrado
- Dataset inicial: 10-20 imágenes drone (o 1-2 ortofotos chicas).
- Duración: 5-7 días corridos.
- KPIs: solo 2 (conteo de árboles + % cobertura).
- Sin integración compleja de sistemas de terceros en esta fase.

## 2) Cronograma sugerido

### Día 1 — Kickoff + Data Intake
- Validar formato, resolución y consistencia de imágenes.
- Seleccionar muestra representativa (sin outliers extremos).
- Congelar dataset de PoC v1.

### Día 2 — Setup pipeline mínimo
- Preparar ejecución DeepForest y SAM3 sobre muestra.
- Definir parámetros base (thresholds) para consistencia.

### Día 3 — Corrida técnica y QA
- Ejecutar pipeline completo en muestra v1.
- QA visual de detecciones/segmentaciones.
- Ajustes menores de umbrales.

### Día 4 — Métricas y visuales
- Calcular 2 KPIs por imagen/lote.
- Generar overlays antes/después.
- Consolidar tabla final.

### Día 5 — Entrega ejecutiva
- Armar reporte de 1 página.
- Demo web simple.
- Reunión de lectura de resultados y propuesta Fase 2.

## 3) Roles
- Alegent ForestAI:
  - Técnico: armado pipeline, ejecución, QA, reporte.
  - Comercial: marco de alianza y siguientes pasos.
- ReforestLatam:
  - Proveer imágenes + contexto básico de lotes.
  - Validar utilidad de resultados.

## 4) Riesgos controlados
- Calidad de imagen heterogénea.
- Cobertura incompleta de metadata de vuelo.
- Expectativas de clasificación avanzada demasiado tempranas.

Mitigación: mantener objetivo de prueba de potencial, no certificación final.

## 5) Cierre del piloto
Checklist de cierre:
- [ ] 2 KPIs calculados y trazables.
- [ ] 3 ejemplos visuales claros.
- [ ] reporte ejecutivo 1 página.
- [ ] decisión Go/No-Go para Fase 2.