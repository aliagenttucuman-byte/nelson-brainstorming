# Ultralytics YOLO → TensorFlow.js — Evaluación de adopción

**Fecha:** 2026-06-05
**Docs:** https://docs.ultralytics.com/integrations/tfjs
**Status al evaluar:** YOLO26 mencionado en docs, YOLOv8/YOLOv11 son los más estables
**Mantenedor:** Ultralytics

---

## ¿Qué es?

Ultralytics exporta modelos YOLO a **TensorFlow.js** para correr **en el browser** del usuario.

- Detección de objetos / segmentación **client-side** (sin enviar imágenes al servidor)
- 100% JavaScript, sin Python en runtime, sin GPU servidor
- Compatible con la mayoría de browsers modernos (WebGL/WebGPU)

**Casos de uso típicos:**
- Demos interactivas en landing pages
- Apps web/mobile con inferencia on-device
- Prototipos rápidos sin backend de ML

---

## Casos evaluados

| Caso de uso | Veredicto | Notas |
|-------------|-----------|-------|
| ForestAI drone aerial (ortofotos GeoTIFF, server-side) | ❌ No | TFJS es para browser. ForestAI necesita precisión industrial en server con Python + GPU. |
| **ForestAI demo interactiva para inversores (subí foto, la IA detecta)** | ✅ **Sí encaja** | Demo en reunión con Pablo: probar en vivo desde el celu. |
| Bisonte pipeline Excel→Excel | ❌ No | No hay imágenes, son Excel. |
| Spike `nelson-ai-vision` skill del equipo | 🔶 Documentar | Agregar como opción "browser-side inference" al stack. |

---

## Análisis técnico

### Fortalezas
- **Zero infra**: una vez exportado el modelo, no hay servidor de inferencia. Browser del usuario hace todo.
- **Privacidad**: las imágenes nunca salen del dispositivo del usuario. Útil para compliance.
- **Demo inmediata**: ideal para mostrar "esto funciona" sin que el cliente instale nada.
- **Python-first export**: encaja con nuestro stack (regla de oro: Python backend). El export se hace en Python, el runtime es JS.

### Debilidades
- **Lento en CPU**: TFJS en browser sin GPU es **3-10x más lento** que en server con PyTorch/ONNX. No sirve para análisis masivo.
- **YOLO26 todavía no consolidado**: Ultralytics viene de YOLOv5/v8/v11. Esperar 6 meses a ver si YOLO26 se mantiene.
- **Tamaño del bundle**: un modelo YOLO exportado a TFJS pesa **5-30 MB**. Hay que servirlo desde CDN.
- **No es para producción industrial**: es para demo, prototipo, validación visual.

### ¿Cuándo lo necesitaríamos DE VERDAD?
- Cuando Pablo muestre ForestAI a un **cliente nuevo en reunión presencial** y quiera que el cliente **pruebe en vivo** desde su celu.
- Cuando un usuario del ForestAI quiera **"ver cómo detecta un árbol"** en su propia foto, sin subir a ningún server.
- Cuando construyamos una **landing page interactiva** para ForestAI / AlegentAI que demuestre capacidad de visión.

---

## Decisión

**No adoptar ahora. Documentar como opción futura.**

**Por qué no:**
1. **YOLO26 es muy nuevo** — todavía no hay tracción. Mejor YOLOv11 o v8 que están probados.
2. **TFJS en browser es para demo**, no para producción de análisis serio de ortofotos.
3. **No tenemos caso concreto** donde un cliente quiera probar desde el browser. Pablo todavía no llegó a esa instancia con ForestAI.

**Por qué re-evaluar:**
- Cuando ForestAI tenga **cliente nuevo** y la demo en vivo sea un diferenciador.
- Cuando YOLO26 tenga 6+ meses de tracción y sea el estándar de Ultralytics.
- Cuando el bundle de TFJS baje a <5MB (optimizaciones de Ultralytics).

---

## Si se adopta: cómo empezar

```bash
# 1. Exportar YOLO a TFJS desde Python (entorno ForestAI)
pip install ultralytics tensorflowjs
yolo export model=yolo11n.pt format=tfjs

# 2. Output: carpeta yolo11n_web_model/ con shards .bin y model.json

# 3. Hostear en página estática
# - Sube la carpeta a Cloudflare Pages / Vercel / GitHub Pages
# - Carga el modelo con @tensorflow/tfjs
# - Render en <canvas> con bounding boxes

# 4. Demo interactiva para Pablo
# - Landing en forestai.alegentai.com/demo
# - "Subí una foto de un árbol y te lo detecta"
```

**Tiempo estimado de spike:** 1 día.
**Costo de infra:** $0 (TFJS corre en browser, hosting estático free tier).

---

## Diferencia clave a tener clara

| | TFJS en browser | Server Python (PyTorch/ONNX) |
|---|---|---|
| **Caso de uso** | Demo, prototipo, validación visual | Producción, análisis masivo |
| **Velocidad CPU** | 3-10x más lento | Baseline |
| **Velocidad GPU** | 2-5x más lento | Baseline |
| **Privacidad** | ✅ On-device | ❌ Imágenes al server |
| **Costo infra** | $0 | GPU server $$ |
| **Tamaño bundle** | 5-30 MB | N/A (server-side) |
| **Para quién** | Cliente prueba en vivo | Equipo procesa batch |

**Regla de bolsillo:**
- **1-3 objetos en imagen única** → TFJS alcanza
- **Miles de detecciones sobre ortofoto** → Server Python con GPU

---

## Estado

- [x] Evaluación inicial
- [ ] Spike con YOLOv11 → TFJS (cuando Pablo pida demo interactiva)
- [ ] Revisión YOLO26: 6 meses (~2026-12)
- [ ] Documentar como opción en `nelson-ai-vision` skill

**Tags:** `#vision` `#browser-inference` `#demo` `#forestai`
