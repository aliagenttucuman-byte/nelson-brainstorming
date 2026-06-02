# PIPELINE — DeepForest + SAM3 + NetFlora (Mini PoC)

## Objetivo técnico
Procesar imágenes de drone con pipeline híbrido para devolver 2 KPIs robustos y visuales explicativos.

## Flujo E2E
1. Ingesta de imágenes (lote PoC).
2. Preprocesamiento mínimo (normalización/tamaño).
3. Detección de árboles (DeepForest).
4. Refinamiento de copas (SAM3).
5. (Opcional) Clasificación demostrativa (NetFlora subset).
6. Postproceso de métricas.
7. Export de resultados + overlays.

## Output por imagen/lote
- `tree_count`: conteo estimado de árboles.
- `canopy_coverage_pct`: porcentaje de cobertura de copa.
- `overlay_image`: imagen anotada para validación visual.

## Definiciones de cálculo
- Conteo de árboles: número de instancias detectadas luego de NMS/filtro de confianza.
- Cobertura de copa: área total de máscaras de copa / área total de imagen * 100.

## Estructura de datos sugerida
```json
{
  "image_id": "lote_01_img_003",
  "tree_count": 147,
  "canopy_coverage_pct": 63.4,
  "processing_time_s": 11.2,
  "model_versions": {
    "deepforest": "x.y",
    "sam3": "x.y",
    "netflora": "subset-demo"
  }
}
```

## Reglas de calidad para mini PoC
- Umbral de confianza fijo por corrida para comparabilidad.
- No cambiar parámetros entre imágenes salvo justificación documentada.
- Guardar run metadata para trazabilidad.

## Criterio de robustez mínima
- El pipeline debe correr extremo a extremo sin intervención manual por imagen.
- Los 2 KPIs deben poder recalcularse de forma reproducible.

## Nota de Caja Negra
Se comparte metodología de alto nivel + resultados + evidencia visual.
No se comparte implementación interna detallada ni componentes propietarios.