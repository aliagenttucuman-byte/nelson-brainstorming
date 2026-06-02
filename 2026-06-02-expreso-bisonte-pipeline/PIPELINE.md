# PIPELINE — Expreso Bisonte Dinamic Analyzer PoC

## Flujo E2E
1. Usuario sube múltiples archivos (`upload-multiple`).
2. Usuario redacta objetivo en lenguaje natural.
3. Backend sugiere plan de cruces (`plan/suggest`).
4. Usuario ejecuta plan (`merge`) con reglas derivadas.
5. Backend aplica transformaciones + prompt LLM (si corresponde).
6. Se devuelve archivo resultado + preview de datos.
7. Se guarda/actualiza procedimiento para reutilización.

## Módulos principales

### Ingesta
- Soporta `.xlsx`, `.xls`, `.csv`.
- Registro temporal de archivos por `file_id`.
- Operaciones: listar, borrar, vaciar, cargar samples.

### Planificador
- Detecta claves comunes entre archivos.
- Sugiere merge o concat según compatibilidad y objetivo.
- Genera pasos explicables para UI wizard.

### Ejecutor
- Aplica reglas encadenadas (merge/concat).
- Valida claves de cruce antes de ejecutar.
- Devuelve errores funcionales (sin caída de app).

### Postproceso
- Exporta resultado a Excel.
- Devuelve preview/tabla para validación rápida.
- Mantiene output como archivo reutilizable.

### Memoria operativa (procedures)
- Guardado de prompt + modelo + resultado esperado.
- Reordenado por prioridad.
- Marcado de uso.
- Duplicación y eliminación.

## Principios del pipeline
- UX simple primero.
- Robustez > complejidad.
- Repetibilidad de procesos por biblioteca.
