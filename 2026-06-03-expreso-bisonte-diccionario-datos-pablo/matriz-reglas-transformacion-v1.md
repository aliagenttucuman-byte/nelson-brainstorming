# Matriz de Reglas de Transformación v1 (Sistema -> Trabajada)

Generado: 2026-06-03 00:41

Objetivo: definir reglas explícitas por campo para replicar exactamente el comportamiento de las hojas trabajadas de referencia.

## Matriz CDO

- Sistema: **CDO SISTEMA** (405 filas, 14 cols)
- Trabajada: **CDO TRABAJADA** (402 filas, 17 cols)
- Clave `nro`: intersección=402 | solo_sistema=0 | solo_trabajada=0

| Campo sistema | Campo trabajada | Regla v1 | Tipo sistema | Tipo trabajada | % no nulo sistema | % no nulo trabajada | Ejemplo |
|---|---|---|---|---|---:|---:|---|
| clase | clase | DIRECTO (1:1) | texto/categórico | texto/categórico | 67.4% | 67.4% | SUCURSAL BUENOS AIRES |
| estado | ESTADO | DIRECTO (normalizar mayúsculas/nombre) | texto/categórico | texto/categórico | 98.8% | 98.8% | DT |
| fechaedit | fechaedit | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 98.8% | 98.8% | 2026-06-01 10:12:40 |
| guiafec | guiafec | DIRECTO (1:1) | fecha/datetime | fecha/datetime | 98.8% | 98.8% | 27/05/2026 |
| importe | importe | DIRECTO (1:1) | numérico(decimal) | numérico(decimal) | 98.8% | 98.8% | 790130.0 |
| nro | nro | DIRECTO (1:1) | texto/categórico | texto/categórico | 100.0% | 100.0% | A.0017.00000053 |
| nrogen_a | nrogen_a | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 4.7% | 4.0% | 157039.0 |
| razsocc | razsocc | DIRECTO (1:1) | texto/categórico | texto/categórico | 98.8% | 98.8% | IMPULSORA METALURGICA DEL NOROESTE |
| saldo | saldo | DIRECTO (1:1) | numérico(decimal) | numérico(decimal) | 98.8% | 98.8% | 790130.0 |
| succobro | succobro | DIRECTO (1:1) | texto/categórico | texto/categórico | 98.8% | 98.8% | BA |
| sucdest | sucdest | DIRECTO (1:1) | texto/categórico | texto/categórico | 98.8% | 98.8% | BA |
| sucori | sucori | DIRECTO (1:1) | texto/categórico | texto/categórico | 98.8% | 98.8% | JU |
| sucursal | sucursal | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 4.7% | 4.0% | 97.0 |
| tiporec | tiporec | DIRECTO (1:1) | texto/categórico | texto/categórico | 4.7% | 4.0% | XF |
| — | JUSTIFICACIÓN | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 9.2% | SGCP 6480 |
| — | OBSERVACIÓN | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 4.0% | VER DIF |
| — | REFERENTE | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 18.2% | BA |

## Matriz PF

- Sistema: **PTE DE FACT SISTEMA** (1409 filas, 37 cols)
- Trabajada: **PF TRABAJADA** (1503 filas, 16 cols)
- Clave `nro`: intersección=632 | solo_sistema=777 | solo_trabajada=871

| Campo sistema | Campo trabajada | Regla v1 | Tipo sistema | Tipo trabajada | % no nulo sistema | % no nulo trabajada | Ejemplo |
|---|---|---|---|---|---:|---:|---|
| clicta | clicta | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 99.6% | 99.6% | 2199.0 |
| dias_sin_c | dias_sin_c | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 99.6% | 99.6% | 2517.0 |
| ensucursal | ensucursal | DIRECTO (1:1) | texto/categórico | texto/categórico | 99.6% | 99.6% | CC |
| fecha_conf | fecha_conf | DIRECTO (1:1) | texto/categórico | fecha/datetime | 86.7% | 91.0% | 10/07/2019 03:48 PM |
| fecharecep | fecharecep | DIRECTO (1:1) | texto/categórico | texto/categórico | 99.6% | 99.6% | -   - |
| fecharep | fecharep | DIRECTO (1:1) | fecha/datetime | fecha/datetime | 86.7% | 91.0% | 10/07/2019 03:46 PM |
| guiaest | guiaest | DIRECTO (1:1) | texto/categórico | texto/categórico | 99.6% | 99.6% | ED |
| guiafec | guiafec | DIRECTO (1:1) | numérico(entero) | numérico(entero) | 99.6% | 99.6% | 2019-07-05 00:00:00 |
| importe | importe | DIRECTO (1:1) | numérico(decimal) | numérico(decimal) | 99.6% | 99.6% | 13659.230000000001 |
| nicsuc | nicsuc | DIRECTO (1:1) | texto/categórico | texto/categórico | 99.6% | 99.6% | BA |
| nro | nro | DIRECTO (1:1) | texto/categórico | texto/categórico | 100.0% | 100.0% | R.0009.00113004 |
| razsocc | razsocc | DIRECTO (1:1) | texto/categórico | texto/categórico | 99.6% | 99.6% | ASB TECHNOLOGIES S.R.L. |
| reparto | reparto | DIRECTO (1:1) | texto/categórico | texto/categórico | 86.7% | 91.0% | CC     32472 |
| — | JUSTIFICACIÓN | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 52.2% | PENDIENTE DE FACTURAR |
| — | OBS CTAS CTES | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 0.8% | Gestionar HM con el cliente |
| — | REFERENTE | MANUAL/OPERATIVO (input humano) | — | texto/categórico | — | 52.2% | OFICINA |
| Año-FECHARECEP | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| Año-GUIAFEC | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 2025.0 |
| AñoMes-FECHARECEP | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| AñoMes-GUIAFEC | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 202507.0 |
| clisuc | — | NO EXPUESTO en trabajada (descartado v1) | texto/categórico | — | 99.6% | — | CC |
| codmovesp | — | NO EXPUESTO en trabajada (descartado v1) | texto/categórico | — | 99.6% | — | GS |
| comprob | — | NO EXPUESTO en trabajada (descartado v1) | vacío | — | 0.0% | — |  |
| facconfor | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 1.0 |
| fcdocensuc | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| gravado | — | NO EXPUESTO en trabajada (descartado v1) | numérico(decimal) | — | 99.6% | — | 26153.81 |
| guicodsuc | — | NO EXPUESTO en trabajada (descartado v1) | texto/categórico | — | 99.6% | — | CC |
| guinrogen | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 241483.0 |
| impadi | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| impar | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| impcr | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| impdomic | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |
| impflete | — | NO EXPUESTO en trabajada (descartado v1) | numérico(decimal) | — | 99.6% | — | 26153.66 |
| impseg | — | NO EXPUESTO en trabajada (descartado v1) | numérico(decimal) | — | 99.6% | — | 0.15 |
| nicnro | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 103887.0 |
| sucdest | — | NO EXPUESTO en trabajada (descartado v1) | texto/categórico | — | 99.6% | — | BA Buenos Aires |
| totbultos | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 1.0 |
| totkilos | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 15.0 |
| totm3 | — | NO EXPUESTO en trabajada (descartado v1) | numérico(decimal) | — | 99.6% | — | 0.0 |
| valorcr | — | NO EXPUESTO en trabajada (descartado v1) | numérico(entero) | — | 99.6% | — | 0.0 |

## Reglas globales acordadas

1. El análisis y la validación se hacen por pares: Sistema <-> Trabajada.
2. Campos en trabajada sin origen en sistema se clasifican como manuales/operativos.
3. El matching primario es por `nro` (clave de trazabilidad).
4. Cuando la cobertura de `nro` no es 100%, la diferencia se documenta como universo externo/no derivable en v1.

## Pendientes para versión v1.1 (reunión con Pablo)

- Definir origen formal de registros en PF TRABAJADA que no aparecen en PTE DE FACT SISTEMA.
- Definir catálogo cerrado para JUSTIFICACIÓN / OBS (dominio permitido).
- Asignar owner por campo manual (quién completa, cuándo, y con qué validación).