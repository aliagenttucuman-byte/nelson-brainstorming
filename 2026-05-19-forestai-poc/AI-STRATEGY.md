# ForestAI — Estrategia de Inteligencia Artificial

> Documento creado: 2026-05-21
> Autor: JARVIS / Equipo Nelson

---

## Vision General

ForestAI puede evolucionar de un sistema de visualizacion satelital a un **Agente de Inventario Forestal** que combina:
- Image Analytics sobre fotos de drone de alta resolucion
- NDVI / telemetria satelital (Sentinel-2 via CDSE)
- RAG conversacional sobre la base de datos del bosque

El resultado: un guardabosques digital que ve el bosque, lo entiende y responde preguntas.

---

## 3 Modulos de IA

### Modulo 1 — Image Analytics (Computer Vision)

**Que hace:** Analiza fotos aereas de drone para detectar, segmentar y contar arboles automaticamente.

**Pipeline:**
```
Foto drone (alta resolucion, >2MP, RGB o multiespectral)
  → Pre-procesamiento (normalizacion, tile splitting si es ortomosaico)
  → Deteccion de copas (YOLOv8 / Detectron2)
  → Segmentacion individual (Segment Anything Model - SAM de Meta)
  → Extraccion de features por arbol:
      - Diametro de copa (metros)
      - Altura estimada (con DEM o sombra)
      - Estado sanitario (color, textura)
      - Deteccion de arboles muertos / enfermos
  → Salida: GeoJSON con poligono + atributos por arbol
```

**Requerimientos de imagen:**
- Resolucion minima: 5 cm/pixel (drone a ~80m de altura)
- Formato: GeoTIFF con georreferencia, o JPG + coordenadas GPS
- Optimo: imagen multiespectral (agrega banda NIR para calcular NDVI local por arbol)
- Solapamiento para ortomosaico: 80% frontal, 60% lateral

**Modelos candidatos:**
| Modelo | Tarea | Notas |
|--------|-------|-------|
| YOLOv8 | Deteccion de copas | Rapido, fine-tunable con pocas imagenes |
| SAM (Meta) | Segmentacion de copas | Zero-shot, no requiere entrenamiento |
| DeepForest | Deteccion especializada en arboles | Dataset forestal pre-entrenado |
| TreeDetection (Rasterio) | Conteo clasico por umbral | Fallback sin GPU |

**Impacto en el negocio:**
- Inventario de 100 ha en minutos vs semanas a mano
- Diferencial competitivo vs Treemetrics ($3.8M/año)
- Base para calcular carbon capture, valor maderero, estado sanitario

---

### Modulo 2 — Estimacion de Variables Dasometricas

**Que hace:** Desde la imagen y el NDVI, estima variables del inventario sin medicion terrestre.

**Variables estimables:**
```
- Diametro de copa (directa desde segmentacion)
- Altura dominante (con modelo de elevacion o longitud de sombra)
- DAP estimado (Diametro a Altura de Pecho) via correlacion copa/DAP
- Volumen maderable (con ecuaciones de la especie)
- Estado sanitario: 0-100 score (color + NDVI local + textura)
- Clase de edad estimada (tamanio + especie)
```

**Fusion de datos:**
```
Imagen drone + Sentinel-2 NDVI + DEM (modelo elevacion)
  → Feature extraction por arbol
  → Modelo de regresion (XGBoost / Random Forest)
  → Salida: tabla de inventario dasometrico estimado
```

**Precision esperada:**
- Conteo de arboles: 90-95% (estado del arte con SAM)
- DAP estimado: +/- 15% (suficiente para inventario de gestion)
- Estado sanitario: clasificacion 3 clases (sano/estresado/muerto): ~85%

---

### Modulo 3 — Agente Conversacional de Inventario

**Que hace:** RAG sobre la base de datos del bosque. El cliente o guardabosques hace preguntas en lenguaje natural.

**Ejemplos de queries:**
```
"¿Cuantos pinos hay en el lote 3?"
"¿Que parcelas bajaron el NDVI mas del 10% este mes?"
"¿Cuando fue el ultimo relevamiento de la zona norte?"
"¿Cuales son los 5 arboles con peor estado sanitario?"
"Genera un reporte PDF del inventario de esta semana"
"¿Cuanto carbono capturo este bosque en el ultimo ano?"
```

**Arquitectura:**
```
Usuario (natural language)
  → LLM con Tool Use (GPT-4o / Claude / llama local)
  → Tools disponibles:
      - query_inventory(sql_like) → consulta la DB
      - get_ndvi_history(parcela, rango_fechas) → telemetria
      - get_tree_details(tree_id) → atributos del arbol
      - generate_report(tipo, filtros) → PDF/Excel
      - list_alerts() → arboles en estado critico
  → Respuesta en lenguaje natural + datos adjuntos
```

**Stack:**
- LangChain o LlamaIndex para el agente
- PostgreSQL como backend de datos
- pgvector para busqueda semantica sobre notas/reportes
- LLM: Claude Sonnet (API) o llama3 local (privacidad del cliente)

**Experiencia de usuario:**
- Chat embebido en el dashboard web existente
- Posibilidad de consultar por WhatsApp (integracion futura)
- Exportacion directa desde el chat: "generame el Excel"

---

## Roadmap de Implementacion

### Fase 0 — Ya hecho (PoC actual)
- [x] Mapa interactivo con parcelas
- [x] NDVI via Sentinel-2 / CDSE
- [x] Stack backend FastAPI + React

### Fase 1 — Quick Win (2-3 semanas)
**Agente conversacional basico**
- RAG sobre datos de la DB existente
- 5-10 queries de ejemplo funcionando
- Chat embebido en el dashboard
- **Impacto demo:** muy alto, efecto WOW inmediato

### Fase 2 — Diferencial tecnico (1-2 meses)
**Image Analytics MVP**
- Upload de fotos de drone
- SAM para segmentacion de copas
- Conteo automatico + poligonos en el mapa
- Estimacion de diametro de copa
- **Requiere:** fotos de drone reales de Pablo para calibrar

### Fase 3 — Producto completo (3-6 meses)
**Inventario dasometrico automatizado**
- Pipeline completo imagen → inventario
- Fine-tuning con datos del noroeste argentino (especies locales)
- Fusion Sentinel-2 + drone
- Reportes automaticos
- Integracion WhatsApp para el campo

---

## Consideraciones Tecnicas

### Calidad de imagen — CRITICO
Para que el image analytics funcione bien:
- **Drone:** DJI Phantom 4 Multispectral ideal (tiene banda NIR)
- **Altura de vuelo:** 80-120m para 5cm/pixel
- **Condiciones:** dia claro, sol alto (evitar sombras largas)
- **Formato de entrega:** ortomosaico GeoTIFF o fotos con GPS embebido

Si las fotos son de mala calidad → el modelo falla → no hay magia que lo salve.
Definir con Pablo el protocolo de vuelo ANTES de desarrollar.

### Privacidad de datos
- Los datos de inventario son CONFIDENCIALES para el cliente
- Opcion de LLM local (llama3 via Ollama en el servidor) para clientes que no quieren datos en la nube
- Las imagenes de drone no salen del servidor

### Hardware
- GPU necesaria para SAM y YOLOv8 en produccion
- Para el PoC: CPU es suficiente con imagenes pequenas (< 50 arboles)
- El servidor actual (ai-server) tiene capacidad para esto

---

## Propuesta de Valor Diferencial

| Competidor | Que hace | Precio | Nosotros |
|-----------|----------|--------|---------|
| Treemetrics | Computer vision forestal | $3.8M/año empresa | 1/10 del precio + asistente IA |
| Silviaterra | Inventario satelital | $$$$ | Drone + satelite + chat |
| Foresty | App de campo manual | $$ | Automatizacion total |

**Nuestro pitch:** "El primer inventario forestal que se hace solo y se puede consultar hablando"

---

## Proximos Pasos

- [ ] Conseguir fotos de drone de prueba de Pablo (o zona publica)
- [ ] Spike: SAM sobre imagen de copas (validar precision)
- [ ] Spike: Agente conversacional con 5 queries sobre datos mock
- [ ] Definir protocolo de vuelo con el equipo de drones
- [ ] Presentar esta estrategia a Pablo en reunion
