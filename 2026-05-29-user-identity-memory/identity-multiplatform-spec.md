# Sistema de Identidad Multi-Plataforma para Agentes IA
## Especificación Técnica — Memoria Persistente Unificada

*Versión 1.0 — Enfoque principal: Microsoft Teams*

---

## 1. Problema a Resolver

Un agente IA puede recibir mensajes desde múltiples plataformas de mensajería:
Microsoft Teams, WhatsApp, Telegram, Slack, Discord, Signal, entre otros.

Cada plataforma asigna su propio identificador interno al usuario.
Sin un sistema de identidad unificada, el agente trata cada canal como un usuario diferente,
perdiendo todo el contexto acumulado en sesiones anteriores.

**El objetivo:** que el agente reconozca a la misma persona sin importar desde
qué plataforma escriba, y cargue su memoria persistente de forma automática.

---

## 2. Concepto Central — Identity Map

El Identity Map es un registro que vincula múltiples identidades de plataforma
a un único identificador canónico interno.

```
canonical_id: "usuario-001"
    │
    ├── teams:    aadObjectId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ├── telegram: user_id     = "123456789"
    ├── whatsapp: phone       = "5491112345678"
    └── slack:    user_id     = "U0XXXXXXXXX"
```

Cuando llega un mensaje, el sistema resuelve el identificador de plataforma
al identificador canónico y usa ese como llave para cargar la memoria del usuario.

---

## 3. Identificadores por Plataforma

Cada plataforma expone el ID del usuario de forma diferente:

| Plataforma        | Campo a usar              | Formato típico                               | Estabilidad |
|-------------------|---------------------------|----------------------------------------------|-------------|
| Microsoft Teams   | `from.aadObjectId`        | UUID: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | Permanente  |
| Telegram          | `message.from.id`         | Entero: `123456789`                          | Permanente  |
| WhatsApp (Baileys)| JID del número            | `5491112345678@s.whatsapp.net`               | Permanente  |
| Slack             | `event.user`              | `U0XXXXXXXXX`                                | Permanente  |
| Discord           | `message.author.id`       | Snowflake: `123456789012345678`              | Permanente  |
| Signal            | Número de teléfono        | `+5491112345678`                             | Permanente  |
| Microsoft Teams*  | `from.id` (interno)       | `29:1XXXXX...`                               | ⚠️ Inestable |

> **Nota Teams:** Existen DOS identificadores en Teams. El campo `from.id` es
> el ID interno del canal y puede cambiar entre tenants o reinstalaciones.
> Siempre usar `from.aadObjectId` — es el ID de Azure Active Directory
> y no cambia nunca mientras exista la cuenta.

---

## 4. Microsoft Teams — Detalle Técnico

### 4.1 Estructura del payload entrante

Cuando un usuario escribe al bot de Teams, el framework Bot Framework entrega
una actividad con la siguiente estructura relevante:

```json
{
  "type": "message",
  "text": "Hola, ¿cómo estás?",
  "from": {
    "id": "29:1XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "name": "Nombre Apellido",
    "aadObjectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  },
  "channelData": {
    "tenant": {
      "id": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"
    }
  },
  "channelId": "msteams",
  "conversation": {
    "id": "a:1XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "conversationType": "personal"
  }
}
```

### 4.2 Campos a capturar y almacenar

```
from.aadObjectId     → identificador principal del usuario (NUNCA cambia)
from.name            → nombre display (referencial, puede cambiar)
channelData.tenant.id → ID del tenant de Azure AD (para multi-tenant)
```

### 4.3 Por qué NOT usar `from.id`

El campo `from.id` en Teams tiene el formato `29:1XXXX...`. Este identificador:
- Es interno al canal de Teams
- Puede diferir entre entornos (dev, staging, prod)
- Puede cambiar si el bot se reinstala o se mueve entre tenants
- No tiene relación directa con la identidad de Azure AD del usuario

El `aadObjectId` es el UUID de la cuenta en Azure Active Directory.
Es inmutable mientras exista la cuenta en el directorio.

---

## 5. Estructura del Identity Map

### 5.1 Archivo — `identity_map.json`

Ruta recomendada: `~/.hermes/identity_map.json`  
Permisos: `600` (lectura/escritura solo para el owner del proceso)

```json
{
  "version": "1.0",
  "last_updated": "2026-01-01T00:00:00Z",
  "identities": [
    {
      "canonical_id": "usuario-001",
      "display_name": "Nombre del usuario",
      "role": "owner",
      "approved": true,
      "approved_at": "2026-01-01T00:00:00Z",
      "memory_key": "usuario-001",
      "permissions": ["all"],
      "platforms": {
        "teams": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "teams_tenant": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy",
        "telegram": "123456789",
        "whatsapp": "5491112345678",
        "slack": null,
        "discord": null
      }
    },
    {
      "canonical_id": "usuario-002",
      "display_name": "Segundo usuario",
      "role": "team",
      "approved": true,
      "approved_at": "2026-01-01T00:00:00Z",
      "memory_key": "usuario-002",
      "permissions": ["chat", "query"],
      "platforms": {
        "teams": "zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
        "teams_tenant": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy",
        "telegram": "987654321",
        "whatsapp": null,
        "slack": null,
        "discord": null
      }
    }
  ],
  "pending_approval": []
}
```

### 5.2 Campos del objeto de identidad

| Campo          | Tipo     | Descripción                                                  |
|----------------|----------|--------------------------------------------------------------|
| `canonical_id` | string   | ID único interno. Inmutable. Usar kebab-case sin espacios.   |
| `display_name` | string   | Nombre legible para logs y notificaciones.                   |
| `role`         | string   | `"owner"` / `"team"` / `"external"`. Define nivel de acceso.|
| `approved`     | boolean  | Solo se carga memoria si es `true`.                          |
| `approved_at`  | ISO 8601 | Timestamp de aprobación.                                     |
| `memory_key`   | string   | Llave usada en el backend de memoria. Igual a `canonical_id`.|
| `permissions`  | array    | Lista de acciones permitidas para el rol.                    |
| `platforms`    | object   | Mapa de plataforma → ID en esa plataforma. `null` = no registrado. |

---

## 6. Flujos de Sesión

### 6.1 Primera vez que un usuario escribe (usuario desconocido)

```
┌──────────────────────────────────────────────────────────────┐
│  PLATAFORMA: usuario envía primer mensaje                     │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  GATEWAY: extrae (platform, platform_user_id)                 │
│  Ejemplo Teams:                                               │
│    platform         = "teams"                                 │
│    platform_user_id = from.aadObjectId                        │
│    display_name     = from.name                               │
│    tenant_id        = channelData.tenant.id                   │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  IDENTITY RESOLVER: buscar en identity_map.json               │
│  lookup("teams", "xxxxxxxx-xxxx-...")                         │
│  → RESULTADO: no encontrado                                   │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  PENDING APPROVAL: agregar entrada en pending_approval[]      │
│  {                                                            │
│    "id": "pending-abc123",                                    │
│    "platform": "teams",                                       │
│    "platform_user_id": "xxxxxxxx-xxxx-...",                   │
│    "display_name": "Nombre Apellido",                         │
│    "tenant_id": "yyyyyyyy-yyyy-...",                           │
│    "first_seen": "2026-01-01T10:00:00Z",                     │
│    "message": "Hola, ¿cómo estás?"                           │
│  }                                                            │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  NOTIFICACIÓN AL OWNER (por el canal configurado como home)   │
│                                                               │
│  "Nuevo acceso pendiente:                                     │
│   Plataforma: Microsoft Teams                                 │
│   Nombre detectado: Nombre Apellido                           │
│   AAD ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx               │
│   Tenant: yyyyyyyy-yyyy-...                                    │
│   Mensaje: 'Hola, ¿cómo estás?'                              │
│                                                               │
│   Responder: APROBAR <id-pendiente> <alias>                  │
│   Ejemplo: APROBAR pending-abc123 juan-dev"                   │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  RESPUESTA AL USUARIO PENDIENTE (en la plataforma donde       │
│  intentó conectarse):                                         │
│                                                               │
│  "Hola, tu solicitud de acceso fue recibida.                 │
│   Estás pendiente de aprobación.                             │
│   Te avisaré cuando pueda ayudarte."                         │
└──────────────────────────────────────────────────────────────┘

--- el owner responde APROBAR ---

┌──────────────────────────────────────────────────────────────┐
│  IDENTITY RESOLVER: crear nueva entrada en identities[]       │
│  canonical_id   = "juan-dev" (alias dado por el owner)        │
│  platforms.teams = "xxxxxxxx-xxxx-..."                        │
│  approved       = true                                        │
│  memory_key     = "juan-dev"                                  │
│  Guardar identity_map.json                                    │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  NOTIFICACIÓN AL USUARIO (en su plataforma):                  │
│  "Tu acceso fue aprobado. Ya podés hablar conmigo."          │
└──────────────────────────────────────────────────────────────┘
```

---

### 6.2 Usuario conocido — sesión posterior (el flujo feliz)

```
┌──────────────────────────────────────────────────────────────┐
│  PLATAFORMA: usuario envía mensaje                            │
│  Puede ser cualquier plataforma registrada para ese usuario   │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  GATEWAY: extrae (platform, platform_user_id)                 │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  IDENTITY RESOLVER: buscar en identity_map.json               │
│  lookup("teams", "xxxxxxxx-xxxx-...")                         │
│  → RESULTADO: canonical_id = "juan-dev"                       │
│               memory_key   = "juan-dev"                       │
│               role         = "team"                           │
│               approved     = true                             │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  MEMORY LOADER: cargar memoria con key "juan-dev"             │
│  ← perfil del usuario (preferencias, proyectos, historial)   │
│  ← contexto acumulado de sesiones anteriores                 │
│  ← independientemente del canal desde el que llegó           │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  AGENTE: procesa el mensaje con memoria completa cargada      │
│  Responde con contexto de todas las sesiones anteriores       │
│  sin importar desde qué plataforma se conectó antes          │
└──────────────────────────────────────────────────────────────┘
```

---

### 6.3 Usuario conocido — agrega nueva plataforma

Escenario: el usuario ya está registrado en Telegram, ahora escribe desde Teams por primera vez.

```
┌──────────────────────────────────────────────────────────────┐
│  El usuario escribe desde Teams (plataforma nueva para él)    │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  IDENTITY RESOLVER:                                           │
│  lookup("teams", "xxxxxxxx-xxxx-...")                         │
│  → RESULTADO: no encontrado en platforms.teams                │
│  → Buscar si hay pending_approval con ese ID                  │
│  → No hay pending tampoco                                     │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  NOTIFICACIÓN AL OWNER:                                       │
│  "Nuevo ID de Teams no registrado.                           │
│   Nombre detectado: Juan Dev                                 │
│   AAD ID: xxxxxxxx-xxxx-...                                  │
│                                                               │
│   ¿Es un usuario existente?                                   │
│   VINCULAR pending-xyz juan-dev  (agrega plataforma al exist.)│
│   APROBAR pending-xyz nuevo-alias (crea usuario nuevo)        │
└────────────────────────────┬─────────────────────────────────┘
                             │ VINCULAR
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  IDENTITY RESOLVER:                                           │
│  identities["juan-dev"].platforms.teams = "xxxxxxxx-xxxx-..." │
│  Guardar identity_map.json                                    │
│  A partir de ahora Teams resuelve al mismo canonical_id       │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. Implementación — Módulo Python

### 7.1 Estructura de archivos

```
identity/
├── __init__.py
├── resolver.py          # Lógica principal de resolución
├── storage.py           # Lectura/escritura del identity_map.json
├── approvals.py         # Flujo de aprobación de nuevos usuarios
├── notifier.py          # Envío de notificaciones al owner
└── models.py            # Dataclasses / Pydantic models
```

### 7.2 models.py

```python
from dataclasses import dataclass, field
from typing import Optional
from enum import Enum


class UserRole(str, Enum):
    OWNER    = "owner"
    TEAM     = "team"
    EXTERNAL = "external"


@dataclass
class PlatformIds:
    teams:        Optional[str] = None
    teams_tenant: Optional[str] = None
    telegram:     Optional[str] = None
    whatsapp:     Optional[str] = None
    slack:        Optional[str] = None
    discord:      Optional[str] = None
    signal:       Optional[str] = None


@dataclass
class Identity:
    canonical_id: str
    display_name: str
    role:         UserRole
    approved:     bool
    memory_key:   str
    platforms:    PlatformIds
    permissions:  list[str] = field(default_factory=list)
    approved_at:  Optional[str] = None


@dataclass
class PendingApproval:
    id:               str
    platform:         str
    platform_user_id: str
    display_name:     str
    tenant_id:        Optional[str]
    first_seen:       str
    message:          Optional[str]


@dataclass
class ResolveResult:
    found:        bool
    canonical_id: Optional[str]
    memory_key:   Optional[str]
    role:         Optional[UserRole]
    identity:     Optional[Identity]
```

### 7.3 resolver.py

```python
import json
import os
from datetime import datetime, timezone
from pathlib import Path
from .models import Identity, PendingApproval, ResolveResult, UserRole, PlatformIds


IDENTITY_MAP_PATH = Path.home() / ".hermes" / "identity_map.json"

# Mapa de campo en platforms según la plataforma
PLATFORM_FIELD_MAP = {
    "teams":    "teams",
    "telegram": "telegram",
    "whatsapp": "whatsapp",
    "slack":    "slack",
    "discord":  "discord",
    "signal":   "signal",
}


class IdentityResolver:

    def __init__(self, map_path: Path = IDENTITY_MAP_PATH):
        self.map_path = map_path
        self._data: dict = {}
        self._load()

    def _load(self) -> None:
        if self.map_path.exists():
            with open(self.map_path, "r") as f:
                self._data = json.load(f)
        else:
            self._data = {"version": "1.0", "identities": [], "pending_approval": []}

    def _save(self) -> None:
        self.map_path.parent.mkdir(parents=True, exist_ok=True)
        self._data["last_updated"] = datetime.now(timezone.utc).isoformat()
        with open(self.map_path, "w") as f:
            json.dump(self._data, f, indent=2, ensure_ascii=False)
        os.chmod(self.map_path, 0o600)

    def resolve(self, platform: str, platform_user_id: str) -> ResolveResult:
        """
        Resuelve un (platform, platform_user_id) a su canonical_id.
        Retorna ResolveResult con found=False si no está registrado.
        """
        field = PLATFORM_FIELD_MAP.get(platform)
        if not field:
            return ResolveResult(found=False, canonical_id=None,
                                 memory_key=None, role=None, identity=None)

        for entry in self._data.get("identities", []):
            platforms = entry.get("platforms", {})
            if platforms.get(field) == platform_user_id:
                if not entry.get("approved", False):
                    # Usuario existe pero no aprobado todavía
                    return ResolveResult(found=False, canonical_id=None,
                                         memory_key=None, role=None, identity=None)
                identity = self._parse_identity(entry)
                return ResolveResult(
                    found=True,
                    canonical_id=identity.canonical_id,
                    memory_key=identity.memory_key,
                    role=identity.role,
                    identity=identity,
                )

        return ResolveResult(found=False, canonical_id=None,
                             memory_key=None, role=None, identity=None)

    def add_pending(
        self,
        platform: str,
        platform_user_id: str,
        display_name: str,
        first_message: str,
        tenant_id: str = None,
    ) -> PendingApproval:
        """Registra un usuario desconocido como pendiente de aprobación."""
        import uuid
        pending_id = f"pending-{uuid.uuid4().hex[:8]}"
        entry = {
            "id":               pending_id,
            "platform":         platform,
            "platform_user_id": platform_user_id,
            "display_name":     display_name,
            "tenant_id":        tenant_id,
            "first_seen":       datetime.now(timezone.utc).isoformat(),
            "message":          first_message[:200] if first_message else None,
        }
        self._data.setdefault("pending_approval", []).append(entry)
        self._save()
        return PendingApproval(**entry)

    def approve_pending(self, pending_id: str, canonical_alias: str) -> Identity:
        """
        Aprueba un usuario pendiente y lo registra como identidad.
        canonical_alias es el nombre canónico elegido por el owner.
        """
        pending = next(
            (p for p in self._data.get("pending_approval", []) if p["id"] == pending_id),
            None
        )
        if not pending:
            raise ValueError(f"Pending approval {pending_id} not found")

        platform_field = PLATFORM_FIELD_MAP[pending["platform"]]
        new_identity = {
            "canonical_id": canonical_alias,
            "display_name": pending["display_name"],
            "role":         "team",
            "approved":     True,
            "approved_at":  datetime.now(timezone.utc).isoformat(),
            "memory_key":   canonical_alias,
            "permissions":  ["chat", "query"],
            "platforms": {
                platform_field:   pending["platform_user_id"],
                "teams_tenant":   pending.get("tenant_id"),
                "telegram":       None,
                "whatsapp":       None,
                "slack":          None,
                "discord":        None,
            }
        }
        self._data["identities"].append(new_identity)
        self._data["pending_approval"] = [
            p for p in self._data["pending_approval"] if p["id"] != pending_id
        ]
        self._save()
        return self._parse_identity(new_identity)

    def link_platform(
        self,
        canonical_id: str,
        platform: str,
        platform_user_id: str,
    ) -> None:
        """Vincula una plataforma adicional a un canonical_id existente."""
        field = PLATFORM_FIELD_MAP.get(platform)
        if not field:
            raise ValueError(f"Unknown platform: {platform}")

        for entry in self._data["identities"]:
            if entry["canonical_id"] == canonical_id:
                entry["platforms"][field] = platform_user_id
                self._save()
                return

        raise ValueError(f"Identity {canonical_id} not found")

    def is_pending(self, platform: str, platform_user_id: str) -> bool:
        return any(
            p["platform"] == platform and p["platform_user_id"] == platform_user_id
            for p in self._data.get("pending_approval", [])
        )

    def _parse_identity(self, entry: dict) -> Identity:
        p = entry.get("platforms", {})
        return Identity(
            canonical_id=entry["canonical_id"],
            display_name=entry["display_name"],
            role=UserRole(entry.get("role", "team")),
            approved=entry.get("approved", False),
            memory_key=entry.get("memory_key", entry["canonical_id"]),
            permissions=entry.get("permissions", []),
            approved_at=entry.get("approved_at"),
            platforms=PlatformIds(
                teams=p.get("teams"),
                teams_tenant=p.get("teams_tenant"),
                telegram=p.get("telegram"),
                whatsapp=p.get("whatsapp"),
                slack=p.get("slack"),
                discord=p.get("discord"),
            )
        )
```

### 7.4 Hook de entrada en el gateway (pseudocódigo)

```python
# gateway/hooks/identity_hook.py
from identity.resolver import IdentityResolver
from identity.notifier import notify_owner

resolver = IdentityResolver()


def on_message_received(platform: str, platform_user_id: str,
                         display_name: str, message_text: str,
                         extra: dict = None) -> dict:
    """
    Hook que se ejecuta ANTES de pasarle el mensaje al agente.
    Retorna el contexto de identidad para inyectar en la sesión.
    """

    # 1. Intentar resolver identidad
    result = resolver.resolve(platform, platform_user_id)

    if result.found:
        # --- FLUJO FELIZ: usuario conocido ---
        return {
            "canonical_id": result.canonical_id,
            "memory_key":   result.memory_key,
            "role":         result.role.value,
            "display_name": result.identity.display_name,
            "status":       "known",
        }

    # 2. Verificar si ya está pendiente de aprobación
    if resolver.is_pending(platform, platform_user_id):
        # Ya notificamos al owner, solo responder que está pendiente
        return {
            "canonical_id": None,
            "memory_key":   None,
            "role":         None,
            "status":       "pending",
        }

    # 3. Usuario completamente nuevo → registrar y notificar
    tenant_id = extra.get("tenant_id") if extra else None
    pending = resolver.add_pending(
        platform=platform,
        platform_user_id=platform_user_id,
        display_name=display_name,
        first_message=message_text,
        tenant_id=tenant_id,
    )

    notify_owner(
        platform=platform,
        display_name=display_name,
        platform_user_id=platform_user_id,
        pending_id=pending.id,
        tenant_id=tenant_id,
        first_message=message_text,
    )

    return {
        "canonical_id": None,
        "memory_key":   None,
        "role":         None,
        "status":       "new_pending",
    }
```

---

## 8. Plantilla de Notificación al Owner

Cuando llega un usuario nuevo desde cualquier plataforma, el owner recibe:

```
🔐 NUEVO ACCESO PENDIENTE

Plataforma:     Microsoft Teams
Nombre:         [display_name detectado]
ID en platform: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Tenant ID:      yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
Primera vez:    2026-01-01 10:00 UTC
Primer mensaje: "Hola, necesito ayuda con..."

Para APROBAR y crear nuevo usuario:
→ APROBAR <pending-id> <alias>

Para VINCULAR a un usuario existente:
→ VINCULAR <pending-id> <canonical_id_existente>

Para RECHAZAR:
→ RECHAZAR <pending-id>
```

---

## 9. Casos Edge y Manejo de Errores

### 9.1 aadObjectId ausente en Teams

Algunos mensajes de Teams (especialmente los del tipo `conversationUpdate` o
webhooks entrantes) pueden no incluir `aadObjectId`. En ese caso:

```python
def extract_teams_user_id(activity: dict) -> tuple[str, str]:
    aad_id = activity.get("from", {}).get("aadObjectId")
    internal_id = activity.get("from", {}).get("id", "")

    if aad_id:
        return "aad", aad_id          # ID confiable
    elif internal_id.startswith("29:"):
        return "internal", internal_id # fallback inestable, solo para logging
    else:
        return "unknown", ""           # no identificable
```

Si el aadObjectId no está disponible, rechazar el mensaje o pedir que el
usuario lo reenvíe desde el canal personal del bot (donde sí viene el aadObjectId).

### 9.2 Mismo usuario en dos tenants distintos

Un usuario puede tener cuentas en múltiples organizaciones de Azure AD.
En ese caso, el mismo nombre puede aparecer con distintos `aadObjectId`
(uno por tenant). Se deben registrar como dos identidades separadas,
o usar el campo `tenant_id` para diferenciarlas dentro del mismo canonical_id:

```json
{
  "canonical_id": "juan-dev",
  "platforms": {
    "teams": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "teams_tenant": "tenant-empresa-a",
    "teams_alt": "zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "teams_alt_tenant": "tenant-empresa-b"
  }
}
```

### 9.3 Usuario rechazado intenta volver a escribir

```python
if resolver.is_rejected(platform, platform_user_id):
    # Silenciar — no notificar al owner de nuevo, no responder al usuario
    return {"status": "rejected", "canonical_id": None}
```

### 9.4 Corrupción del identity_map.json

```python
try:
    self._load()
except json.JSONDecodeError:
    # Restaurar desde backup si existe
    backup_path = self.map_path.with_suffix(".json.bak")
    if backup_path.exists():
        import shutil
        shutil.copy(backup_path, self.map_path)
        self._load()
    else:
        # Iniciar vacío — ningún usuario conocido
        self._data = {"version": "1.0", "identities": [], "pending_approval": []}
```

Estrategia de backup: guardar una copia `.json.bak` en cada `_save()` exitoso.

---

## 10. Seguridad

| Aspecto                  | Implementación                                                  |
|--------------------------|-----------------------------------------------------------------|
| Permisos del archivo     | `chmod 600` en cada escritura — solo el proceso owner puede leer |
| Auto-registro            | Prohibido — todo usuario nuevo queda en `pending_approval`     |
| Validación de tenant     | Opcional pero recomendado: whitelist de tenant IDs permitidos  |
| Roles y permisos         | El campo `permissions[]` limita lo que puede pedir cada rol     |
| Identidades rechazadas   | Silenciar sin notificar — no revelar que el sistema existe      |
| Rotación de IDs          | Si un `aadObjectId` cambia (cuenta eliminada y recreada), es un nuevo usuario — flujo de aprobación normal |

---

## 11. Fases de Implementación

### Fase 1 — Identity Map manual (sin código adicional)

- Crear `~/.hermes/identity_map.json` a mano con los IDs ya conocidos
- El agente lo lee al inicio de cada sesión como archivo de contexto
- El gateway inyecta `canonical_id` en el system prompt manualmente
- **Costo:** 1-2 horas | **Beneficio:** memoria unificada desde el primer día

### Fase 2 — Módulo Python con hook de gateway

- Implementar `identity/resolver.py` según el código de esta spec
- Integrar el hook en el gateway (función `on_message_received`)
- Flujo de aprobación manual por comando de texto
- **Costo:** 4-6 horas | **Beneficio:** flujo automático completo

### Fase 3 — Integración nativa en el gateway de Hermes

- Pull request al repositorio de Hermes Agent
- Comandos `hermes identity list/link/approve/revoke`
- UI de aprobación en la plataforma de mensajería del owner
- Sincronización de identidades entre instancias (multi-server)
- **Costo:** 2-3 días | **Beneficio:** feature de primera clase en el framework

---

## 12. Consideraciones Adicionales para Teams Enterprise

Si el entorno de Teams es corporativo (Azure AD con acceso al Microsoft Graph API),
es posible enriquecer automáticamente el perfil del usuario sin esperar aprobación manual:

```python
# Con permisos User.Read.All en Azure AD
from msgraph import GraphServiceClient

async def enrich_from_aad(aad_object_id: str, tenant_id: str) -> dict:
    client = GraphServiceClient(credentials, scopes=["https://graph.microsoft.com/.default"])
    user = await client.users.by_user_id(aad_object_id).get()
    return {
        "display_name":  user.display_name,
        "email":         user.mail,
        "job_title":     user.job_title,
        "department":    user.department,
        "office":        user.office_location,
    }
```

Esto permite un onboarding automático dentro de la misma organización,
sin necesidad de aprobación manual para cada miembro del equipo.

---

---

## 13. Integración con Hermes Holographic Memory

### 13.1 Qué es Holographic en Hermes

Holographic es el plugin de memoria profunda de Hermes. A diferencia de la
memoria built-in (archivos Markdown en `~/.hermes/memories/`), Holographic
usa una base de datos SQLite con capacidades avanzadas:

```
~/.hermes/memory_store.db
├── tabla: facts          → hechos almacenados con vector HRR, trust_score, categoría
├── tabla: entities       → entidades reconocidas (personas, proyectos, conceptos)
├── tabla: fact_entities  → relación fact ↔ entity (N:M)
└── FTS5 virtual table    → búsqueda full-text sobre facts
```

Cada hecho tiene un `hrr_vector` (Holographic Reduced Representation) — un
vector de fases que permite recuperación composicional: "dame todo lo que
relaciona a esta persona con este proyecto".

El plugin expone la herramienta `fact_store` con acciones:
- `add` — guardar un hecho
- `search` — búsqueda por keywords
- `probe` — todos los hechos sobre una entidad
- `related` — vecindad estructural de una entidad
- `reason` — consulta composicional multi-entidad
- `contradict` — detectar hechos contradictorios

### 13.2 El problema de identidad en Holographic

Holographic es single-user por diseño: una sola `memory_store.db` por perfil
de Hermes. Cuando el mismo usuario escribe desde distintos canales, el agente
puede crear hechos inconsistentes o duplicados sobre la "misma persona vista
desde diferentes ángulos".

Sin Identity Map:
```
Sesión WhatsApp  → fact: "usuario_wa_5491112345678 prefiere respuestas cortas"
Sesión Telegram  → fact: "usuario_tg_987654321 trabaja en YPF"
→ Holographic no sabe que son la misma persona
→ probe("usuario_wa_*") no devuelve los hechos de Telegram y viceversa
```

Con Identity Map + canonical_id:
```
Sesión WhatsApp  → fact: "juan-dev prefiere respuestas cortas" (tag: canonical_id=juan-dev)
Sesión Teams     → fact: "juan-dev trabaja en YPF" (tag: canonical_id=juan-dev)
→ probe("juan-dev") devuelve TODOS los hechos de ese usuario, de todos los canales
```

### 13.3 Cómo conectar Identity Map con Holographic

El puente es etiquetar cada hecho con el `canonical_id` del usuario activo.
Esto se hace en el hook de sesión, **antes** de que el agente empiece a
procesar el mensaje.

```python
# gateway/hooks/identity_hook.py — versión ampliada con Holographic

from identity.resolver import IdentityResolver
from plugins.memory.holographic import HolographicMemoryProvider  # o vía API de Hermes

resolver  = IdentityResolver()
holographic = HolographicMemoryProvider()   # abre memory_store.db


def on_message_received(platform, platform_user_id, display_name,
                         message_text, extra=None) -> dict:

    result = resolver.resolve(platform, platform_user_id)

    if not result.found:
        # ... flujo de aprobación (igual que antes)
        return {"status": "pending_or_unknown", "canonical_id": None}

    canonical_id = result.canonical_id

    # ── PUENTE CON HOLOGRAPHIC ──────────────────────────────────────────
    # 1. Recuperar contexto existente del usuario antes de pasarle el mensaje al agente
    existing_facts = holographic.fact_store(
        action="probe",
        entity=canonical_id,
    )

    # 2. Inyectar el canonical_id como contexto activo de la sesión
    #    El agente usará este valor al llamar a fact_store(action="add", ...)
    session_context = {
        "canonical_id":     canonical_id,
        "memory_key":       result.memory_key,
        "role":             result.role.value,
        "holographic_hint": f"El usuario activo es '{canonical_id}'. "
                            f"Siempre usar este valor como entidad principal "
                            f"al guardar o recuperar hechos en fact_store.",
        "existing_facts":   existing_facts,   # hechos previos del usuario
    }
    # ────────────────────────────────────────────────────────────────────

    return session_context
```

### 13.4 Flujo completo con Holographic — primera sesión vs. sesión posterior

#### Primera sesión del usuario (recién aprobado)

```
Usuario aprobado → canonical_id = "juan-dev" creado
                │
                ▼
Primer mensaje recibido
                │
                ▼
IdentityResolver → resolve("teams", aad_id) → canonical_id = "juan-dev"
                │
                ▼
Holographic → probe("juan-dev")
→ RESULTADO: [] (vacío, es la primera vez)
                │
                ▼
Agente procesa el mensaje sin contexto previo
Durante la sesión: el agente aprende preferencias, proyectos, etc.
Al guardar hechos: fact_store(action="add", entity="juan-dev", content="...")
                │
                ▼
memory_store.db: nuevas filas en facts/entities/fact_entities
entities.name = "juan-dev", vinculado a cada hecho de esa sesión
```

#### Sesión posterior (usuario vuelve, desde otro canal)

```
Usuario escribe desde Teams (antes usaba solo Telegram)
                │
                ▼
IdentityResolver → resolve("teams", aad_id) → canonical_id = "juan-dev"
                │
                ▼
Holographic → probe("juan-dev")
→ RESULTADO: [
    "juan-dev prefiere respuestas cortas",
    "juan-dev trabaja en el módulo de pagos",
    "juan-dev usa Python y FastAPI",
    "juan-dev está en el timezone UTC-3",
    ...
  ]
                │
                ▼
Agente recibe el mensaje CON todo el contexto acumulado
Responde como si ya conociera al usuario
Sin importar que esta sesión sea desde Teams y la anterior haya sido desde Telegram
```

### 13.5 Taggeado de hechos por canonical_id

Para que Holographic permita filtrar hechos por usuario en un entorno
multi-usuario, cada hecho debe tener el `canonical_id` en el campo `tags`:

```python
# Al guardar un hecho desde el agente:
holographic.fact_store(
    action="add",
    content="Juan prefiere respuestas concisas",
    category="preferences",
    tags=f"canonical_id:{canonical_id} platform:{current_platform}",
    entity=canonical_id,
)

# Al recuperar todos los hechos de un usuario específico:
holographic.fact_store(
    action="search",
    query=f"canonical_id:{canonical_id}",
)
```

### 13.6 Esquema extendido de memory_store.db para multi-usuario

La tabla `facts` ya tiene el campo `tags` (TEXT). La extensión necesaria
es mínima — solo convención de naming en los tags:

```sql
-- Sin cambios de schema, solo convención:
-- tags contiene: "canonical_id:juan-dev platform:teams category:preferences"

-- Query para recuperar todos los hechos de un usuario:
SELECT * FROM facts WHERE tags LIKE '%canonical_id:juan-dev%'
ORDER BY trust_score DESC, retrieval_count DESC;

-- Query composicional (HRR) para "hechos de juan-dev sobre proyectos":
-- Esto lo hace Holographic internamente con fact_store(action="reason", ...)
```

### 13.7 Diagrama de capas completo

```
┌─────────────────────────────────────────────────────────────┐
│                    CAPA DE PLATAFORMA                        │
│  Teams / WhatsApp / Telegram / Slack / Discord               │
│  Cada una entrega: (platform, platform_user_id, message)    │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  IDENTITY RESOLVER                           │
│  identity_map.json                                          │
│  (platform, platform_user_id) → canonical_id               │
│  Si no existe → flujo de aprobación                         │
└───────────────────────────┬─────────────────────────────────┘
                            │  canonical_id resuelto
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              HERMES MEMORY — CAPA BUILT-IN                   │
│  ~/.hermes/memories/MEMORY.md    → notas del agente          │
│  ~/.hermes/memories/USER.md      → perfil del usuario        │
│  (actualmente sin namespace por usuario — TODO multi-user)  │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│          HERMES MEMORY — CAPA HOLOGRAPHIC (profunda)         │
│  ~/.hermes/memory_store.db                                   │
│  ├── facts taggeados con canonical_id                        │
│  ├── entities con nombre = canonical_id                      │
│  ├── HRR vectors para recuperación composicional             │
│  └── FTS5 para búsqueda full-text por usuario                │
│                                                              │
│  probe("juan-dev") → todos los hechos de ese usuario        │
│  reason("juan-dev", "proyecto-x") → intersección semántica  │
└───────────────────────────┬─────────────────────────────────┘
                            │  contexto cargado
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                       AGENTE IA                              │
│  Recibe el mensaje + contexto completo del usuario           │
│  Responde con memoria total acumulada entre sesiones         │
│  Al guardar hechos nuevos: etiqueta con canonical_id         │
└─────────────────────────────────────────────────────────────┘
```

### 13.8 Configurar Holographic como provider activo

```bash
# Activar Holographic como backend de memoria en Hermes
hermes config set memory.provider holographic

# Verificar que está activo
hermes memory status
# → Provider: holographic ← active ✓

# El agente ya usa fact_store automáticamente desde la próxima sesión
# Requiere reiniciar el gateway si está corriendo
hermes gateway restart
```

Configuración opcional en `config.yaml`:
```yaml
plugins:
  hermes-memory-store:
    db_path: ~/.hermes/memory_store.db   # default
    auto_extract: false                   # extraer hechos automáticamente de conversaciones
    default_trust: 0.5                    # trust score inicial de nuevos hechos
    min_trust_threshold: 0.3             # umbral mínimo para incluir en respuestas
    temporal_decay_half_life: 0          # 0 = sin decay temporal
```

---

---

## 14. SIE — Superlinked Inference Engine

### 14.1 Qué es SIE

SIE (https://github.com/superlinked/sie) es un servidor de inferencia open source
que expone tres funciones a través de una API HTTP unificada:

```
encode   →  texto/imagen a vector (dense, sparse, multi-vector)
score    →  reranking de documentos por relevancia semántica
extract  →  NER zero-shot sin entrenamiento (entidades nombradas)
```

- 85+ modelos pre-configurados y verificados contra MTEB en CI
- Compatible con OpenAI /v1/embeddings (drop-in)
- Integración nativa con Qdrant, Chroma, Weaviate, LanceDB
- Integración con LangChain, LlamaIndex, DSPy, CrewAI, Haystack
- Apache 2.0 — corre en CPU (Docker) o GPU (CUDA)

### 14.2 Dónde encaja SIE en el SDD

SIE no reemplaza ninguna capa del sistema. Es un servicio de soporte que
potencia tres puntos específicos del pipeline:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PIPELINE COMPLETO CON SIE                         │
│                                                                      │
│  MENSAJE ENTRANTE                                                    │
│       │                                                              │
│       ▼                                                              │
│  ┌──────────────────────────────────────────┐                       │
│  │  [1] SIE extract                          │  ← PUNTO 1           │
│  │  GLiNER / NuNER zero-shot                 │                       │
│  │  Input:  texto del mensaje                │                       │
│  │  Output: entidades detectadas             │                       │
│  │  Ej: {proyecto:"ForestAI", tech:"FastAPI"}│                       │
│  └──────────────────┬───────────────────────┘                       │
│                     │ entidades extraídas                            │
│                     ▼                                                │
│  ┌──────────────────────────────────────────┐                       │
│  │  Identity Resolver                        │                       │
│  │  identity_map.json → canonical_id         │                       │
│  └──────────────────┬───────────────────────┘                       │
│                     │                                                │
│                     ▼                                                │
│  ┌──────────────────────────────────────────┐                       │
│  │  [2] SIE encode                           │  ← PUNTO 2           │
│  │  BGE-M3 / Stella v5 / multilingual-e5    │                       │
│  │  Input:  texto de hechos a guardar        │                       │
│  │  Output: vector denso + sparse (1024d)    │                       │
│  │  → reemplaza HRR básico de Holographic   │                       │
│  └──────────────────┬───────────────────────┘                       │
│                     │ vectores de calidad MTEB                       │
│                     ▼                                                │
│  ┌──────────────────────────────────────────┐                       │
│  │  Holographic + Qdrant                     │                       │
│  │  memory_store.db / qdrant collection      │                       │
│  │  hechos taggeados por canonical_id        │                       │
│  └──────────────────┬───────────────────────┘                       │
│                     │                                                │
│                     ▼                                                │
│  ┌──────────────────────────────────────────┐                       │
│  │  [3] SIE score                            │  ← PUNTO 3           │
│  │  BGE-reranker-v2-m3                       │                       │
│  │  Input:  query del agente + N hechos      │                       │
│  │  Output: hechos reordenados por relevancia│                       │
│  │  → solo los top-K entran al contexto      │                       │
│  └──────────────────┬───────────────────────┘                       │
│                     │ contexto filtrado y relevante                  │
│                     ▼                                                │
│  ┌──────────────────────────────────────────┐                       │
│  │  AGENTE IA (JARVIS)                       │                       │
│  │  responde con contexto limpio y preciso   │                       │
│  └──────────────────────────────────────────┘                       │
└─────────────────────────────────────────────────────────────────────┘
```

### 14.3 Punto 1 — Extracción automática de entidades (extract)

**Problema que resuelve:** hoy el agente decide manualmente qué guardar en
Holographic. Con SIE `extract`, cada mensaje se procesa automáticamente antes
de llegar al agente y las entidades detectadas se taggean en los hechos.

**Modelos recomendados:**

| Modelo | Uso | Tamaño |
|--------|-----|--------|
| `urchade/gliner_multi-v2.1` | Multilingüe — ideal para español/inglés mezclado | Medium |
| `numind/NuNER_Zero` | Muy liviano, entidades genéricas | Tiny |
| `gliner-community/gliner_large-v2.5` | Máxima precisión | Large |

**Labels a detectar en el contexto del equipo:**

```python
labels = [
    "proyecto",       # ForestAI, Fleet Optimizer, EduAI
    "tecnología",     # FastAPI, React, Docker, Qdrant
    "persona",        # nombres de colaboradores, clientes
    "organización",   # YPF, AlegentAI, empresa cliente
    "concepto_tecnico", # RAG, embeddings, CI/CD
    "tarea",          # "implementar auth", "deploy staging"
]
```

**Código de integración:**

```python
from sie_sdk import SIEClient
from sie_sdk.types import Item

sie = SIEClient("http://localhost:8080")

def extract_entities_from_message(message: str, canonical_id: str) -> list[dict]:
    result = sie.extract(
        "urchade/gliner_multi-v2.1",
        Item(text=message),
        labels=["proyecto", "tecnología", "persona", "organización", "tarea"]
    )
    entities = result["entities"]
    # Taggear con canonical_id para Holographic
    for e in entities:
        e["canonical_id"] = canonical_id
    return entities

# Uso en el hook de gateway:
entities = extract_entities_from_message(message_text, canonical_id)
# → [{"text": "ForestAI", "label": "proyecto", "score": 0.94, "canonical_id": "juan-dev"}]
# Guardar en Holographic: fact_store(action="add", entity=canonical_id, tags="proyecto:ForestAI")
```

### 14.4 Punto 2 — Embeddings de calidad para Holographic (encode)

**Problema que resuelve:** Holographic usa HRR (Holographic Reduced Representations)
para la recuperación composicional — vectores de fases que son eficientes pero no
tienen la calidad semántica de modelos entrenados sobre benchmarks como MTEB.

Con SIE como servidor de embeddings, cada hecho guardado en Holographic
(o en Qdrant como backend alternativo) usa vectores de alta calidad.

**Modelo recomendado:** `BAAI/bge-m3`
- Soporta dense + sparse en una sola llamada (hybrid search)
- Multilingüe: español, inglés, mezcla de ambos
- 1024 dimensiones dense, sparse nativo para Qdrant

```python
from sie_qdrant import SIENamedVectorizer
from qdrant_client import QdrantClient
from qdrant_client.models import (
    Distance, VectorParams, PointStruct,
    SparseVectorParams, SparseVector
)

vectorizer = SIENamedVectorizer(
    base_url="http://localhost:8080",
    model="BAAI/bge-m3",
    output_types=["dense", "sparse"],
)

qdrant = QdrantClient("http://localhost:6333")

# Colección por canonical_id (o colección única con filtro por canonical_id)
qdrant.create_collection(
    collection_name="agent_memory",
    vectors_config={"dense": VectorParams(size=1024, distance=Distance.COSINE)},
    sparse_vectors_config={"sparse": SparseVectorParams()},
)

def store_fact_with_vector(fact_text: str, canonical_id: str, metadata: dict):
    vectors = vectorizer.embed_documents([fact_text])
    qdrant.upsert(
        collection_name="agent_memory",
        points=[PointStruct(
            id=generate_uuid(fact_text),
            vector={
                "dense":  vectors[0]["dense"],
                "sparse": SparseVector(**vectors[0]["sparse"]),
            },
            payload={
                "content":      fact_text,
                "canonical_id": canonical_id,
                "category":     metadata.get("category", "general"),
                "tags":         metadata.get("tags", ""),
                "created_at":   metadata.get("created_at"),
            }
        )]
    )
```

### 14.5 Punto 3 — Reranking de hechos antes de responder (score) — JARVIS

**Problema que resuelve:** cuando un usuario tiene muchos hechos acumulados en
Holographic, una búsqueda semántica devuelve N resultados de similar score.
Meter todos en el contexto del agente consume tokens y agrega ruido.

SIE `score` actúa como reranker de segunda etapa: dado un query del agente
y los N hechos candidatos, devuelve los mismos hechos ordenados por relevancia
real con un cross-encoder (modelo que ve query + documento juntos, no en
forma de vectores separados).

**Modelo recomendado:** `BAAI/bge-reranker-v2-m3` — multilingüe, precisión alta.

```python
def retrieve_relevant_facts(
    query: str,
    canonical_id: str,
    top_n: int = 5
) -> list[str]:

    # Paso 1: búsqueda semántica amplia en Qdrant/Holographic
    # (devuelve top-20 candidatos por similaridad de vector)
    candidates = qdrant_search(query, canonical_id, limit=20)

    # Paso 2: reranking con SIE score (cross-encoder)
    scores = sie.score(
        "BAAI/bge-reranker-v2-m3",
        Item(text=query),
        [Item(text=fact["content"], item_id=fact["id"]) for fact in candidates]
    )

    # Paso 3: tomar solo los top-N más relevantes
    reranked = sorted(scores["scores"], key=lambda x: x["rank"])[:top_n]
    fact_ids = {s["item_id"] for s in reranked}

    return [f["content"] for f in candidates if f["id"] in fact_ids]

# Resultado: el agente recibe 5 hechos ultra-relevantes en lugar de 20 mediocres
```

**Impacto directo en JARVIS:**
- Respuestas más precisas cuando el usuario tiene mucho historial acumulado
- Menos tokens de contexto gastados en hechos irrelevantes
- El ranking toma en cuenta el query exacto, no solo similaridad vectorial

### 14.6 Deploy de SIE en ai-server

SIE corre como contenedor Docker en el servidor local. No requiere GPU para
modelos pequeños/medium. Para modelos large (BGE-M3 completo) se recomienda
al menos 8GB de RAM.

```bash
# CPU (para empezar — funciona con GLiNER y bge-m3 en CPU)
docker run -d \
  --name sie-server \
  -p 8080:8080 \
  --restart unless-stopped \
  ghcr.io/superlinked/sie-server:latest-cpu-default

# Verificar que levantó
curl http://localhost:8080/health
# → {"status": "ok"}

# Listar modelos disponibles
curl http://localhost:8080/v1/models | python3 -m json.tool | head -30
```

**Integración en docker-compose del equipo Nelson:**

```yaml
services:
  sie-server:
    image: ghcr.io/superlinked/sie-server:latest-cpu-default
    ports:
      - "8080:8080"
    restart: unless-stopped
    environment:
      - SIE_TELEMETRY_DISABLED=1
    volumes:
      - sie_model_cache:/root/.cache/huggingface
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  sie_model_cache:
```

**Instalación del SDK:**

```bash
pip install sie-sdk sie-qdrant
```

### 14.7 Dónde aparece SIE en cada fase del SDD

| Fase SDD | Actividad | Rol de SIE |
|----------|-----------|------------|
| SPEC     | Definir contratos de datos | Definir schema de entidades a extraer (labels) |
| DESIGN   | Diseño del pipeline de memoria | SIE como servicio de soporte: extract + encode + score |
| IMPL — Backend | Hook de gateway, storage en Holographic/Qdrant | `sie-sdk` en el hook `on_message_received` |
| IMPL — JARVIS | Recuperación de contexto para responder | `sie.score()` como reranker antes de armar el prompt |
| VERIFICATION | QA del sistema de memoria | Validar que entidades extraídas son correctas por plataforma |

### 14.8 Modelos SIE recomendados por caso de uso

| Caso de uso | Modelo | Función SIE | Impacto |
|-------------|--------|-------------|---------|
| Extracción de entidades ES/EN | `urchade/gliner_multi-v2.1` | `extract` | Taggeo automático de hechos |
| Embeddings híbridos (dense+sparse) | `BAAI/bge-m3` | `encode` | Búsqueda semántica de alta calidad |
| Reranking de hechos para el agente | `BAAI/bge-reranker-v2-m3` | `score` | Contexto preciso, menos tokens |
| Multilingüe liviano (dev/test) | `intfloat/multilingual-e5-large` | `encode` | Menor consumo de RAM |
| NER muy liviano (producción CPU) | `numind/NuNER_Zero` | `extract` | Latencia mínima |

---

*Especificación técnica — Sistema de Identidad Multi-Plataforma*
*Versión 1.2 — Integración SIE (Superlinked Inference Engine)*
*Uso interno*
