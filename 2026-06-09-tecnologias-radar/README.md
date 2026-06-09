# Tecnologías en el Radar — AlegentAI / Equipo Nelson

**Fecha:** 2026-06-09
**Contexto:** Sesión de exploración junto a JARVIS. Tecnologías descubiertas con potencial directo para ForestAI, pipeline RAG y agentes del equipo.

---

## 1. OpenCV 5 — Heterogeneous Computing para ForestAI

**Repo:** https://opencv.org/opencv-5/
**Skills:** `nelson-forestai-demo`

### Qué es
La actualización más grande en la historia de OpenCV. API C++17/20 unificada, Python bindings reescritos desde cero, UMat heterogéneo que apunta a CPU/CUDA/OpenCL/Vulkan con el mismo código.

### Por qué importa para ForestAI
- UMat unificado → cuando llegue la GPU, el pipeline de tiling + ExG migra sin reescribir
- DNN module mejorado con transformers e INT8 → posible reemplazo de SAM por algo más liviano
- 20-40% más rápido en CPU → las transformaciones ExG y tiling ganan rendimiento gratis
- Nuevos algoritmos de segmentación → evaluar alternativa a SAM para copas

### Acción
Benchmarkear pipeline tiling+ExG con OpenCV 5 vs implementación numpy actual. Priorizar cuando llegue GPU.

---

## 2. Opik — Evaluación de LLMs (Comet ML)

**Repo:** https://github.com/comet-ml/opik
**Stars:** 5.1k | **Licencia:** Apache 2.0
**Skills:** `nelson-opik-eval`, `nelson-forestai-demo`

### Qué es
Plataforma open-source de tracing, evaluación y experimentos para aplicaciones LLM. Self-hosted con Docker. Dashboard visual, LLM-as-a-Judge, datasets, comparación de modelos/prompts.

### Por qué importa para ForestAI
Hoy el VLM (claude-haiku clasificando especies) no tiene ningún sistema de evaluación. No sabemos si alucina especies ni si la confianza que reporta es real.

Con Opik:
- Tracing de cada llamada → visibilidad de costos, latencia, tokens
- Dataset de árboles etiquetados manualmente → benchmark real
- LLM-as-a-Judge → detectar hallucinations automáticamente
- Comparar haiku vs sonnet en el mismo dataset

### Acción
Integrar en ForestAI antes de propuesta formal a ReforestLatam. Poder mostrar métricas de calidad del clasificador es argumento de venta.

---

## 3. OpenDataLoader PDF — Parser #1 en benchmarks

**Repo:** https://github.com/opendataloader-project/opendataloader-pdf
**Licencia:** Apache 2.0
**Skills:** `nelson-document-processing`

### Qué es
Parser de PDFs para pipelines de IA. #1 en benchmarks (0.907 accuracy general, 0.928 en tablas sobre 200 PDFs reales). Modo local determinístico a 0.015s/página — sin modelos pesados. Modo híbrido para páginas complejas con IA.

### Por qué importa
Hoy usamos Docling + Surya para el pipeline RAG. Surya en CPU tarda 5-15 seg/página. OpenDataLoader promete 0.015s/página en modo local con mejor accuracy.

Salida directa para RAG: Markdown + JSON con bounding boxes por elemento. LangChain integration nativa. Python, Node.js, Java (requiere Java 11+).

### Acción
Benchmarkear vs Docling/Surya con documentos reales del equipo (contratos, reportes MRV, licitaciones). Si supera en velocidad y accuracy → migrar como capa 1 del pipeline RAG.

---

## 4. Gemini Live API — Voz + Video en Tiempo Real

**Repo:** https://github.com/google-gemini/gemini-live-api-examples
**Stars:** 298 | **Licencia:** Apache 2.0
**Skills:** `nelson-gemini-live`, `nelson-forestai-demo`

### Qué es
API de Google para interacciones multimodales en tiempo real con Gemini. WebSocket stateful, latencia ultra baja. Recibe audio (16kHz PCM), video (JPEG 1FPS) y texto en streaming. Responde con voz en tiempo real. Barge-in, tool use, 70 idiomas.

### Caso de uso — Inspector de campo ForestAI
El inspector apunta la cámara a un árbol y habla:
> "Árbol 47, copa densa, sin deterioro visible"

El sistema transcribe, analiza el frame de video, llama `registrar_arbol()` via function calling y confirma por voz. Sin tocar el celular. Sin tipear. En campo.

### Otros usos
- JARVIS con interfaz de voz
- Traducción en tiempo real para reuniones con clientes internacionales
- Agente de voz para demos con stakeholders

### Acción
Prototipo CLI en Python — simular inspector de campo. Validar latencia y calidad de transcripción en español antes de invertir en app mobile.

---

## Resumen de acciones

| Tecnología | Acción inmediata | Prioridad |
|------------|-----------------|-----------|
| OpenCV 5 | Benchmarkear tiling+ExG — cuando llegue GPU | Media |
| Opik | Integrar tracing VLM ForestAI antes de reunión ReforestLatam | Alta |
| OpenDataLoader PDF | Benchmark vs Docling con docs reales | Media |
| Gemini Live API | Prototipo CLI inspector de campo | Media |
