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

- `matriz-reglas-transformacion-v1.md`
  - Matriz campo a campo: DIRECTO / MANUAL / NO EXPUESTO
  - Match por `nro` y brechas por universo

- `alineacion-pablo-reglas-operativas-v1.md`
  - Traducción de la explicación funcional de Pablo a reglas técnicas
  - Estado de match (implementado/parcial) y próximos cierres v1.1

- `verificacion-e2e-v1-2026-06-03.md`
  - Prueba end-to-end documentada (clear/upload/run/download)
  - Resultado validado contra filas de hojas trabajadas de referencia

- `estrategia-evangelizacion-automatizacion-operativa-v1.md`
  - Diagnóstico ejecutivo del caso (conocimiento tácito + carga operativa)
  - Propuesta en 3 carriles: quick wins, captura de reglas, adopción por niveles
  - Plan de 2 semanas para conversación con Pablo

- `briefing-reunion-pablo-hoy.md`
  - Guion ejecutivo de 30 minutos para conversación de hoy
  - Mensajes clave, decisiones a aprobar y KPIs de seguimiento

- `agenda-y-preguntas-reunion-pablo.md`
  - Agenda minuto a minuto + preguntas críticas de cierre
  - Estructura para alinear ownership, reglas PF y fuentes externas

- `minuta-decisiones-reunion-pablo-template.md`
  - Plantilla lista para completar durante/pos reunión
  - Tabla de decisiones, riesgos, backlog v1.1 y próximos checkpoints

- `comparativa-pipeline-vs-manual-v1.md`
  - Comparación de las 2 salidas del pipeline vs las 2 salidas trabajadas a mano
  - Resultado: coincidencia 1:1 con normalización de formato

## Hallazgos clave (para reunión)

1. CDO trabajada agrega campos operativos: `JUSTIFICACIÓN`, `REFERENTE`, `OBSERVACIÓN`.
2. PF trabajada agrega campos operativos: `JUSTIFICACIÓN`, `REFERENTE`, `OBS CTAS CTES`.
3. PF trabajada no se comporta como transformación 1:1 de PF sistema (requiere definición funcional explícita de alcance/fuentes).

## Siguiente decisión requerida
Aprobar versión v1.1 del diccionario con:
- owner por campo,
- regla de negocio por campo agregado,
- criterio formal de ingreso/egreso para PF trabajada.
