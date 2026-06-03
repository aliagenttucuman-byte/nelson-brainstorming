# Agenda + guion de preguntas — reunión con Pablo

Fecha: 2026-06-03
Tiempo total: 30 minutos

## 0-5 min — Contexto y objetivo

- Contexto: operación actual depende de trabajo manual intensivo.
- Objetivo: acordar formalización mínima para acelerar automatización segura.

## 5-12 min — Estado de avance (rápido)

- Diccionario de datos v1 generado.
- Matriz de reglas v1 generada.
- Validación E2E documentada.
- Gap pendiente: definición funcional completa de PF trabajada.

## 12-22 min — Preguntas críticas para decisión

### A) Ownership y gobernanza de datos
1. ¿Quién aprueba definición final de cada campo agregado?
2. ¿Quién valida cambios futuros del diccionario?
3. ¿Cuál es el SLA esperado para resolver dudas funcionales?

### B) Reglas de negocio PF (núcleo)
4. ¿Cuál es el criterio formal de ingreso de registros a PF trabajada?
5. ¿Cuáles son los casos de exclusión obligatoria?
6. ¿Qué excepciones se aceptan y cómo se justifican?

### C) Fuentes externas / enriquecimiento
7. ¿Qué campos se completan desde fuera del Excel base?
8. ¿Qué fuente manda si hay conflicto entre fuentes?
9. ¿Qué campos quedan “manuales” por ahora y por qué?

### D) Adopción operativa
10. ¿Quién será referente operativo para validación diaria?
11. ¿Cómo medimos éxito en 2 semanas?
12. ¿Qué riesgo operativo no se puede tolerar?

## 22-27 min — Cierre de decisiones

Definir explícitamente:
- Owners por campo crítico.
- Regla PF de inclusión/exclusión.
- Backlog v1.1 y prioridades.
- Fecha de demo de avance.

## 27-30 min — Próximos pasos

- Publicar minuta con decisiones en el repo de brainstorming.
- Abrir sprint de robustez v1.1.
- Programar checkpoint ejecutivo (15 min) a mitad de semana 2.

## Resultado esperado de la reunión

- Decisiones accionables hoy, sin bloquear operación.
- Alineación negocio-tecnología para pasar a ejecución inmediata.
