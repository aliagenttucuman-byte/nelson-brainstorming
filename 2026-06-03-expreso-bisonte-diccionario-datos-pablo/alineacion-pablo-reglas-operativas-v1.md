# Alineación con explicación de Pablo — Reglas Operativas v1

Fecha: 2026-06-03
Fuente: explicación funcional de Pablo (cobranzas + facturación)

## Resumen de match

La explicación de Pablo **sí hace match** con la ingeniería inversa y con la matriz v1.

## Reglas de negocio extraídas (texto Pablo -> regla técnica)

1) 4 hojas en un mismo libro
- CDO Sistema
- CDO Trabajada
- PTE de Fact Sistema
- PF Trabajada

2) Cobranzas (CDO)
- CDO Sistema = base diaria de pendientes de cobro contado.
- CDO Trabajada = resultado operativo diario con:
  - quién hay que cobrar,
  - asignación de REFERENTE,
  - observaciones/justificación.
- Transferencias impactan al día siguiente (T+1).
- Pagos parciales: se ven por cobro/saldo.
- Objetivo operativo: tender a 0 pendientes.
- Excluir envíos que no deben entrar en CC aunque el cliente tenga cuenta corriente.

3) Facturación (PF)
- PTE de Fact Sistema = base diaria de pendientes de facturar.
- PF Trabajada = resultado operativo con tratamiento y asignación.
- Regla de bloqueo: si está pendiente de facturar, no se inicia cobro.
- Carga manual clave por Edith: número de factura, número de guía o referencia.

## Estado actual de implementación vs Pablo

### Match alto (implementado o modelado)
- Flujo por pares Sistema <-> Trabajada.
- Regla de bloqueo por pendientes de facturar.
- Soporte de pagos parciales (saldo/cobro).
- Regla T+1 para transferencias.
- Campos manuales/operativos identificados: REFERENTE, JUSTIFICACIÓN, OBS.

### Match parcial (pendiente de cierre funcional)
- Catálogo formal de criterios para “no incluir en CC”.
- Reglas exactas de asignación automática de referente (balance de carga, prioridad, fallback).
- Definición final de PF Trabajada cuando hay universo externo/manual no derivable 1:1 de PTE sistema.

## Definiciones sugeridas para cerrar v1.1 (para reunión con Pablo)

1. Bloqueo de cobranzas
- `si nro ∈ PF pendiente => no entra a cobranza operativa`.

2. Referente
- si referente vacío => asignación automática por round-robin o por cartera.
- registrar fecha/hora de asignación y usuario/regla.

3. Cuenta Corriente (CC)
- campo/criterio explícito por envío: incluir/excluir en CC.
- default recomendado: excluir cuando no haya marca afirmativa.

4. Facturación
- campos obligatorios en PF Trabajada: `nro_factura`, `guia_o_referencia` (al menos uno según estado).
- estado mínimo: `pendiente` / `facturado`.

## Recomendación operativa

Trabajar con dos modos explícitos:
- **Modo Validación (benchmark):** usar trabajadas de referencia para comparar/medir match.
- **Modo Operación diaria:** generar trabajadas desde sistema + reglas + inputs manuales.

Así se evita mezclar “resultado esperado histórico” con “resultado calculado diario”.

## Próximo entregable

Matriz v1.1 con:
- regla por campo,
- owner por campo manual (Edith/sistema),
- validación automática por regla,
- KPI de calidad diaria (coincidencia vs benchmark cuando aplique).
