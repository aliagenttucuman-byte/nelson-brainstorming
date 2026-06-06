# Apache Kafka — Evaluación de adopción

**Fecha:** 2026-06-05
**Repo:** https://github.com/apache/kafka
**Status al evaluar:** v3.9.0 (Oct 2024), 29.5k⭐, Java, Apache 2.0
**Mantenedor:** Apache Software Foundation

---

## ¿Qué es?

Apache Kafka es una plataforma de **event streaming distribuido** open-source.

- Usado por miles de empresas para pipelines de datos, streaming analytics, integración entre sistemas
- v3.9.0 es la última que soporta ZooKeeper. De acá en adelante solo **KRaft** (sin ZooKeeper)
- Se consume desde Python vía `confluent-kafka` o `aiokafka`

**Casos de uso típicos:**
- Pipelines de datos en tiempo real
- Integración entre sistemas (TMS, ERP, WMS)
- Telemetría IoT / sensores
- Event sourcing y CQRS

---

## Casos evaluados

| Caso de uso | Veredicto | Notas |
|-------------|-----------|-------|
| Bisonte (Excel→Excel batch actual) | ❌ No | Es procesamiento batch estático. Kafka overkill total. |
| ForestAI (procesamiento de ortofotos) | ❌ No | No es streaming, es análisis por lote. Celery alcanza. |
| **Bisonte fase 2** (remitos en tiempo real, integración TMS) | ✅ **Sí encaja** | Cuando cada remito generado dispare el pipeline automáticamente. |
| YPF I+D+I (pipelines sensores/telemetría IoT) | ✅ Sí | Ese es el caso clásico de Kafka. |

---

## Análisis técnico

### Fortalezas
- **Event streaming real**: mensajes persistentes, replay, múltiples consumers
- **Escala horizontal**: millones de eventos/segundo sin cambiar arquitectura
- **Ecosistema enorme**: Kafka Connect, Kafka Streams, ksqlDB
- **KRaft mode**: eliminó ZooKeeper, simplifica operación desde v3.3+

### Debilidades
- **Java** — no encaja con la regla de oro Python backend. El server es Java; Python es solo el cliente.
- **Overhead enorme**: para 400 CDOs y 1400 PFs, Kafka es un cohete para ir a la esquina. Celery + Redis ya cubre el caso.
- **Infra pesada**: mínimo 1 broker + disco persistente. No es `pip install kafka`.
- **Curva operativa**: monitoreo, particiones, consumer groups, offsets. No es trivial.

### ¿Cuándo lo necesitaríamos DE VERDAD?
- Cuando Bisonte quiera recibir **eventos en tiempo real**: cada remito generado en su TMS dispara el pipeline automáticamente (sin subir Excel manualmente).
- Cuando YPF I+D+I tenga un pipeline de telemetría de sensores con miles de eventos/minuto.
- Cuando ForestAI quiera procesar alertas de drones en tiempo real (detección de incendio → alerta inmediata).

---

## Decisión

**No adoptar ahora. Radar 12 meses.**

**Por qué no:**
1. Bisonte hoy es **batch Excel → FastAPI + Celery**. No hay eventos en tiempo real. Kafka no agrega nada.
2. **Java server** no encaja con el stack Python del equipo.
3. El overhead operativo no justifica adoptarlo para PoCs I+D+I de 3-6 meses.

**Por qué re-evaluar en 12 meses:**
- Si Bisonte escala a integración con TMS/ERP de la empresa.
- Si YPF I+D+I tiene caso concreto de telemetría IoT.
- Si aparece un cliente que ya tiene Kafka y quiere integrarse.

---

## Stack actual que reemplaza Kafka para nuestros casos

| Necesidad | Hoy usamos | ¿Cuándo migrar a Kafka? |
|-----------|-----------|------------------------|
| Jobs async en background | Celery + Redis | Cuando necesitemos replay de eventos o múltiples consumers |
| Pipeline batch Excel | FastAPI + Celery | Cuando el cliente quiera integración en tiempo real con su TMS |
| Alertas / notificaciones | WebSockets / Polling | Cuando haya miles de eventos/minuto |

---

## Si se adopta: cómo empezar

```bash
# 1. Levantar Kafka con KRaft (sin ZooKeeper) vía Docker
docker run -d \
  --name kafka \
  -p 9092:9092 \
  -e KAFKA_NODE_ID=1 \
  -e KAFKA_PROCESS_ROLES=broker,controller \
  -e KAFKA_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
  -e KAFKA_CONTROLLER_QUORUM_VOTERS=1@localhost:9093 \
  apache/kafka:3.9.0

# 2. Cliente Python
pip install aiokafka  # async-friendly

# 3. Producir un evento
from aiokafka import AIOKafkaProducer
# producer.send("bisonte-remitos", value=remito_json)

# 4. Consumir y disparar pipeline
from aiokafka import AIOKafkaConsumer
# consumer.subscribe(["bisonte-remitos"])
```

**Tiempo estimado de spike:** 2 días.
**Costo de infra:** 1 container + disco persistente (~512MB RAM mínimo).

---

## Estado

- [x] Evaluación inicial
- [ ] Spike (cuando Bisonte pida integración en tiempo real con TMS)
- [ ] Revisión: 2027-06

**Tags:** `#streaming` `#events` `#kafka` `#radar` `#bisonte-fase2` `#ypf`
