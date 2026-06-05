# Molmo2 — Evaluación Ejecutiva

| Campo | Valor |
|-------|-------|
| **Repo** | https://github.com/allenai/molmo2 |
| **Autor** | AllenAI (Ai2) |
| **Licencia** | Apache-2.0 |
| **Stars** | 587 |
| **Forks** | 38 |
| **Lenguaje** | Python |
| **Creado** | 2025-12-15 |
| **Último update** | 2026-06-05 |

---

## ¿Qué es?

VLM (Vision-Language Model) open-source de AllenAI. Combina visión + lenguaje con capacidades de **pointing** (apuntar objetos en imágenes/video con coordenadas precisas).

Dos variantes:
- **Molmo2** — comprensión de imagen/video, captioning, QA visual.
- **MolmoPoint** — extensión con arquitectura propia para pointing espacial.

---

## Stack técnico

- Python ≥ 3.11
- PyTorch
- torchcodec (video decoding)
- Transformers (HF) + vLLM para inferencia rápida
- Docker image disponible: `ghcr.io/allenai/molmo2:latest`

---

## ¿Nos sirve?

| Caso de uso | Veredicto |
|-------------|-----------|
| **ForestAI hoy** | NO. Ya cubrimos segmentación con SAM + YOLO + índices multiespectrales. |
| **PoCs I+D+I** | **RADAR**. Si surge caso de anotación semiautomática con lenguaje natural (*"marcá los pinos enfermos"*), MolmoPoint tiene ventaja sobre SAM puro. |
| **Producción** | NO. 587 estrellas = muy verde. Qwen-VL o LLaVA son más maduros para cliente. |

---

## Feature distintiva

**Pointing con lenguaje natural** — el usuario describe en texto lo que quiere señalar, y el modelo devuelve coordenadas (x, y) en la imagen. Esto es diferente a SAM (necesita puntos de entrada) o YOLO (clases fijas).

**Potencial:** en ForestAI, un operador podría decir *"señalá los árboles con bajo NDVI"* y MolmoPoint marcaría las ubicaciones sin entrenar un detector específico.

---

## Recomendación

- **Hoy:** standby. No integrar.
- **Re-evaluar:** cuando tengamos un caso concreto de anotación/QA visual en campo que SAM/YOLO no cubran con elegancia.
- **Mantener en radar:** AllenAI es sólida, el repo se actualiza activamente.

---

## Recursos

- Paper: https://arxiv.org/abs/2601.10611
- Blog: https://allenai.org/blog/molmo2
- HF Models: https://huggingface.co/collections/allenai/molmo2
- HF Datasets: https://huggingface.co/collections/allenai/molmo2-data
