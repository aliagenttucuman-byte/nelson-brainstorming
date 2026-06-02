# FRONTEND — Demo Web Mini PoC

## Objetivo UX
Mostrar resultados de forma simple, visual y comercialmente contundente.
Sin complejidad técnica expuesta al cliente.

## Pantallas mínimas

### 1) Home / Carga
- Subida de lote de imágenes.
- Estado de procesamiento (pendiente/en curso/listo).

### 2) Resultados
- Tabla corta por imagen/lote:
  - ID imagen/lote
  - Conteo de árboles
  - % cobertura de copa
- Botón para ver imagen anotada.

### 3) Evidencia visual
- Galería de 3 casos seleccionados:
  - original
  - overlay de detección/segmentación

## Diseño
- Limpio, ejecutivo, mobile-friendly.
- Foco en lectura rápida para tomadores de decisión.

## Componentes sugeridos
- `UploadPanel`
- `RunStatusCard`
- `KpiTable`
- `OverlayGallery`
- `ExecutiveSummaryCard`

## API mínima sugerida
- `POST /api/poc/upload`
- `POST /api/poc/run`
- `GET /api/poc/results/{run_id}`
- `GET /api/poc/overlay/{image_id}`

## Mensaje de valor en UI
- "Resultados orientados a decisión: conteo y cobertura en minutos."

## No incluir en esta fase
- Configuradores avanzados de modelos.
- Parámetros técnicos de tuning visibles al cliente.
- Features de producción (roles, multi-tenant, etc.).