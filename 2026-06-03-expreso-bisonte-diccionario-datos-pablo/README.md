# Expreso Bisonte — Diccionario de datos para revisión con Pablo

Fecha: 2026-06-03
Estado: listo para revisión ejecutiva/funcional

## Objetivo
Consolidar y documentar la ingeniería inversa de campos del archivo real `CONTADO_PABLO_RUIZ.xlsx`, dejando una base auditable para alinear definiciones de negocio y reglas del pipeline estático.

## Entregables de esta carpeta

- `diccionario-datos-v1-contado-pablo-ruiz.md`
  - Diccionario completo por hoja (tipos inferidos, % no nulo, ejemplos)
  - Trazabilidad sistema -> trabajada (CDO/PF)
  - Definiciones funcionales v1 y próximos pasos

- `resumen-ejecutivo-pablo-diccionario.md`
  - Versión breve orientada a presentación
  - Hallazgos clave para conversación ejecutiva

- `ingenieria-inversa-campos-contado-pablo-ruiz.md`
  - Relevamiento técnico de diferencias de campos
  - Identificación de campos agregados operativos

## Hallazgos clave (para reunión)

1. CDO trabajada agrega campos operativos: `JUSTIFICACIÓN`, `REFERENTE`, `OBSERVACIÓN`.
2. PF trabajada agrega campos operativos: `JUSTIFICACIÓN`, `REFERENTE`, `OBS CTAS CTES`.
3. PF trabajada no se comporta como transformación 1:1 de PF sistema (requiere definición funcional explícita de alcance/fuentes).

## Siguiente decisión requerida
Aprobar versión v1.1 del diccionario con:
- owner por campo,
- regla de negocio por campo agregado,
- criterio formal de ingreso/egreso para PF trabajada.
