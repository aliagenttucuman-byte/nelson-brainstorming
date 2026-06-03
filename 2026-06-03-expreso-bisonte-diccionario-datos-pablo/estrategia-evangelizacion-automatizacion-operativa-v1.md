# Expreso Bisonte — Estrategia de evangelización + automatización operativa (v1)

Fecha: 2026-06-03  
Audiencia: Nelson + Pablo  
Contexto: múltiples Excels operativos con conocimiento crítico distribuido y parcialmente tácito.

## 1) Diagnóstico ejecutivo

### Situación actual
- El proceso operativo de Excel consume mucho tiempo en tareas manuales repetitivas.
- Parte del significado de los campos está explícito en archivos/diccionarios; otra parte está en conocimiento tácito de quien opera (principalmente gerencia operativa).
- Existen datos que no nacen en los Excel base y se completan desde otras fuentes o criterio experto.
- Riesgo de dependencia de persona clave: si la operadora no está, baja la continuidad y trazabilidad.

### Oportunidad
- Caso ideal para automatización incremental: primero eficiencia operativa, luego inteligencia.
- Alto potencial de reducción de tiempo y estandarización con bajo riesgo si arrancamos con reglas determinísticas.

## 2) Propuesta de enfoque (3 carriles en paralelo)

## Carril A — Estabilización operativa inmediata (quick wins)
Objetivo: bajar horas manuales rápido sin discutir IA.

- Mantener contrato estricto 1:1:
  - Entrada: `CDO Sistema` + `PTE de Fact Sistema`
  - Salida: `CDO Trabajada` + `PF Trabajada`
- Reglas determinísticas en Python/pandas (sin modelos).
- Trazabilidad completa de archivo origen, regla aplicada y resultado.
- KPI base: tiempo manual actual vs tiempo post-pipeline.

Resultado esperado: reducción tangible de carga operativa desde la primera iteración.

## Carril B — Captura de conocimiento tácito (sin frenar operación)
Objetivo: transformar “lo tengo en la cabeza” en reglas auditables.

- Sesiones de shadowing cortas (30–45 min) sobre casos reales.
- Por cada campo agregado/manual, documentar:
  1) definición funcional,
  2) owner,
  3) fuente de dato,
  4) regla de negocio,
  5) excepciones.
- Construir artefactos vivos:
  - `diccionario de campos v1.1`
  - `matriz de reglas operativas v1.1`
  - `catálogo de excepciones frecuentes`

Resultado esperado: menor dependencia personal + mayor continuidad del proceso.

## Carril C — Adopción y evangelización por niveles
Objetivo: asegurar adopción real sin resistencia del área operativa.

Nivel 0: Automatizar lo repetitivo (sin cambiar criterio humano).  
Nivel 1: Validaciones automáticas y alertas de inconsistencias.  
Nivel 2: Sugerencias inteligentes (IA) sobre casos ambiguos.

Mensaje clave de cambio:
- La gerencia operativa no pierde control.
- Pasa de ejecutora manual a dueña de reglas de negocio del sistema.

## 3) Riesgos y mitigaciones

1. Riesgo: documentación incompleta de reglas PF.
   - Mitigación: cerrar owner/fuente/criterio de inclusión por campo antes de escalar automatización.

2. Riesgo: rechazo por “automatización = reemplazo”.
   - Mitigación: framing explícito de co-pilot operativo + ownership funcional en negocio.

3. Riesgo: mezclar mejoras de proceso con IA demasiado temprano.
   - Mitigación: secuencia obligatoria Quick win determinístico -> validaciones -> IA.

## 4) Plan sugerido de 2 semanas (para conversar con Pablo)

Semana 1
- Día 1-2: baseline de proceso actual (tiempos, errores, retrabajos).
- Día 2-3: talleres cortos de captura de reglas con operadora clave.
- Día 4-5: diccionario v1.1 + matriz de reglas v1.1 + backlog técnico priorizado.

Semana 2
- Día 1-3: implementación de reglas faltantes de alto impacto.
- Día 3-4: validación end-to-end con lotes reales y KPIs.
- Día 5: demo ejecutiva con Pablo + plan de fase siguiente.

## 5) Qué le mostramos a Pablo (formato reunión)

1. Estado actual en una slide:
   - tiempo operativo manual,
   - puntos de dolor,
   - riesgo por conocimiento tácito.

2. Qué ya está resuelto:
   - pipeline 1:1,
   - trazabilidad,
   - outputs separados y controlados.

3. Qué falta para robustez total:
   - definiciones finales de campos agregados,
   - fuentes externas por campo,
   - reglas PF de inclusión/exclusión.

4. Roadmap de adopción:
   - fase 1 (determinístico),
   - fase 2 (alertas),
   - fase 3 (IA asistiva).

5. KPI de éxito propuesto:
   - reducción de tiempo operativo,
   - reducción de retrabajo,
   - porcentaje de campos con owner y regla formalizada,
   - continuidad operativa sin dependencia de una sola persona.

## 6) Decisión solicitada en reunión

Aprobar formalmente:
1) esquema de captura de conocimiento tácito (sesiones + owners),  
2) backlog de automatización v1.1,  
3) criterio de avance por fases (sin saltear directo a IA).

---

Documento pensado para conversación ejecutiva con Pablo, orientado a adopción, reducción de riesgo operativo y escalamiento gradual del flujo Excel de Expreso Bisonte.
