# Verificación E2E v1 — Pipeline estático (CONTADO_PABLO_RUIZ)

Fecha/hora: 2026-06-03T00:45:00

## Entorno validado
- Backend local: http://127.0.0.1:9000
- Frontend local: http://127.0.0.1:5173
- URL pública: https://memorial-involved-employees-binary.trycloudflare.com

## Archivo de prueba
- /home/server/.hermes/document_cache/doc_07f056549711_CONTADO_PABLO_RUIZ.xlsx

## Flujo ejecutado
1. POST /api/v1/excel/files/clear -> 200
2. POST /api/v1/excel/upload-multiple -> 200
3. POST /api/v1/excel/pipeline/static -> 200
4. GET /api/v1/excel/download/{file_id} -> 200

## Resultado del backend
- output_mode: reference_worked_sheet
- row_stats:
  - cdo_sistema_rows: 405
  - pf_sistema_rows: 1409
  - cdo_trabajada_rows: 402
  - pf_trabajada_rows: 1503
- stages:
  - CDO Sistema: 405
  - Filtro no-CC + bloqueo PF: 402
  - PF Sistema: 1409
  - PF Trabajada: 1503

## Validación del Excel descargado
Archivo descargado: /tmp/verificacion_pipeline_contado_pablo.xlsx

- Hoja `CDO Trabajada`: 402 filas
- Hoja `PF Trabajada`: 1503 filas

## Conclusión
La corrida E2E quedó consistente con las hojas trabajadas de referencia del archivo de Pablo.
