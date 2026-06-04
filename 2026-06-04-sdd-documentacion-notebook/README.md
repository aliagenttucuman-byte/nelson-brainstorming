# Brainstorming: Open-Notebook como Motor de Documentación SDD

**Fecha:** 2026-06-04  
**Origen:** Nelson (Tony Stark) — idea de adaptar el concepto de *notebook* (de Open Notebook) al flujo SDD de AlegentAI.  
**Posicionamiento:** Servicio transversal al flujo SDD. Principal valor en **Fase 0: Captura de Conocimiento del Stakeholder** (pre-Constitución), persistiendo durante todo el ciclo de vida del proyecto.  
**Estado:** Idea validada. Pendiente de spike cuando Nelson active.

---

## Hipótesis

> CREEMOS QUE si adaptamos el concepto de **notebook digital** (agrupar fuentes + notas + chat con contexto) al flujo SDD, RESULTARÁ en un sistema de documentación de proyectos donde el equipo puede **consultar, conversar y transformar** todo el conocimiento de un proyecto sin perder contexto entre sesiones.  
> CRITERIOS DE ACEPTACIÓN:
> - [ ] Un proyecto SDD tiene un "notebook" único con todas las fuentes ingestadas (specs, documentos, reuniones, decisiones).
> - [ ] El equipo puede hacer preguntas en lenguaje natural sobre el proyecto y obtener respuestas con citas a las fuentes originales.
> - [ ] Se pueden generar transformaciones automáticas: resumen ejecutivo, extracción de requisitos, detección de inconsistencias, briefing para stakeholders.
> - [ ] El notebook persiste entre sesiones y es consultable por cualquier agente del equipo (Julián, Mercedes, JARVIS).

---

## Posición en el Flujo SDD

Este servicio NO es una fase adicional del SDD, sino un **motor transversal** que se alimenta de todo el flujo. Su mayor valor está en:

```
FASE 0: Captura de Conocimiento del Stakeholder
┌─────────────────────────────────────────────────────────────┐
│  Documentos stakeholders → Ingesta → Notebook → Chat RAG         │
│  Audios reuniones        → Whisper→                             │
│  Emails, imágenes       → OCR/Vision→  → Sintesis → Fase 1      │
│                                                             │
│  Ej: "¿Qué requisitos no funcionales mencionó Pablo?"        │
│  → Respuesta con cita al audio/reunion-kickoff.mp3 (minuto 12)   │
└─────────────────────────────────────────────────────────────┘
       │
       ▼
FASE 1: CONSTITUTION.md (se alimenta de la síntesis del notebook)
       │
       ▼
FASE 2: OpenAPI Spec (notebook indexa el spec como fuente)
       │
       ▼
FASE 3: Desarrollo (decisiones técnicas quedan como notas en el notebook)
       │
       ▼
FASE 4+: Operación/Mantenimiento (notebook como documentación viva)
```

**Fase 0** es donde más valor entrega porque es el momento donde Nelson recibe material de stakeholders (documentos, audios, mails) y antes de escribir código necesita entender qué se pidió. El notebook automatiza esa síntesis.

Después, durante todo el ciclo de vida, el notebook sigue creciendo y se convierte en **documentación viva** consultable por cualquier agente o humano nuevo en el proyecto.

---

## Qué tomamos de Open Notebook

| Concepto Open Notebook | Adaptación al flujo SDD |
|------------------------|------------------------|
| **Notebook** como contenedor de fuentes + notas | Cada proyecto en `~/brainstorming/` se convierte en un notebook activo |
| **Ingesta multi-formato** | Specs (OpenAPI, markdown), documentos stakeholders, transcripts reuniones, imágenes/diagramas |
| **Chat con contexto (RAG)** | Preguntarle a JARVIS: "¿Qué dice el spec sobre autenticación?" con cita al archivo |
| **Vector search** | Buscar semánticamente en todo el corpus del proyecto |
| **Transformaciones** | Generar Constitution.md, SDD, estimaciones, briefings automáticos |
| **Podcast multi-speaker** | Generar audio-briefing del estado del proyecto para stakeholders |

---

## Arquitectura Propuesta (Integración con Stack Existente)

```
┌─────────────────────────────────────────────────────────────┐
│                    SDD NOTEBOOK ENGINE                        │
├─────────────────────────────────────────────────────────────┤
│  Fuente                    Ingesta              Notebook      │
│  ─────────────────────────────────────────────────────────  │
│  specs/openapi.yaml   →   MarkItDown  →     chunks + meta   │
│  docs/constitution.md →   Chunking      →     Qdrant        │
│  audio/reunion.mp3    →   Whisper     →     transcript      │
│  img/diagrama.png     →   Vision OCR  →     descripción     │
│  emails/              →   Texto plano →     embeddings      │
│                                                             │
│  Chat Interface (JARVIS): RAG sobre el notebook del proyecto  │
│  Transformaciones: resumen, extracción, inconsistencias     │
│  Generative Output: Constitution, SDD, briefing, podcast    │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         PostgreSQL       Qdrant          File System
        (metadatos)    (embeddings)    (~/brainstorming/)
```

**Stack aprovechando lo que ya tenemos:**
- **Ingesta:** `nelson-document-processing` (MarkItDown + Chunking)
- **Embeddings:** `nelson-embeddings` (OpenAI / SentenceTransformers)
- **Vector DB:** `nelson-vector-databases` (Qdrant ya corriendo)
- **LLM:** `nelson-llm-generation` (OpenRouter, OpenAI, local)
- **Audio:** `nelson-audio-processing` (Whisper + edge-tts)
- **Backend:** FastAPI (ya es el estándar)
- **Frontend:** React + Vite (ya es el estándar)

**NUEVO a construir:**
- Modelo `Notebook` (proyecto = notebook)
- Modelo `Source` (archivo ingestado con metadata)
- Modelo `Note` (notas del equipo, decisiones, anotaciones)
- Chat RAG scopado por `notebook_id`
- Motor de transformaciones (resumen, extracción, inconsistencias)
- Generador de podcast/briefing multi-speaker (desacoplado de Open Notebook)

---

## Flujo de Uso

### 1. Crear Proyecto = Crear Notebook
```bash
# Nelson ejecuta el flujo SDD normal
~/brainstorming/2026-06-04-mi-proyecto/
  ├── README.md          (constitución)
  ├── specs/
  │   ├── openapi.yaml
     └── sdd.md
  ├── docs/
  │   └── stakeholder-email.pdf
  ├── audio/
  │   └── reunion-kickoff.mp3
  └── img/
      └── diagrama-arquitectura.png

# JARVIS automáticamente crea el notebook en Qdrant
POST /api/notebooks → {project_id, path, sources: [...]}
```

### 2. Ingesta Automática
- Todo archivo nuevo en la carpeta del proyecto se ingesta automáticamente.
- Whisper transcribe audios.
- Vision OCR describe imágenes.
- MarkItDown procesa PDFs/Docs.
- Chunking + embeddings → Qdrant.

### 3. Consultar el Proyecto (Chat con Contexto)
```
Tony: "JARVIS, ¿qué dijo Pablo sobre el límite de registros en la reunión?"

JARVIS: "En la reunión del 2026-06-03 (audio/reunion-kickoff.mp3), 
Pablo estableció que el límite de registros por archivo Excel 
debe ser de 10.000 filas. Cita: '...no queremos que el sistema 
se trabe con archivos de más de 10.000 filas...'"
```

### 4. Transformaciones
```
Tony: "Generame el SDD a partir de la constitución y el spec"

JARVIS: Procesa Constitution.md + openapi.yaml → Genera SDD.md 
con secciones: Overview, Arquitectura, APIs, Modelos, Seguridad.
```

### 5. Briefing Auditivo (Podcast)
```
Tony: "Generame un briefing del proyecto para Pablo"

JARVIS: Resume el notebook → Script multi-speaker → edge-tts 
(voces: "Experto Técnico" y "Project Manager") → MP3
```

---

## Anti-Patterns a Evitar

1. **NO reemplazar el filesystem** — El notebook es un índice sobre `~/brainstorming/`, no un reemplazo. Los archivos siguen ahí.
2. **NO duplicar el orchestrator** — El notebook engine es un *servicio de documentación*, no un orquestador de tareas. El meta-orchestrator sigue siendo el cerebro.
3. **NO bloquear el flujo SDD** — La ingestas es *asíncrona* y *opt-in*. El flujo SDD funciona igual sin el notebook.
4. **NO vendor lock-in** — Si Qdrant falla, todo el corpus sigue en archivos markdown legibles.

---

## Spike Propuesto (2-4 horas)

1. **Crear modelo `Notebook`** en FastAPI con SQLAlchemy (PostgreSQL).
2. **Ingesta de un proyecto existente** (ej: ForestAI o Expreso Bisonte) en Qdrant.
3. **Chat RAG scopado** — endpoint `/api/notebooks/{id}/chat` que solo consulta chunks de ese proyecto.
4. **Generar una transformación** — resumen ejecutivo automático del proyecto.
5. **Validar con Nelson** — si le sirve, se integra al flujo SDD como Fase 0 (Documentación Activa).

---

## Criterios de Activación

- Nelson dice: "quiero poder preguntarle a JARVIS sobre cualquier proyecto sin tener que releer los docs".
- El equipo crece y necesita que nuevos agentes (o humanos) entren rápido a un proyecto.
- Stakeholders piden resúmenes automáticos o briefings auditivos.
- Se detecta que specs y documentos se desfasan y hace falta un "análisis de consistencia" automático.
