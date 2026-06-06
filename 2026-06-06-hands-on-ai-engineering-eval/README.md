# Hands-On AI Engineering — Evaluación del Repo

**Fecha:** 2026-06-06
**Estado:** evaluado — ÚTIL COMO REFERENCIA
**Fuente:** https://github.com/Sumanth077/Hands-On-AI-Engineering
**Trigger:** Tony trajo el repo para evaluar si sirve al equipo I+D+I

---

## Veredicto

ÚTIL COMO REFERENCIA (biblioteca de recetas), NO como base de PoC directa.

**Stars:** 906 | **Forks:** 132 | **Licencia:** MIT | **Último commit:** ~3 semanas atrás | **Actividad:** activo, en construcción

---

## ¿Qué es?

Companion code del libro "Hands-On AI Engineering" (Sumanth077). 7 capítulos de
Jupyter Notebooks que cubren el ciclo completo de desarrollo con IA/LLM: desde
llamadas básicas a API hasta agentes multi-agente, RAG avanzado y fine-tuning.
Código limpio, progresivo, bien comentado. MIT License = libre uso.

---

## Estructura (7 capítulos)

| Capítulo | Contenido | Valor para el equipo |
|---|---|---|
| 1 - Intro | OpenAI Basics, Anthropic Basics | Bajo (ya lo sabemos) |
| 2 - Working with LLMs | Text gen, Structured outputs, Streaming, Token counting, Chat history | Medio (referencias rápidas) |
| 3 - Prompt Engineering | Basic, Few-shot, Chain of Thought, Templates | Medio (buenas recetas) |
| **4 - RAG** | Document loading, Chunking, Embeddings, Vector stores, Basic RAG, **Advanced RAG** | **ALTO** |
| **5 - AI Agents** | Tool use, ReAct, Planning, Memory, **Multi-Agent (LangGraph)** | **ALTO** |
| **6 - Fine-Tuning** | Dataset prep, OpenAI FT, Open-source (LoRA/PEFT) | **ALTO** (cuando sea el momento) |
| 7 - Multimodal | Image understanding, Audio (Whisper), Image generation (DALL-E) | Alto para ForestAI |

---

## Stack tecnológico

- Python 3.10+
- OpenAI API + Anthropic API
- LangChain + LangGraph + LlamaIndex
- ChromaDB + FAISS (vector stores)
- HuggingFace + PEFT/LoRA (fine-tuning)
- Jupyter Notebooks (solo formato educativo, no producción)
- python-dotenv para keys

NO tiene: FastAPI, React, Docker, tests, CI/CD, deployment.

---

## Lo que más vale para nuestro equipo

### Cap 4 — Advanced RAG (4.6)
Técnicas que se aplican directo a nelson-rag-pipeline:
- HyDE (Hypothetical Document Embeddings): genera respuesta hipotética y la usa para buscar docs similares
- Query Expansion: expande la query para mejorar recall
- Contextual Compression: extrae solo las partes relevantes del doc recuperado
- Re-ranking: reordena resultados por relevancia con modelo separado
- Multi-Vector Retrieval: múltiples embeddings por documento

Implementación: LangChain + ChromaDB + OpenAI.

### Cap 5 — Multi-Agent con LangGraph (5.5)
Exactamente lo que estamos construyendo en el meta-orquestador:
- Agent roles y especialización
- Agent communication
- Orchestrator/worker patterns
- Parallel vs sequential execution

Implementación: LangGraph + ToolNode + StateGraph + END.

### Cap 6 — Fine-Tuning open-source (6.3)
Para cuando ForestAI necesite un modelo custom (detección de especies, análisis
multiespectral). Usa PEFT/LoRA con HuggingFace Transformers.

### Cap 7 — Image Understanding (7.1)
Directamente aplicable a ForestAI (análisis de imágenes de drones).

---

## Lo que NO tiene (gap real)

- Sin FastAPI ni REST endpoints → hay que wrappearlo nosotros
- Sin React ni frontend → cero UI
- Sin Docker → no es deployment-ready
- Sin tests → no es producción
- Todo asume keys de OpenAI/Anthropic directo (nosotros usamos FreeLLMAPI proxy)

---

## Cómo usarlo en el flujo Nelson

1. Arrancar una PoC nueva con IA → revisar el capítulo relevante antes
2. Cap 4 → copiar receta de RAG avanzado, adaptar a FastAPI + Qdrant (nuestro stack)
3. Cap 5 → copiar patrón multi-agente LangGraph, integrar con meta-orquestador
4. Cap 6 → cuando una PoC exitosa necesite un modelo custom, acá está la receta
5. Cap 7 → para ForestAI vision features

**Regla de uso:** no clonar como base. Usar como biblioteca de referencia.
Cuando arranquemos una PoC que use RAG o agentes, capítulos 4 y 5 son la primera parada.

---

## Diferencias con nuestro stack

| Ellos usan | Nosotros usamos |
|---|---|
| OpenAI API directo | FreeLLMAPI proxy (:3101) |
| ChromaDB / FAISS | Qdrant (nelson-vector-databases) |
| LangChain | FastAPI + código propio + LangGraph cuando aplica |
| Jupyter notebooks | Python scripts + FastAPI endpoints |
| DALL-E | Azure Claude Vision + LocateAnything-3B (ForestAI) |

---

## Próximos pasos (si nos interesa profundizar)

- [ ] Spike: implementar HyDE + Re-ranking encima del pipeline Qdrant existente
- [ ] Spike: armar un grafo LangGraph simple para el meta-orquestador (Julián + Mercedes)
- [ ] Evaluar Cap 6 para fine-tuning de modelo de clasificación de especies (ForestAI)

---

## Referencias

- Repo: https://github.com/Sumanth077/Hands-On-AI-Engineering
- Skill relacionada: nelson-rag-pipeline (pipeline RAG del equipo)
- Skill relacionada: nelson-ai-agents (agentes del equipo)
- Skill relacionada: nelson-poc-ai-quickstart (reglas para arrancar una PoC con IA)
- Skill relacionada: nelson-meta-orchestrator (meta-orquestador en construcción)
- Eval anterior relacionada: 2026-06-06-locateanything-eval (visión para ForestAI)
