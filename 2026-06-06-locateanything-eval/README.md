# LocateAnything-3B — Evaluación rápida

**Fecha:** 2026-06-06
**Fuente:** https://huggingface.co/nvidia/LocateAnything-3B
**Estado:** Pendiente de PoC
**Trigger:** Nelson preguntó qué opino sobre el modelo

## TL;DR

VLM 3B de NVIDIA para visual grounding (boxes, points, refer expressions).
Compacto (7.6GB BF16), corre local, mucho traction (1.4k likes, 111k downloads).
**Bloqueante crítico:** Licencia NVIDIA non-commercial only. No sirve para
producción comercial en ForestAI, Expreso Bisonte ni AlegentAI.

## Ficha técnica

| Campo | Valor |
|---|---|
| Autor | NVIDIA (familia Eagle VLM) |
| Release | 2026-05-26 (paper arxiv 2605.27365) |
| Tamaño | 3B params (7.6GB BF16) |
| Base LM | Qwen2.5-3B-Instruct |
| Vision encoder | MoonViT-SO-400M (MIT) |
| Pipeline | image-text-to-text (Transformers, vLLM, SGLang, llama.cpp) |
| Licencia | NVIDIA Non-Commercial (research only) |
| Likes / Downloads | 1.4k / 111k |
| Custom code | Sí (trust_remote_code requerido) |

## Capacidades (lo que hace)

- **Referring expression grounding** — "el árbol a la derecha del camino"
- **Dense object detection** — escenas con muchos objetos
- **GUI element grounding** — para agentes de desktop (ScreenSpot-Pro)
- **Layout grounding + OCR localization** — documentos, facturas, pantallas
- **Point-based localization** — point prompting tipo SAM
- **Multi-object detection** en una pasada

**Innovación clave:** Parallel Box Decoding (PBD) — predice boxes completos
en paralelo en vez de autoregresivo → más rápido que Grounding DINO
manteniendo coherencia geométrica.

## Datasets de entrenamiento

- 12M imágenes, 138M queries, 785M bounding boxes
- Multi-dominio: natural scenes, robotics, driving, GUI, documents
- Cuatro etapas: captioning/VQA → grounding/dense-scene fine-tune

## Veredicto ejecutivo

### ✅ Vale la pena si
- PoC interno o research
- Caso de uso agentic (GUI grounding) — brilla acá
- Complemento a SAM en ForestAI (pointing + refer expressions)
- Pipeline de OCR/layout en Expreso Bisonte

### ❌ No sirve si
- Va a producción comercial → license non-commercial
- Necesitás peer review → preprint mayo 2026
- No querés trust_remote_code en tu stack

## Alternativas comerciales

| Modelo | Licencia | Notas |
|---|---|---|
| Grounding DINO | Apache 2.0 | Estándar actual, Apache OK |
| Florence-2 | MIT | Microsoft, multimodal |
| SAM 2 | Apache 2.0 | Solo segmentation, no refer |
| OWL-V2 | Apache 2.0 | Open-vocabulary detection |

## Match con proyectos Nelson

### ForestAI (Alegent)
- **Actual:** SAM para segmentación de copas
- **Con LocateAnything:** agregar pointing + refer expressions sobre ortofotos
  ("segmentá el árbol más alto de la parcela 7")
- **Bloqueante:** license — solo investigación

### Expreso Bisonte
- **Caso de uso:** layout grounding + OCR en PDFs de facturación
- **Acción:** experimentar en sandbox, si funciona bien evaluar Florence-2
  o Grounding DINO para producción

### nelson-browser-agent
- **Caso de uso:** GUI grounding para clickear botones correctamente
- **Alto potencial** — ScreenSpot-Pro es exactamente este dominio
- **Bloqueante:** license non-commercial — usar Florence-2 en producción

## Próximos pasos (cuando se reactive)

1. [ ] Bajar el modelo y probar en imagen de muestra de ForestAI
2. [ ] Probar en screenshot de desktop (caso browser agent)
3. [ ] Medir latencia vs Grounding DINO
4. [ ] Si resultados buenos → buscar alternativa con license comercial
   (Florence-2 / OWL-V2) y portar el approach

## Referencias

- HF: https://huggingface.co/nvidia/LocateAnything-3B
- Demo: https://huggingface.co/spaces/nvidia/LocateAnything
- Paper: https://research.nvidia.com/labs/lpr/locate-anything/LocateAnything.pdf
- GitHub: https://github.com/NVlabs/Eagle/tree/main/Embodied
- Tech report: arxiv 2605.27365 (preprint, sin peer review)
