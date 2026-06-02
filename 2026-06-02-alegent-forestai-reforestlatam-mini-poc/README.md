# Alegent ForestAI × ReforestLatam — Mini PoC (Caja Negra)

## Resumen Ejecutivo
Este brainstorming define una mini PoC comercial y técnica para demostrar el potencial de Alegent ForestAI con imágenes reales de drone de ReforestLatam.

La estrategia es deliberadamente chica pero contundente: entregar valor visible en pocos días con solo **2 KPIs** y evidencia visual clara, sin exponer propiedad intelectual del pipeline (caja negra).

La mini PoC apunta a habilitar una alianza comercial seria en fases:
1. NDA + acuerdo de piloto corto.
2. Mini PoC técnica con alcance acotado.
3. Revisión ejecutiva de resultados.
4. Si hay fit: propuesta de fase ampliada + acuerdo marco.

## Objetivo del piloto
Demostrar en 5-7 días que el pipeline de Alegent ForestAI puede procesar imágenes de ReforestLatam y devolver métricas accionables con trazabilidad visual.

## KPIs del piloto (solo 2)
1. **Conteo estimado de árboles** por imagen/lote.
2. **% de cobertura de copa** por imagen/lote.

## Entregables
- 1 reporte ejecutivo (1 página).
- 1 tabla de resultados por imagen/lote con 2 KPIs.
- 3 imágenes anotadas (antes/después con overlays).
- Demo web simple de resultados.

## Alcance técnico resumido
Pipeline base: **DeepForest + SAM3 + NetFlora (subset opcional de demostración)**.

- DeepForest: detección inicial de árboles.
- SAM3: segmentación/refinamiento de copa.
- NetFlora: capa opcional para mostrar potencial de clasificación (sin comprometer exactitud productiva en esta fase).

## Documentos de esta carpeta
- `PLAN.md` → plan operativo de ejecución.
- `PIPELINE.md` → arquitectura técnica y flujo E2E.
- `FRONTEND.md` → UX/UI de demo web.
- `LEGAL-COMERCIAL.md` → marco de caja negra y acuerdo inicial.
- `PITCH-REUNION.md` → guion corto para reunión con ReforestLatam.

## Criterio de éxito
La mini PoC se considera exitosa si ReforestLatam valida:
- que los resultados son entendibles y útiles,
- que el tiempo de respuesta es razonable,
- y que existe potencial comercial para escalar a fase 2.