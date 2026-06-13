# Propuesta Comercial — Sistema Integral de Automatización Operativa
## Expreso Bisonte × AlegentAI

**Fecha:** Junio 2026
**Preparado por:** AlegentAI — Nelson Acosta (CTO) + Pablo Ruiz (COO)
**Para:** Gerencia Operativa — Expreso Bisonte
**Metodología:** PMI / ISO 21502 / Agile
**Horizonte:** 12 meses
**Referencia cambiaria:** USD 1 = ARS 1.460 (dólar blue junio 2026)

---

## 1. Resumen Ejecutivo

AlegentAI propone el desarrollo de un **Sistema Integral de Automatización Operativa** para Expreso Bisonte, empresa con **5 sucursales activas** — Tucumán (sede central), Buenos Aires, Salta, Jujuy y Rosario — operando rutas interprovinciales en todo el NOA y el corredor nacional. El sistema se estructura en tres líneas de trabajo paralelas que cubren los principales procesos críticos de la empresa:

- **Línea 1 — Automatización de Procesos Operativos:** ~15 procesos con input Excel, comenzando por el motor CDO/PF ya validado — abarca la operación completa de todas las sucursales
- **Línea 2 — Auditoría de Mantenimiento de Flota:** control preventivo y correctivo del parque vehicular, con **gestión centralizada multi-sucursal** — panel único para las 5 bases de operación
- **Línea 3 — Visualización en Tiempo Real de Flota:** monitoreo de camiones consumiendo datos del sistema Sitrack, con **tracking distribuido** para rutas interprovinciales entre las 5 sucursales

El sistema se puede contratar como **producto propio** (Bisonte dueño del código) o como **servicio SaaS** (AlegentAI opera y mantiene). El mantenimiento y la evolución continua de cada línea forman parte del acuerdo postventa.

**El primer proceso operativo ya existe y fue validado con datos reales de Bisonte.** El sistema procesó 1.905 filas con resultado 1:1 al proceso manual. Lo que se propone es escalar esa capacidad al resto de la operación.

---

## 2. Antecedentes y Punto de Partida

### PoC validada — Motor CDO/PF

| Concepto | Resultado |
|----------|-----------|
| Proceso automatizado | CDO Sistema + PTE Fact Sistema → CDO Trabajada + PF Trabajada |
| Filas procesadas | 1.905 filas |
| Precisión | 1:1 respecto al proceso manual de referencia |
| Tiempo de ejecución | Segundos vs horas manuales |
| Estado | PoC funcionando, accesible vía web |

> Este proceso, que fue el primero en ser automatizado, representa intencionalmente el caso de complejidad base. Los demás procesos operativos serán analizados en conjunto con la referente del negocio y presupuestados en función de esa complejidad real.

### Inversión ya realizada por AlegentAI

| Concepto | Horas | USD | ARS |
|----------|:-----:|:---:|:---:|
| PoC CDO/PF (análisis + desarrollo + deploy) | 560 hs | USD 16.800 | ARS 24.528.000 |

> Esta inversión no se traslada como costo a Bisonte. Es el respaldo que demuestra capacidad real de entrega.

---

## 3. Línea 1 — Automatización de Procesos Operativos

### Descripción

Automatización de los procesos operativos que hoy se realizan manualmente sobre archivos Excel. El objetivo es que cada proceso que actualmente requiere horas de trabajo humano se ejecute en segundos, con resultado auditado y trazable.

Se estima un total de **~15 procesos**, con un ritmo de entrega de **1 a 2 procesos por mes**, condicionado a la disponibilidad de la referente del negocio para los relevamientos. Cada proceso pasa por el mismo ciclo validado:

```
Relevamiento → Análisis → Desarrollo → Validación con datos reales → Producción
```

### Referente clave

La velocidad de entrega depende directamente de la disponibilidad de la **referente principal del negocio** — la persona que conoce las reglas operativas y puede validar los resultados. Sin esa interacción, AlegentAI no puede asumir el conocimiento tácito del negocio. Esto no es una limitación técnica: es el proceso correcto para garantizar que cada automatización sea fiel a la operación real.

### Disclaimer — Fuentes de datos

> ⚠️ **En esta primera instancia de automatización, todas las fuentes de datos deben estar disponibles en formato Excel accesible** para el sistema. Esto incluye:
>
> - Archivos descargados manualmente desde **Transoft** (sistema operativo de Bisonte)
> - Archivos descargados manualmente desde **Sitrack** (sistema de trackeo de flota)
> - Archivos generados internamente por el equipo operativo
>
> La integración directa con las APIs o bases de datos de Transoft y Sitrack **no está incluida en esta propuesta** y requeriría un análisis técnico y comercial separado con cada proveedor. Mientras no exista esa integración, el flujo de trabajo implica que el personal de Bisonte descarga los archivos de cada sistema y los sube al módulo de automatización.

### Estimación de alcance

| Bloque | Descripción | Procesos estimados | Meses |
|--------|-------------|:------------------:|:-----:|
| Base | Motor CDO/PF en producción + hardening | 1 (ya hecho) | 1-2 |
| Bloque A | Procesos de cobranzas y facturación | 3-4 | 3-5 |
| Bloque B | Procesos de seguimiento de envíos y logística | 3-4 | 5-8 |
| Bloque C | Procesos de análisis operativo y reportería | 3-4 | 8-11 |
| Bloque D | Motor genérico + integración cruzada | 2-3 | 11-12 |
| **Total** | | **~15 procesos** | **12 meses** |

> Los procesos concretos de cada bloque se definen en el relevamiento con la referente del negocio. Esta tabla refleja la capacidad de entrega estimada, no un listado de procesos comprometidos que aún no fueron analizados.

### Inversión Línea 1

| Concepto | Horas | USD | ARS |
|----------|:-----:|:---:|:---:|
| Producción motor CDO/PF + deploy en infra Bisonte | 200 hs | USD 6.000 | ARS 8.760.000 |
| ~14 procesos adicionales (promedio 50 hs/proceso) | 700 hs | USD 21.000 | ARS 30.660.000 |
| Infra, testing, documentación, capacitación | 100 hs | USD 3.000 | ARS 4.380.000 |
| **Total Línea 1** | **1.000 hs** | **USD 30.000** | **ARS 43.800.000** |

---

## 4. Línea 2 — Auditoría de Mantenimiento de Flota

### Descripción

Sistema de control y auditoría del mantenimiento preventivo y correctivo del parque vehicular de Expreso Bisonte, con **gestión centralizada para las 5 sucursales** (Tucumán, Buenos Aires, Salta, Jujuy y Rosario). El objetivo es eliminar la dependencia del conocimiento individual sobre el estado de cada unidad, dar visibilidad total al estado de mantenimiento de la flota y unificar el control desde un único panel sin importar en qué base opera cada unidad.

Hoy, sin este sistema, cada sucursal gestiona su mantenimiento en forma aislada — sin visibilidad cruzada, sin alertas unificadas y sin capacidad de comparar costos entre sedes.

### Funcionalidades base (sujetas a relevamiento)

| Módulo | Descripción |
|--------|-------------|
| Registro de flota | Ficha digital de cada unidad: patente, modelo, año, km actuales, estado operativo |
| Plan de mantenimiento preventivo | Calendario por unidad: services, revisiones, vencimientos de habilitación |
| Alertas automáticas | Notificación anticipada por km o por fecha antes del vencimiento |
| Historial de intervenciones | Registro completo de reparaciones: fecha, taller, costo, repuestos, responsable |
| Auditoría de costos | Gasto por unidad, por período, por tipo de intervención — identificación de unidades de alto costo |
| Indicadores gerenciales | Estado general de la flota en un vistazo: unidades operativas, en mantenimiento, vencidas |
| Exportación | Reportes en Excel o PDF por demanda |
| Vista centralizada multi-sucursal | Panel único con estado de toda la flota (Tucumán + BsAs + Salta + Jujuy + Rosario) |
| Asignación de unidades por sucursal | Cada unidad asociada a su base de operación |
| Reportes por sucursal | Costos y estado de mantenimiento segmentados por sede |

### Fuente de datos

Los datos de mantenimiento actual (si existen en Excel o en algún sistema) se relevan en la primera sesión de trabajo. Si el registro hoy es manual o informal, el sistema de AlegentAI se convierte en el primer registro digital formal de la flota.

### Inversión Línea 2

| Concepto | Horas | USD | ARS |
|----------|:-----:|:---:|:---:|
| Relevamiento y análisis de datos actuales | 40 hs | USD 1.200 | ARS 1.752.000 |
| Desarrollo módulos base (registro + plan + alertas) | 200 hs | USD 6.000 | ARS 8.760.000 |
| Historial de intervenciones + auditoría de costos | 160 hs | USD 4.800 | ARS 7.008.000 |
| Indicadores gerenciales + exportación + capacitación | 120 hs | USD 3.600 | ARS 5.256.000 |
| Módulo multi-sucursal + sincronización centralizada | 80 hs | USD 2.400 | ARS 3.504.000 |
| **Total Línea 2** | **600 hs** | **USD 18.000** | **ARS 26.280.000** |

---

## 5. Línea 3 — Visualización en Tiempo Real de Flota

### Descripción

Panel de monitoreo en tiempo real de los camiones de Expreso Bisonte, consumiendo los reportes de posición descargados del sistema **Sitrack**. Permite visualizar el estado operativo de la flota en un mapa interactivo, con datos de posición, velocidad y estado de cada unidad.

Las rutas cubren **corredores nacionales** (RN9, RN34, RN40) entre las 5 sucursales — Tucumán-BsAs, Tucumán-Rosario, Tucumán-Salta-Jujuy y el corredor NOA completo — con recorridos de hasta **1.500 km entre origen y destino**. Cada sucursal necesita ver el estado de sus unidades asignadas sin perder visibilidad del conjunto.

### Funcionamiento técnico

```
Reporte Excel Sitrack (descarga manual o automática)
        ↓
    Motor de procesamiento AlegentAI
        ↓
    Panel de visualización en tiempo real
        ↓
    Mapa interactivo con posición de cada camión
```

### Disclaimer — Integración Sitrack

> ⚠️ **Sitrack no expone una API pública documentada.** La integración de datos se realiza a través de los reportes Excel que el sistema permite descargar desde su portal web. En esta primera versión:
>
> - Los reportes se descargan manualmente desde el portal Sitrack y se cargan al sistema
> - El sistema procesa el Excel y actualiza el mapa automáticamente
> - La frecuencia de actualización depende de la frecuencia con que se descargue el reporte
>
> Una integración automatizada (descarga periódica sin intervención humana) es técnicamente viable mediante automatización del portal web, pero requiere evaluación de los términos de uso de Sitrack y análisis adicional. No está incluida en el alcance base de esta propuesta.

### Funcionalidades base

| Módulo | Descripción |
|--------|-------------|
| Mapa interactivo | Visualización de posición de cada camión sobre mapa geográfico |
| Estado por unidad | Indicador visual: en movimiento / detenido / motor apagado |
| Velocidad en tiempo real | Dato de velocidad actual y alertas por exceso |
| Historial de recorrido | Trayectoria del día para cada unidad |
| Integración con Línea 2 | Cruce de datos de posición con estado de mantenimiento — alertas si una unidad en ruta tiene mantenimiento vencido |
| Exportación | Reporte de actividad diaria/semanal por unidad |
| Vista por sucursal de origen/destino | Filtrar camiones por ruta o sede |
| Panel multi-sucursal | Cada sede ve sus unidades asignadas |
| Alertas de llegada a sucursal | Notificación cuando una unidad llega a destino |

### Inversión Línea 3

| Concepto | Horas | USD | ARS |
|----------|:-----:|:---:|:---:|
| Análisis del formato de reportes Sitrack | 40 hs | USD 1.200 | ARS 1.752.000 |
| Motor de procesamiento de reportes Sitrack | 120 hs | USD 3.600 | ARS 5.256.000 |
| Panel de visualización + mapa interactivo | 160 hs | USD 4.800 | ARS 7.008.000 |
| Integración con Línea 2 + capacitación | 80 hs | USD 2.400 | ARS 3.504.000 |
| Módulo multi-sucursal + filtros por ruta/sede | 80 hs | USD 2.400 | ARS 3.504.000 |
| **Total Línea 3** | **480 hs** | **USD 14.400** | **ARS 21.024.000** |

---

## 6. Resumen de Inversión Total

| Línea | Descripción | Horas | USD | ARS |
|-------|-------------|:-----:|:---:|:---:|
| Línea 1 | Automatización ~15 procesos Excel | 1.000 hs | USD 30.000 | ARS 43.800.000 |
| Línea 2 | Auditoría mantenimiento de flota (multi-sucursal) | 600 hs | USD 18.000 | ARS 26.280.000 |
| Línea 3 | Visualización en tiempo real (Sitrack multi-sucursal) | 480 hs | USD 14.400 | ARS 21.024.000 |
| **TOTAL SISTEMA INTEGRAL** | | **2.080 hs** | **USD 62.400** | **ARS 91.104.000** |

> Las líneas son **contratables de forma independiente**. Bisonte puede iniciar con una o dos líneas y sumar las restantes en cualquier momento dentro del período de contrato.

---

## 7. Modalidades de Contratación

### Opción A — Sistema Propietario (Bisonte dueño del código)

Bisonte contrata el desarrollo completo. Al finalizar cada fase, recibe el código fuente y la propiedad intelectual del módulo entregado. AlegentAI no retiene derechos sobre el sistema.

**Esquema de pago:** 30% anticipo al inicio de cada fase + 70% contra entrega verificada.

| Línea | Anticipo por fase | Total |
|-------|:-----------------:|:-----:|
| Línea 1 | USD 9.000 (30% de USD 30.000) | USD 30.000 |
| Línea 2 | USD 5.400 (30% de USD 18.000) | USD 18.000 |
| Línea 3 | USD 4.320 (30% de USD 14.400) | USD 14.400 |
| **Total sistema** | | **USD 62.400** |

**Mantenimiento post-entrega (postventa):**

| Plan | Incluye | USD/mes | ARS/mes |
|------|---------|:-------:|:-------:|
| Básico | Corrección de errores + actualizaciones de seguridad | USD 600/mes | ARS 876.000/mes |
| Evolutivo | Básico + mejoras menores + hasta 1 proceso nuevo por año | USD 1.000/mes | ARS 1.460.000/mes |
| Full | Sin límite de evolutivos + prioridad de atención | USD 1.600/mes | ARS 2.336.000/mes |

> El mantenimiento es **obligatorio** para garantizar la continuidad operativa del sistema. AlegentAI no puede garantizar soporte sobre versiones que no están bajo un plan de mantenimiento activo.

---

### Opción B — SaaS (AlegentAI opera el sistema)

Bisonte paga una cuota mensual. AlegentAI desarrolla, opera, mantiene y evoluciona el sistema. Sin inversión inicial. El código es de AlegentAI; Bisonte tiene licencia de uso mientras el contrato esté vigente.

| Plan | Incluye | USD/mes | ARS/mes |
|------|---------|:-------:|:-------:|
| **Línea 1 — Starter** | Primeros 5 procesos operativos | USD 1.200/mes | ARS 1.752.000/mes |
| **Línea 1 — Operativo** | Hasta 10 procesos operativos | USD 2.000/mes | ARS 2.920.000/mes |
| **Línea 1 — Completo** | Los ~15 procesos completos | USD 2.800/mes | ARS 4.088.000/mes |
| **Línea 2** | Mantenimiento de flota multi-sucursal | USD 1.500/mes | ARS 2.190.000/mes |
| **Línea 3** | Visualización Sitrack multi-sucursal | USD 1.200/mes | ARS 1.752.000/mes |
| **Sistema Integral (3 líneas)** | Todo incluido | USD 5.200/mes | ARS 7.592.000/mes |

Contrato mínimo: **12 meses**. Equivalente año 1: **USD 62.400/año** (sistema integral).

El SaaS incluye:
- Desarrollo del sistema completo
- Hosting en infraestructura de AlegentAI
- Mantenimiento correctivo y evolutivo
- Soporte operativo ante incidentes
- Actualizaciones del sistema sin costo adicional

---

### Comparativa A vs B

| Concepto | Opción A (Propietario) | Opción B (SaaS) |
|----------|:----------------------:|:----------------:|
| Inversión inicial | USD 62.400 (en fases) | USD 0 |
| Costo mensual | USD 600-1.600 (mantenimiento) | USD 5.200 (sistema integral) |
| Costo total año 1 | USD 62.400 + mantenimiento | USD 62.400 |
| Costo total año 3 | ~USD 76.800 | USD 187.200 |
| Propietario del código | **Bisonte** | AlegentAI |
| Riesgo de continuidad | Código propio al finalizar | Depende del contrato vigente |
| Flexibilidad de cambios | Alta (código propio) | A cargo de AlegentAI |
| **Recomendación** | Si se proyecta uso a largo plazo | Si se prefiere cero inversión inicial |

**Recomendación de AlegentAI:** Opción A. A partir del año 2, el sistema propietario tiene un costo total significativamente menor. Y Bisonte retiene la propiedad del conocimiento operativo codificado en el sistema.

---

## 8. Cronograma General

```
          M1   M2   M3   M4   M5   M6   M7   M8   M9   M10  M11  M12
LÍNEA 1   |CDO  |--Bloque A--|--Bloque B--|--Bloque C--|--Bloque D--|
LÍNEA 2   |Relev|--Desarrollo base--|--Historial y costos--|--Integración--|
LÍNEA 3   |Relev|--Motor Sitrack--|--Visualización--|--Integración--|
```

Las tres líneas corren en paralelo con equipos de trabajo independientes.

---

## 9. Criterios de Aceptación

**Línea 1 — Por cada proceso entregado:**
- CREEMOS QUE automatizando el proceso X reduce el tiempo operativo asociado
- RESULTARÁ EN que el proceso se ejecuta sin intervención manual con resultado verificable
- CRITERIOS: (1) Proceso ejecutado con datos reales de Bisonte sin errores; (2) resultado validado por la referente del negocio como equivalente al manual; (3) log de auditoría generado por ejecución; (4) el equipo de Bisonte opera el proceso de forma autónoma

**Línea 2 — Módulo de mantenimiento:**
- CREEMOS QUE centralizar el registro de mantenimiento elimina dependencias personales
- RESULTARÁ EN visibilidad total del estado de la flota en tiempo real
- CRITERIOS: (1) 100% de la flota registrada; (2) alertas enviadas antes de vencimientos; (3) historial de intervenciones consultable por unidad y por período

**Línea 3 — Visualización Sitrack:**
- CREEMOS QUE visualizar posiciones de camiones desde reportes Sitrack mejora la coordinación operativa
- RESULTARÁ EN reducción del tiempo de consulta sobre estado de envíos
- CRITERIOS: (1) Mapa actualizado correctamente al cargar reporte Sitrack; (2) datos de posición, velocidad y estado visibles por unidad; (3) cruce con datos de mantenimiento operativo

---

## 10. Lo Que AlegentAI NO Compromete en Esta Propuesta

Con el objetivo de establecer un acuerdo transparente y sostenible:

- **Los ~14 procesos adicionales de Línea 1** se definen en relevamiento con la referente del negocio, no antes. La propuesta compromete la metodología y la capacidad de entrega, no un listado de procesos desconocidos.
- **La integración directa con Transoft** (acceso a base de datos o API del sistema operativo) no está incluida. Los datos deben estar disponibles como Excel descargado.
- **La integración directa con Sitrack** (acceso automatizado al portal) no está incluida en el alcance base. Requiere análisis adicional de viabilidad técnica y legal.
- **Plazos de entrega** de procesos individuales de Línea 1 dependen de la disponibilidad de la referente del negocio para los relevamientos. Sin esa instancia, AlegentAI no inicia el desarrollo del proceso.
- **Datos históricos** de mantenimiento o flota que no existan en formato digital no pueden ser incorporados sin un trabajo previo de digitalización, que se presupuesta por separado si fuera necesario.

---

## 11. Por Qué AlegentAI

| Factor | Detalle |
|--------|---------|
| Experiencia real con Bisonte | La PoC CDO/PF fue desarrollada con datos reales de la empresa — no es una demo genérica |
| Metodología validada | 560 horas de trabajo con el proceso más complejo como base; la curva ya fue recorrida |
| Compromiso honesto | Solo comprometemos lo que podemos demostrar, no lo que suena bien en una presentación |
| Equipo dedicado | Nelson Acosta (CTO / Desarrollo IA) + Pablo Ruiz (COO / Gestión de proyectos) |
| Continuidad | 12 meses de trabajo conjunto — AlegentAI no entrega y desaparece |
| Escala gradual | Bisonte puede empezar por una línea y sumar las demás cuando decida |

---

## 12. Próximos Pasos

| # | Acción | Responsable | Plazo |
|---|--------|-------------|-------|
| 1 | Revisión de esta propuesta y definición de líneas a contratar | Gerencia Bisonte | A confirmar |
| 2 | Definición de modalidad (A — Propietario / B — SaaS) | Gerencia Bisonte | A confirmar |
| 3 | Relevamiento Línea 1: sesión con la referente del negocio | Referente Bisonte + Nelson | Semana 1 post-acuerdo |
| 4 | Relevamiento Línea 2: sesión con el área de flota | Responsable flota + Nelson | Semana 1 post-acuerdo |
| 5 | Análisis de reportes Sitrack (Línea 3) | Nelson + responsable Bisonte | Semana 2 post-acuerdo |
| 6 | Firma del acuerdo y arranque | Ambas partes | Semana 2 post-acuerdo |

---

*Propuesta preparada por AlegentAI — Tucumán, Argentina — Junio 2026*
*Válida por 30 días desde la fecha de emisión*

---

## 13. Análisis de Retorno sobre Inversión (ROI)

### El punto de partida real

La gerente general de Expreso Bisonte dedica entre **3 y 5 horas diarias, los 7 días de la semana**, al procesamiento manual de los ~15 procesos operativos que esta propuesta automatiza. No son horas de empleados operativos: son horas de la máxima autoridad de la empresa, aplicadas a trabajo que un sistema puede ejecutar en segundos.

Esto representa entre **1.092 y 1.820 horas anuales** de tiempo gerencial absorbido por tareas repetitivas.

### Supuestos del análisis

| Parámetro | Valor | Criterio |
|-----------|-------|----------|
| Horas diarias dedicadas | 3 h (base) — 5 h (real) | Dato provisto por la gerente |
| Días de trabajo por semana | 7 días | Dato provisto por la gerente |
| Semanas por año | 52 | |
| Horas anuales (base) | 1.092 hs/año | 3 hs × 7 días × 52 semanas |
| Horas anuales (real) | 1.820 hs/año | 5 hs × 7 días × 52 semanas |
| Costo hora gerencia (conservador) | USD 15/h | Referencia mercado transporte Argentina |
| Costo hora gerencia (real) | USD 25/h | Valor de oportunidad — tiempo gerencial |
| Inversión sistema integral | USD 62.400 | Opción A — sistema propietario |
| Mantenimiento año 2+ | USD 7.200/año | USD 600/mes plan básico |
| SaaS (Opción B) | USD 62.400/año | USD 5.200/mes × 12 meses |

> El costo de hora gerencial se expresa conservadoramente. El valor real del tiempo liberado —decisiones más rápidas, menos errores, capacidad estratégica recuperada— no está cuantificado en este análisis para mantener la propuesta honesta y verificable.

---

### Tabla 1 — Costo del trabajo manual actual (sin automatizar)

| Escenario | Hs/año | Costo/año (USD 15/h) | Costo/año (USD 25/h) | Costo 3 años (USD 15/h) | Costo 3 años (USD 25/h) |
|-----------|:------:|:--------------------:|:--------------------:|:-----------------------:|:-----------------------:|
| Base (3 hs/día, 7 días) | 1.092 hs | USD 16.380 | USD 27.300 | USD 49.140 | USD 81.900 |
| Real (5 hs/día, 7 días) | 1.820 hs | USD 27.300 | USD 45.500 | USD 81.900 | USD 136.500 |

> **En el escenario real (5 hs/día a USD 25/h), Bisonte gasta USD 136.500 en 3 años en trabajo manual que este sistema reemplaza.**

---

### Tabla 2 — ROI acumulado: Sin sistema vs Opción A vs Opción B

#### Escenario Base (3 hs/día — USD 15/h)

| Período | Sin sistema | Opción A (propietario) | Opción B (SaaS) |
|---------|:-----------:|:----------------------:|:----------------:|
| Año 1 | Costo manual: USD 16.380 | Inversión: USD 62.400 | Costo: USD 62.400 |
| Año 2 | Costo acum: USD 32.760 | Inversión + mant: USD 69.600 | Costo acum: USD 124.800 |
| Año 3 | Costo acum: USD 49.140 | Total acum: USD 76.800 | Costo acum: USD 187.200 |
| **Balance año 3** | **Gasto total: USD 49.140** | **Neto: −USD 27.660** | **Neto: −USD 187.200** |

#### Escenario Real (5 hs/día — USD 25/h)

| Período | Sin sistema | Opción A (propietario) | Opción B (SaaS) |
|---------|:-----------:|:----------------------:|:----------------:|
| Año 1 | Costo manual: USD 45.500 | Inversión: USD 62.400 | Costo: USD 62.400 |
| Año 2 | Costo acum: USD 91.000 | Inversión + mant: USD 69.600 | Costo acum: USD 124.800 |
| Año 3 | Costo acum: USD 136.500 | Total acum: USD 76.800 | Costo acum: USD 187.200 |
| **Ahorro neto año 3** | **—** | **✅ USD 59.700 ahorrado** | **❌ USD 50.700 más caro** |

> En el escenario real, con la Opción A la empresa no solo recupera la inversión — **gana USD 59.700 netos en 3 años** comparado con seguir operando manualmente.

---

### Tabla 3 — Punto de equilibrio (Break-even Opción A)

¿En qué mes el ahorro acumulado del sistema supera el costo de la inversión?

| Escenario | Ahorro mensual | Break-even |
|-----------|:--------------:|:----------:|
| Base (3 hs/día — USD 15/h) | USD 1.365/mes | Mes 46 (año 3.8) |
| Base (3 hs/día — USD 25/h) | USD 2.275/mes | Mes 27 (año 2.3) |
| Real (5 hs/día — USD 15/h) | USD 2.275/mes | Mes 27 (año 2.3) |
| **Real (5 hs/día — USD 25/h)** | **USD 3.790/mes** | **✅ Mes 16 (año 1.4)** |

> En el escenario más probable (5 hs/día), **el sistema se paga solo en 16 meses**. A partir del mes 17, cada mes genera USD 3.790 de retorno neto para Bisonte.

---

### Tabla 4 — Horas gerenciales liberadas

| Año | Procesos automatizados | Hs liberadas/año (3 hs/día) | Hs liberadas/año (5 hs/día) | Equivalente FTE |
|-----|:----------------------:|:---------------------------:|:---------------------------:|:---------------:|
| Año 1 | ~5 procesos | ~365 hs | ~608 hs | 0.3 – 0.5 FTE |
| Año 2 | ~10 procesos | ~730 hs | ~1.215 hs | 0.6 – 1.0 FTE |
| Año 3 | ~15 procesos | **1.092 hs** | **1.820 hs** | **1.0 – 1.5 FTE** |

> Al finalizar el año 3 con los 15 procesos automatizados, la gerente general recupera entre **1.092 y 1.820 horas anuales** — equivalente a contratar entre 1 y 1.5 personas de tiempo completo, pero en tiempo de la persona más estratégica de la empresa.

---

### Impacto multi-sucursal en el ROI

| Concepto | Detalle | Impacto |
|----------|---------|---------|
| 5 sucursales operando | Tucumán, Buenos Aires, Salta, Jujuy, Rosario | Todos los procesos de L1 abarcan la operación completa de la empresa |
| Flota distribuida (L2) | Unidades en 5 bases distintas | Sin sistema centralizado: cada sucursal gestiona su mantenimiento en forma aislada — sin visibilidad cruzada, sin alertas unificadas |
| Rutas inter-provinciales (L3) | Corredores RN9, RN34, RN40 — hasta 1.500 km entre origen y destino | Un camión sin mantenimiento al día operando en ruta larga = riesgo operativo y legal para toda la empresa |
| Costo de falla en ruta | Remolque + lucro cesante + penalidades al cliente | USD 1.500 – 5.000 por incidente en ruta larga |
| Ahorro potencial por prevención (estimado conservador 1 incidente/trimestre evitado) | 4 incidentes/año × USD 2.000 promedio | **USD 8.000/año adicional en ahorro** |

> La escala multi-sucursal no multiplica linealmente el costo del sistema — se desarrolla una vez y se despliega en todas las sedes. Pero sí multiplica el valor generado: un sistema centralizado de flota y tracking para 5 sucursales vale proporcionalmente más que uno para una sola sede.

---

### Tabla 5 — Costo de Oportunidad del tiempo gerencial

El costo directo (USD 15-25/h) mide lo que cuesta pagar esas horas. El **costo de oportunidad** mide lo que Bisonte **deja de ganar** porque su gerente general está ejecutando tareas manuales en lugar de hacer lo que solo ella puede hacer.

#### ¿Qué hace una gerente general con su tiempo estratégico?

| Actividad estratégica desplazada | Valor estimado por hora | Fundamento |
|----------------------------------|:-----------------------:|------------|
| Negociación con clientes y contratos nuevos | USD 80–150/h | Un contrato nuevo de transporte genera USD 8.000–15.000/año. Una reunión de 4 hs puede generar ese retorno. |
| Gestión de proveedores y costos operativos | USD 50–80/h | Renegociar tarifas de combustible, seguros o mantenimiento con volumen impacta directamente en el margen. |
| Decisiones de RRHH y estructura | USD 40–60/h | Contratar, retener o reemplazar personal clave en transporte tiene un costo promedio de 3-6 meses de salario. |
| Planificación operativa y rutas | USD 40–70/h | Optimizar rutas y capacidad en transporte puede reducir costos entre 5% y 15% del gasto operativo. |
| **Valor estratégico promedio conservador** | **USD 50/h** | Estimación mínima — una gerente general que no gestiona estratégicamente le cuesta más a la empresa de lo que le pagan. |

#### Costo de oportunidad acumulado

| Escenario | Hs/año | Costo directo/año (USD 25/h) | Costo oportunidad/año (USD 50/h) | **Costo total real/año** | **Costo total 3 años** |
|-----------|:------:|:----------------------------:|:--------------------------------:|:------------------------:|:----------------------:|
| Base (3 hs/día) | 1.092 hs | USD 27.300 | USD 54.600 | **USD 81.900** | **USD 245.700** |
| Real (5 hs/día) | 1.820 hs | USD 45.500 | USD 91.000 | **USD 136.500** | **USD 409.500** |

> El costo de oportunidad (USD 50/h) es conservador. No representa lo que cobra la gerente por hora — representa el **valor del trabajo estratégico que no se hace** mientras ella procesa planillas. En muchas empresas de transporte, una hora de gestión comercial de la gerencia genera entre USD 80 y USD 150 de impacto directo en el resultado.

#### Break-even incluyendo costo de oportunidad (Opción A — USD 62.400)

| Escenario | Costo total mensual (directo + oportunidad) | Break-even |
|-----------|:-------------------------------------------:|:----------:|
| Base (3 hs/día — USD 75/h combinado) | USD 6.825/mes | **Mes 9.1 (< 10 meses)** |
| Real (5 hs/día — USD 75/h combinado) | USD 11.375/mes | **✅ Mes 5.5 (< 6 meses)** |

> Cuando se incorpora el costo de oportunidad, **el sistema se paga solo en menos de 6 meses** en el escenario real. Cada mes de demora en tomar la decisión le cuesta a Bisonte entre USD 6.825 y USD 11.375 entre trabajo manual desaprovechado y valor estratégico no generado.

#### Lo que Bisonte gana al liberar ese tiempo

| Año | Hs liberadas (5 hs/día) | Si el 20% se convierte en gestión comercial | Contratos adicionales potenciales |
|-----|:-----------------------:|:-------------------------------------------:|:--------------------------------:|
| Año 1 | 608 hs (~5 procesos) | 121 hs de gestión comercial | 15–30 reuniones = 5–10 contratos |
| Año 2 | 1.215 hs (~10 procesos) | 243 hs de gestión comercial | 30–60 reuniones = 10–20 contratos |
| Año 3 | 1.820 hs (~15 procesos) | 364 hs de gestión comercial | 45–90 reuniones = 15–30 contratos |

> Estos números no se incluyen en el cálculo de ROI para mantener el análisis conservador y verificable. Son el potencial adicional — no la promesa.

---

### Conclusión financiera

#### Costo total de NO automatizar en 3 años (escenario real, 5 hs/día)

| Concepto | Monto |
|----------|-------|
| Costo directo del tiempo gerencial (USD 25/h × 1.820 hs × 3 años) | USD 136.500 |
| Costo de oportunidad (USD 50/h × 1.820 hs × 3 años) | USD 273.000 |
| **Costo total real de no automatizar** | **USD 409.500** |

#### Comparado con invertir en el sistema

| Concepto | Monto |
|----------|-------|
| Inversión sistema integral (Opción A) | USD 62.400 |
| Mantenimiento años 2 y 3 (USD 7.200 × 2) | USD 14.400 |
| **Inversión total 3 años** | **USD 76.800** |

**Diferencia: USD 409.500 (no automatizar) vs USD 76.800 (automatizar) = USD 332.700 de diferencia en 3 años.**

**La automatización no es un gasto. Es la única decisión financieramente racional cuando el costo de no hacerla supera en casi 6 veces el costo de hacerla.**

> Este análisis es conservador. No cuantifica errores evitados, demoras reducidas, decisiones más rápidas ni el impacto en la calidad de vida de la gerente general. El único supuesto que puede discutirse es el valor de USD 50/h para el costo de oportunidad — pero incluso con USD 0 de costo de oportunidad, el break-even del sistema llega en el mes 16 (escenario real).

---

**Aprobación interna AlegentAI**

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| CTO / Desarrollo | Nelson Acosta | | |
| COO / Comercial | Pablo Ruiz | | |
