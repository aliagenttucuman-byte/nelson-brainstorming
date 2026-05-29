# Sistema de Identidad Multi-Plataforma + Memoria Persistente
*Fecha: 2026-05-29 — Sesión de arquitectura con JARVIS*

---

## Qué se trabajó en esta sesión

Diseño completo de la arquitectura para que JARVIS reconozca al mismo usuario
desde cualquier plataforma (WhatsApp, Telegram, Microsoft Teams, Slack, etc.)
y cargue su memoria persistente de forma automática y unificada.

---

## Documentos generados

| Archivo | Descripción |
|---------|-------------|
| `identity-multiplatform-spec.md` | Especificación técnica completa v1.2 |

---

## Resumen ejecutivo

El problema central: hoy cada canal de mensajería genera una "identidad" distinta.
El mismo usuario visto desde WhatsApp, Telegram y Teams es tratado como tres personas
diferentes, y la memoria acumulada en Holographic no se comparte entre canales.

La solución: un sistema en tres capas.

**Capa 1 — Identity Map** (`identity_map.json`)
Archivo JSON que vincula (plataforma, ID de plataforma) → canonical_id interno.
Cada usuario tiene un canonical_id único que es la llave de toda su memoria.
Ningún usuario se registra solo — todo pasa por aprobación del owner.

**Capa 2 — Holographic Memory** (`memory_store.db`)
Plugin de Hermes que usa SQLite + vectores HRR + FTS5.
Con el Identity Map, todos los hechos de un usuario se taggean con su canonical_id
sin importar desde qué canal llegaron. `probe("canonical_id")` devuelve todo el
historial acumulado en todas las sesiones de todos los canales.

**Capa 3 — SIE (Superlinked Inference Engine)**
Servidor de inferencia open source que potencia tres puntos del pipeline:
- `extract` (GLiNER): extrae entidades automáticamente de cada mensaje → taggeo automático en Holographic
- `encode` (BGE-M3): embeddings de calidad MTEB para reemplazar HRR básico → mejor búsqueda semántica
- `score` (BGE-reranker): reranking de hechos antes de armarle el contexto a JARVIS → menos tokens, más precisión

---

## Stack técnico

| Componente | Tecnología |
|------------|------------|
| Identity Map | `identity_map.json` + Python `IdentityResolver` |
| Memoria profunda | Hermes Holographic (`memory_store.db`, SQLite + FTS5 + HRR) |
| Embeddings | SIE server + BAAI/bge-m3 (dense + sparse) |
| Extracción de entidades | SIE + urchade/gliner_multi-v2.1 |
| Reranking | SIE + BAAI/bge-reranker-v2-m3 |
| Vector DB | Qdrant (backend alternativo a SQLite para escala) |
| Deploy | Docker en ai-server (Tailscale: 100.110.8.13) |

---

## Microsoft Teams — punto clave

Teams tiene dos IDs distintos. El campo `from.id` (formato `29:1XXXX`) es inestable.
El campo a usar siempre es `from.aadObjectId` — el UUID de Azure Active Directory,
inmutable mientras exista la cuenta. También capturar `channelData.tenant.id`
para soporte multi-tenant.

---

## Flujos implementados

1. **Primera vez** — usuario desconocido → pending_approval → notificación al owner → aprobación → canonical_id creado
2. **Sesión posterior** — resolve(platform, id) → canonical_id → probe Holographic → contexto cargado
3. **Nueva plataforma** — usuario conocido desde canal nuevo → VINCULAR al canonical_id existente

---

## Fases de implementación

| Fase | Descripción | Costo estimado |
|------|-------------|----------------|
| 1 | identity_map.json manual + Holographic activo | 1-2 hs |
| 2 | Módulo Python completo + hook de gateway | 4-6 hs |
| 3 | PR a Hermes + comandos nativos `hermes identity` | 2-3 días |

---

## SIE en el SDD

| Fase SDD | Rol de SIE |
|----------|------------|
| SPEC | Definir labels de entidades a extraer |
| DESIGN | SIE como servicio de soporte: extract + encode + score |
| IMPL Backend | `sie-sdk` en hook `on_message_received` |
| IMPL JARVIS | `sie.score()` como reranker antes de armar el prompt |
| VERIFICATION | Validar precisión de extracción de entidades por plataforma |

---

## Próximos pasos

- [ ] Activar Holographic como provider: `hermes config set memory.provider holographic`
- [ ] Crear `identity_map.json` con los IDs conocidos (Fase 1)
- [ ] Conectar Teams al gateway de Hermes → capturar primer `aadObjectId`
- [ ] Deploy de SIE server en Docker en ai-server
- [ ] Implementar `identity/resolver.py` (Fase 2)

---

## Referencia rápida

```bash
# Activar Holographic
hermes config set memory.provider holographic
hermes memory status

# Deploy SIE (CPU)
docker run -d --name sie-server -p 8080:8080 \
  ghcr.io/superlinked/sie-server:latest-cpu-default

# Verificar SIE
curl http://localhost:8080/health
```

---

*Generado por JARVIS — Sesión 2026-05-29*
