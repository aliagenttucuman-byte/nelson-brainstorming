# Nelson Brainstorming - Repositorio de Ideas y Proyectos

> Repositorio central donde se archivan todas las sesiones de brainstorming, specs y SDDs del equipo.
> Cada idea tiene su carpeta, su estado y su camino hacia desarrollo.

---

## 📊 Dashboard de Proyectos

| Proyecto | Fecha | Estado | Fase | Próxima Acción | Asignado |
|----------|-------|--------|------|----------------|----------|
| [Fleet Optimizer](2026-05-13-fleet-optimizer/) | 2026-05-13 | 🟡 Definición | SDD completo | Validar con Pablo + Luigi | Tony |

**Leyenda de Estados:**
- 🔴 Idea cruda / brainstorming
- 🟡 En definición (SDD/spec)
- 🟠 Validando con stakeholders
- 🐛 En desarrollo (MVP)
- 💚 En producción
- ⚪ Pausado / archivado

---

## 📁 Estructura del Repositorio

```
nelson-brainstorming/
├── README.md                          # Este archivo - Dashboard
├── YYYY-MM-DD-nombre-del-proyecto/    # Una carpeta por brainstorming
│   ├── README.md                      # Resumen ejecutivo del proyecto
│   ├── SDD.md                         # Software Design Document
│   ├── financiero.md                  # Modelos de negocio, VAN/TIR, pricing
│   ├── openapi.yaml                   # Spec de API (si aplica)
│   ├── diagramas/                     # Diagramas, mockups, arquitecturas
│   ├── notas/                         # Ideas sueltas, links, referencias
│   └── decisiones.md                  # Decisiones clave y por qué
│
└── templates/
    ├── sdd-template.md
    ├── resumen-ejecutivo-template.md
    └── proyecto-template/             # Copiar para nuevo proyecto
```

---

## ⚡ Flujo de Trabajo

```
Tony tiene una idea
        ↓
JARVIS crea carpeta: YYYY-MM-DD-nombre-proyecto/
        ↓
Brainstorming + documentación
        ↓
README.md con estado = 🔴 Idea cruda
        ↓
Revisión con Pablo/Gino/Luigi
        ↓
Estado → 🟠 Validando
        ↓
Si aprueba → Estado 🐛 En desarrollo
        ↓
Symlink a ~/proyectos/activos/ + equipo asignado
```

---

## 📋 Checklist para Nuevo Proyecto

- [ ] Crear carpeta con fecha: `YYYY-MM-DD-nombre-corto/`
- [ ] Copiar templates si es necesario
- [ ] Escribir `README.md` con resumen ejecutivo
- [ ] Definir estado inicial (🔴 Idea cruda)
- [ ] Actualizar este README.md con la nueva fila en el dashboard
- [ ] Commitear y pushear

---

## 👥 Equipo

| Rol | Nombre | Tipo |
|-----|--------|------|
| CEO / Founder | Tony Stark (Nelson) | Humano 🧔 |
| COO / Founder | Pablo | Humano 🧔 |
| Asesor Gestión | Gino | Humano 🧔 |
| Asesor Finanzas | Luigi | Humano 🧔 |
| Arquitecto Backend | Beto | IA 🤖 |
| Backend Developer | Ricky | IA 🤖 |
| Frontend Developer | Nico | IA 🤖 |
| DevOps | Diego | IA 🤖 |
| QA / AI Integration | Alma | IA 🤖 |
| Asistente Ejecutivo | JARVIS | IA 🤖 |

---

**Repo:** github.com/aliagenttucuman-byte/nelson-brainstorming
**Owner:** Nelson Acosta
**Última actualización:** 2026-05-13
