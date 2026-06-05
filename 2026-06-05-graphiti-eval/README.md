# Graphiti — Evaluación de adopción

**Fecha:** 2026-06-05
**Repo:** https://github.com/getzep/graphiti
**Status al evaluar:** v0.21.0 (hace 13 días), 14k⭐, Python, Apache-2.0
**Mantenedor:** Zep (memoria para chatbots)

---

## ¿Qué es?

Graphiti es un **grafo de conocimiento con ventanas de tiempo** para agentes AI long-running.

- Las relaciones tienen `valid_from` / `valid_to` (memoria temporal real)
- Construido sobre **Neo4j** o **FalkorDB**
- Pensado para coding agents y chatbots que necesitan **recordar contexto a lo largo del tiempo**
- 14k⭐, 947 forks, ~10 meses de vida

**Diferencia clave vs RAG clásico:** RAG busca "lo más similar". Graphiti sabe **"qué era verdad en marzo"** y **"qué dejó de ser verdad en julio"**.

---

## Casos evaluados

| Caso de uso | Veredicto | Notas |
|-------------|-----------|-------|
| ForestAI documental (RAG sobre PDFs de campo) | ❌ No | RAG clásico con Qdrant es más simple y nuestro pipeline ya funciona. |
| Bisonte pipeline Excel→Excel | ❌ No | Flujo estático, no necesita memoria temporal. |
| **Expreso Bisonte gerenta (WhatsApp tickets, contexto 6+ meses)** | ✅ **Sí encaja** | Ella necesita que el agente "se acuerde" de reglas definidas en marzo cuando hable en julio. |
| ForestAI en producción (escalar a muchos usuarios) | 🔁 Re-evaluar | Cuando crezca, evaluar como capa de memoria del agente. |
| Spike interno con un coding agent que arme un backend solo | 🔶 Spike | Sirve como benchmark. |

---

## Análisis técnico

### Fortalezas
- **Problema real que resuelve**: los agentes olvidan. No todos los agentes necesitan memoria temporal, pero los long-running sí.
- **Open source + activo**: commits frecuentes, v0.21.0 hace 13 días, mantiene tracción.
- **Python first**: encaja con nuestro stack (regla de oro: Python backend / React frontend).
- **Doble backend**: Neo4j para producción seria, FalkorDB para PoCs livianos.

### Debilidades
- **Neo4j es infra pesada** para PoCs. FalkorDB es la opción liviana para arrancar.
- **Complejidad operativa**: agregar un grafo de conocimiento a un sistema que hoy vive con `MEMORY.md` + `fact_store` de Hermes no es trivial.
- **No es plug-and-play**: requiere modelar entidades, relaciones y ventanas de tiempo. No es "instalar y listo".

### ¿Cuándo lo necesitaríamos DE VERDAD?
- Cuando un agente del equipo viva **3+ meses** con el mismo cliente.
- Cuando la gerenta de Expreso Bisonte (o cualquier stakeholder) pida **"recordá lo que hablamos en marzo"** y la memoria de la conversación se haya compactado.
- Cuando un usuario del ForestAI diga **"como la vez pasada"** y el sistema no entienda a qué se refiere.

---

## Decisión

**No adoptar ahora. Radar activo con revisión en 6-12 meses.**

**Por qué no:**
1. Hoy no resuelve un problema que tengamos. `hermes-agent` con su `MEMORY.md` + `fact_store` cubre el 80% del caso.
2. Neo4j/FalkorDB es **infra nueva** que sumar a un stack que ya funciona.
3. Nuestro horizonte de PoCs I+D+I es **3-6 meses**, no "agentes long-running de producción".

**Por qué re-evaluar:**
- Si la línea de agentes con stakeholders madura (Bisonte gerenta, ForestAI operadores), Graphiti pasa de "interesante" a "necesario".
- Si Zep lo adopta un actor grande del ecosistema (Anthropic, OpenAI, framework major) y la integración se vuelve trivial.

---

## Si se adopta: cómo empezar

```bash
# 1. Levantar FalkorDB local (liviano, no Neo4j)
docker run -p 6379:6379 -p 3000:3000 -it --rm falkordb/falkordb:latest

# 2. Instalar Graphiti
pip install graphiti-core

# 3. Spike mínimo
# - Conectar a FalkorDB
# - Cargar 3 hechos de prueba con ventanas de tiempo
# - Query "¿qué sabía el agente sobre X en fecha Y?"
# - Comparar vs MEMORY.md plano de Hermes
```

**Tiempo estimado de spike:** 1 día.
**Costo de infra:** FalkorDB ~50MB RAM. Neo4j ~1GB RAM. Diferencia enorme.

---

## Estado

- [x] Evaluación inicial
- [ ] Spike con FalkorDB (cuando haya caso concreto)
- [ ] Revisión 6-12 meses: 2026-12 / 2027-06

**Tags:** `#memory` `#agents` `#long-running` `#radar`
