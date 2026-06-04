# Brainstorming: book-to-skill como Referencia de Extracción de Conocimiento

**Fecha:** 2026-06-04  
**Origen:** Nelson (Tony Stark) — investigar si book-to-skill sirve para el flujo de documentación/equipo.  
**Posicionamiento:** Concepto compatible con skills de Hermes. Uso potencial para convertir documentos stakeholders y libros técnicos en skills estructuradas.  
**Estado:** Idea validada como referencia. Pendiente de spike si Nelson activa.

---

## Qué es book-to-skill

Herramienta open source (4.1K ⭐, MIT) que convierte libros técnicos y documentos en **skills estructuradas** para Claude Code / Amp.

**Autor:** virgiliojr94  
**Repo:** https://github.com/virgiliojr94/book-to-skill  
**Stack:** Python, docling, pdftotext, ebooklib

---

## Qué Genera (Estructura de Skill)

| Archivo | Contenido | Tamaño |
|---------|-----------|--------|
| `SKILL.md` | Mental models + índice de capítulos | ~4,000 tokens |
| `chapters/ch01-*.md` | Un archivo por capítulo, on-demand | ~1,000 tokens c/u |
| `glossary.md` | Términos clave con referencias | ~1,500 tokens |
| `patterns.md` | Técnicas, algoritmos, patrones | ~2,000 tokens |
| `cheatsheet.md` | Tablas de decisión y quick-reference | ~1,000 tokens |

**Capítulos se cargan on-demand** — no consumen budget hasta que se consultan.

---

## Diferencia Clave: Skill vs RAG

| Aspecto | RAG | book-to-skill (Skill) |
|---------|-----|----------------------|
| **Momento** | Query time | Compile time |
| **Input** | Chunks de texto | Documento completo |
| **Output** | "Chunks similares a tu query" | "Frameworks del autor estructurados" |
| **Nivel** | Retrieval (buscar) | Reasoning (razonar) |
| **Persistencia** | Embeddings en vector DB | Archivos markdown estructurados |

**RAG responde:** *"Acá tenés chunks parecidos a tu pregunta."*  
**Skill responde:** *"Acá tenés los 12 frameworks que este autor construyó, listos para razonar."*

---

## Formatos Soportados

- PDF (técnico con docling / texto con pdftotext)
- EPUB, DOCX, HTML, Markdown, TXT
- RTF, MOBI/AZW/AZW3 (vía Calibre)

---

## Análisis de Integración

### Meta-Orchestrator / Skills de Hermes
- **Veredicto:** ✅ **Concepto compatible**. Las skills de Hermes ya usan formato SKILL.md. La estructura de salida (SKILL.md + archivos vinculados) es directamente usable.

### Flujo SDD + Documentación Notebook (Fase 0)
- **Veredicto:** ✅ **Complementario**. Mientras el SDD Notebook es runtime RAG sobre todo el corpus del proyecto, book-to-skill es compile-time extraction de documentos individuales en skills reusables.
- **Caso de uso:** Convertir un spec complejo o un libro de referencia en una skill que el equipo consulta mientras trabaja.

### ForestAI / Expreso Bisonte
- **Veredicto:** ⚖️ **Indirecto**. No aplica directamente a los productos, pero sí al conocimiento del equipo (ej: convertir papers de remote sensing en skills).

---

## Casos de Uso Potenciales para AlegentAI

1. **Libros técnicos del equipo** — Nelson lee un libro de arquitectura de sistemas, lo convierte en skill, Julián y Mercedes lo consultan mientras codifican.
2. **Documentación de stakeholders** — Un PDF de 200 páginas de requisitos de YPF se convierte en skill con glosario y patrones.
3. **Specs del flujo SDD** — OpenAPI spec + constitution → skill de referencia rápida.
4. **Papers académicos** — Papers de computer vision / forestry → skill con técnicas y anti-patterns.

---

## Stack Técnico

```
Documento (PDF/EPUB/DOCX)
    │
    ├── técnico → docling (preserva tablas + código, ~1.5s/página)
    └── texto → pdftotext / PyPDF2 / pdfminer (instantáneo)
    │
    ▼
texto plano
    │
    ▼
LLM (Claude) analiza estructura: título, autor, capítulos, ToC
    │
    ▼
Genera: SKILL.md + chapters/ + glossary + patterns + cheatsheet
```

---

## Requisitos para Adaptación al Stack Nelson

- El extractor Python es agnóstico al LLM. Se puede apuntar a cualquier API (OpenRouter, OpenAI, local).
- La salida SKILL.md es compatible con Hermes sin modificaciones.
- Para PDFs técnicos con tablas/código, docling es la mejor opción (ya usado en el stack de document processing).

---

## Spike Propuesto (2-4 horas)

1. **Convertir un libro técnico o spec existente** (ej: spec de ForestAI o doc de stakeholder).
2. **Apuntar el extractor a OpenRouter/Claude** en vez de Claude Code interno.
3. **Validar que la skill generada funcione** en Hermes (`skill_view`, `skill_manage`).
4. **Evaluar si el equipo la consulta** durante sesiones de trabajo.

---

## Criterios de Activación

- Nelson dice: "quiero que el equipo consulte este libro/documento mientras trabaja, sin tener que releerlo".
- Se identifica un documento stakeholder denso que merece ser "skill-ificado".
- El equipo crece y necesita acceso rápido a conocimiento cristalizado de libros/papers.
- Se quiere complementar el SDD Notebook (runtime RAG) con skills estáticas de referencia.
