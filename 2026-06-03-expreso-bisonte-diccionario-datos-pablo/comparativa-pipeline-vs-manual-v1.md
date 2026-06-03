# Comparativa — salida pipeline vs salidas manuales (2 hojas)

Fecha: 2026-06-03

## Alcance
Comparación de las 2 salidas del pipeline contra las 2 salidas trabajadas a mano del archivo de referencia:
- Manual: `/home/server/.hermes/document_cache/doc_07f056549711_CONTADO_PABLO_RUIZ.xlsx`
- Pipeline CDO: `/tmp/eb_pipeline_cdo_trabajada.xlsx` (hoja `CDO Trabajada`)
- Pipeline PF: `/tmp/eb_pipeline_pf_trabajada.xlsx` (hoja `PF Trabajada`)

## Método de comparación
1. Normalización de lectura de hoja (detección de encabezado real cuando hay fila `Informe: ...`).
2. Normalización de valores para evitar falsos diferenciales de formato:
   - numéricos (`123` vs `123.0`)
   - fechas (`dd/mm/yyyy` vs timestamp)
   - blancos/NA
3. Comparación por:
   - filas totales
   - esquema (columnas)
   - set de tuplas completas por fila (todas las columnas comunes)
   - clave operativa `nro`

## Resultado CDO Trabajada
- Filas manual: **402**
- Filas pipeline: **402**
- Columnas comunes: **17/17**
- Tuplas coincidentes: **402**
- Solo manual: **0**
- Solo pipeline: **0**
- `nro` solo manual: **0**
- `nro` solo pipeline: **0**

## Resultado PF Trabajada
- Filas manual: **1503**
- Filas pipeline: **1503**
- Columnas comunes: **16/16**
- Tuplas coincidentes: **1503**
- Solo manual: **0**
- Solo pipeline: **0**
- `nro` solo manual: **0**
- `nro` solo pipeline: **0**

## Conclusión
La salida del pipeline **coincide 1:1** con las 2 salidas hechas a mano (CDO Trabajada y PF Trabajada), una vez aplicada normalización de formato de celdas.
