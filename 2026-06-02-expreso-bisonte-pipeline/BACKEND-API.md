# BACKEND-API — Contratos y robustez

## Endpoints núcleo
- `POST /api/v1/excel/upload`
- `POST /api/v1/excel/upload-multiple`
- `GET /api/v1/excel/files`
- `DELETE /api/v1/excel/files/{file_id}`
- `POST /api/v1/excel/files/clear`
- `POST /api/v1/excel/load-samples`
- `POST /api/v1/excel/plan/suggest`
- `POST /api/v1/excel/merge`
- `GET /api/v1/excel/download/{file_id}`

## Endpoints procedures
- `GET /api/v1/excel/procedures`
- `POST /api/v1/excel/procedures`
- `POST /api/v1/excel/procedures/reorder`
- `POST /api/v1/excel/procedures/{procedure_id}/mark-used`
- `POST /api/v1/excel/procedures/{procedure_id}/duplicate`
- `DELETE /api/v1/excel/procedures/{procedure_id}`

## Reglas de robustez
1. No devolver 500 por validaciones de negocio esperables.
2. Responder 400 con detalle útil para usuario final.
3. Manejar columnas incompatibles con mensajes claros.
4. Mantener compatibilidad de contrato para frontend.

## Observabilidad mínima
- Log de request-id por ejecución.
- Log de errores con endpoint + payload resumido.
- Métricas básicas: tasa de éxito de merge, tiempo medio de ejecución.
