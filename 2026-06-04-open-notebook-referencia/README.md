# Brainstorming: Open Notebook como Referencia de Producto

**Fecha:** 2026-06-04  
**Origen:** Solicitud de Nelson (Tony Stark) — investigar si open-notebook sirve para el stack AlegentAI.  
**Decisión:** NO integrar. Guardar como referencia de producto y arquitectura. Concepto de podcast multi-speaker anotado para futuras features.

---

## Qué es Open Notebook

Producto completo open source (24K ⭐, MIT) — alternativa self-hosted a **Google Notebook LM**.

**Autor:** lfnovo  
**Repo:** https://github.com/lfnovo/open-notebook  
**Web:** https://www.open-notebook.ai

---

## Stack Tecnológico

| Capa | Tecnología | Compatibilidad Stack Nelson |
|------|------------|----------------------------|
| Backend | Python FastAPI | ✅ Compatible |
| Frontend | Next.js | ❌ No (Nelson usa React + Vite) |
| Base de datos | SurrealDB | ❌ No (Nelson usa PostgreSQL + Qdrant) |
| ORM/ODM | SurrealDB nativo | ❌ No compatible con SQLAlchemy |
| LLM Framework | LangChain | ✅ Compatible (ya usado en equipo) |
| Vector Search | SurrealDB built-in | ❌ No (Nelson usa Qdrant) |
| Auth | Password middleware propio | ⚖️ Rehacer |

---

## Features Principales

1. **Ingesta multi-formato** — PDFs, videos, audio, web pages, textos.
2. **RAG con chat** — Conversaciones con contexto de las fuentes ingestadas.
3. **Vector search** — Búsqueda semántica sobre todo el contenido.
4. **Generación de podcasts** — **Multi-speaker (1-4 speakers)** con perfiles custom. Principal diferenciador vs Notebook LM de Google.
5. **Transformaciones de contenido** — Resúmenes, extracción, reformulación.
6. **18+ providers LLM** — OpenAI, Anthropic, Ollama, LM Studio, etc.
7. **API REST completa** — Automatizable.

---

## Análisis de Integración

### Meta-Orchestrator / RAG Pipeline Actual
- Nelson ya tiene: chunking, embeddings (OpenAI/SentTransformers), Qdrant, chat RAG, document processing de 3 capas.
- **Veredicto:** NO reemplaza ni mejora lo existente. El RAG de open-notebook está atado a SurrealDB.

### ForestAI
- **Veredicto:** NO integrar como producto. Pero el concepto de **generación de podcasts multi-speaker** es valioso como inspiración.
  - Caso de uso potencial: generar resúmenes auditivos/podcasts de informes técnicos forestales para stakeholders que prefieren escuchar.
  - Caso de uso: briefings de inventario forestal en formato conversación entre "experto forestal" y "gerente de operaciones".

### Expreso Bisonte
- **Veredicto:** NO aplica. No hay ingestas de documentos ni necesidad de RAG en pipeline Excel.

---

## Valor como Referencia

| Aspecto | Valor para Nelson |
|---------|-------------------|
| Arquitectura RAG + chat | Media — ya cubierto por nelson-rag-pipeline |
| Ingesta multi-formato | Media — ya cubierto por nelson-document-processing |
| Podcast multi-speaker | **Alta** — concepto novel, aplicable a ForestAI |
| Organización en "notebooks" | Media — inspiración para agrupar proyectos |
| Multi-provider LLM | Baja — ya manejado por nelson-llm-generation |

---

## Componentes Potencialmente Reutilizables

- **Podcast generation engine** — El código de generación de scripts multi-speaker podría portarse a Python puro, sin dependencia de SurrealDB.
- **Speaker profile system** — Definir perfiles de voz/personalidad para diferentes personajes en un podcast.
- **Episode profiles** — Templates de formato (entrevista, debate, narración, análisis técnico).

---

## Requisitos para Futuro Uso (si se porta)

- Desacoplar de SurrealDB y reescribir modelos en SQLAlchemy/Pydantic.
- Reemplazar Next.js por React puro (Vite).
- Reemplazar vector search de SurrealDB por Qdrant.
- Integrar con el sistema de document processing existente (MarkItDown + Chunking).

---

## Próximos Pasos (Sujetos a Prioridad)

1. **Spike opcional:** Portar solo el módulo de podcast generation a un script Python standalone.
2. **Validar TTS:** Verificar si edge-tts (ya usado por Nelson) soporta múltiples voces en una misma sesión para simular diálogos.
3. Si la validación es positiva, integrar como feature en ForestAI: "Generar briefing auditivo del informe forestal".

---

## Criterios de Activación (¿Cuándo revisitar?)

- Nelson dice: "quiero que ForestAI genere resúmenes en audio formato conversación".
- Stakeholder pide briefings auditivos de informes técnicos.
- Se decide hacer un producto tipo "Notebook LM privado" para investigación forestal.
