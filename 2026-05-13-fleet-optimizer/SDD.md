# SDD - Fleet Optimizer & AI Assistant
## Sistema de Gestión de Flotas con IA
### Software Design Document - Versión 1.0

---

## 1. Visión General

**Objetivo:** Desarrollar una plataforma integral para la gestión de flotas de transporte que permita:
- Tracking en tiempo real de vehículos
- Cálculo automático de costos operativos por vehículo y por flota
- Mantenimiento preventivo y correctivo
- Análisis financiero avanzado (VAN, TIR, Payback)
- Asistente de IA para consultas en lenguaje natural sobre la flota

**Diferenciador:** Ningún competidor en el mercado latinoamericano integra un asistente de IA conversacional que permite consultar datos financieros y operativos en tiempo real mediante lenguaje natural.

---

## 2. Actores y Stakeholders

| Actor | Rol | Interacción |
|-------|-----|-------------|
| **Gerente de Flota** | Toma decisiones estratégicas | Dashboard, reportes financieros, VAN/TIR |
| **Supervisor de Operaciones** | Control diario | Tracking, alertas, asignación de rutas |
| **Chofer/Operario** | Usuario de campo | App móvil, reportes de viaje, estado del vehículo |
| **Taller/Mecánico** | Mantenimiento | Órdenes de trabajo, historial de servicios |
| **Administración** | Facturación y costos | Carga de gastos, combustible, peajes, facturas |
| **Asistente IA (JARVIS)** | Consultor virtual | Chatbot con acceso a toda la data de la flota |

---

## 3. Domain Model (Entidades Principales)

### 3.1 Vehículo
```yaml
Vehicle:
  id: UUID
  plate: string (patente)
  brand: string (marca)
  model: string (modelo)
  year: integer
  type: enum [truck, van, car, motorcycle, trailer]
  capacity_kg: float
  fuel_type: enum [diesel, gasoline, cng, electric]
  fuel_consumption_km_per_liter: float
  acquisition_cost: decimal
  acquisition_date: date
  depreciation_years: integer
  current_odometer_km: float
  status: enum [active, maintenance, inactive, sold]
  gps_device_id: string
  assigned_driver_id: UUID (nullable)
  insurance_policy: string
  insurance_expiry: date
  created_at: datetime
  updated_at: datetime
```

### 3.2 Viaje (Trip)
```yaml
Trip:
  id: UUID
  vehicle_id: UUID
  driver_id: UUID
  start_time: datetime
  end_time: datetime (nullable)
  origin: GeoPoint
  destination: GeoPoint (nullable - para viajes en curso)
  distance_km: float (calculado o GPS)
  route: GeoPoint[] (array de coordenadas GPS)
  status: enum [planned, in_progress, completed, cancelled]
  revenue: decimal (ingresos por este viaje)
  notes: string
  created_at: datetime
```

### 3.3 Costo Operativo (OperatingCost)
```yaml
OperatingCost:
  id: UUID
  vehicle_id: UUID
  trip_id: UUID (nullable - si aplica a un viaje específico)
  category: enum [
    fuel,            # Combustible
    toll,            # Peajes
    parking,         # Estacionamiento
    maintenance,     # Mantenimiento
    repair,          # Reparación
    insurance,       # Seguro
    license,         # Patente/Licencias
    tire,            # Neumáticos
    oil,             # Aceite/lubricantes
    cleaning,        # Limpieza
    driver_salary,   # Sueldo chofer
    other            # Otros
  ]
  amount: decimal
  currency: string (default: ARS)
  date: date
  description: string
  receipt_url: string (nullable)
  odometer_km_at_cost: float
  created_by: UUID
  created_at: datetime
```

### 3.4 Mantenimiento (MaintenanceRecord)
```yaml
MaintenanceRecord:
  id: UUID
  vehicle_id: UUID
  type: enum [preventive, corrective, inspection]
  scheduled_date: date
  completed_date: date (nullable)
  description: string
  parts_used: Part[]
  labor_cost: decimal
  parts_cost: decimal
  total_cost: decimal
  odometer_km_at_service: float
  next_service_odometer_km: float (nullable)
  next_service_date: date (nullable)
  status: enum [scheduled, in_progress, completed, cancelled]
  performed_by: string
  created_at: datetime
```

### 3.5 Driver (Chofer)
```yaml
Driver:
  id: UUID
  name: string
  license_number: string
  license_type: string
  license_expiry: date
  phone: string
  email: string
  hire_date: date
  salary_type: enum [fixed_per_month, per_trip, per_km]
  salary_amount: decimal
  assigned_vehicle_id: UUID (nullable)
  status: enum [active, on_leave, inactive]
  created_at: datetime
```

### 3.6 Posición GPS (GPSPosition)
```yaml
GPSPosition:
  id: UUID
  vehicle_id: UUID
  latitude: float
  longitude: float
  altitude: float (nullable)
  speed_kmh: float
  heading: float (dirección en grados)
  timestamp: datetime
  ignition_status: boolean
```

---

## 4. Cálculos Financieros

### 4.1 Costo Total de Propiedad (TCO) por Vehículo

El TCO incluye todos los costos asociados a un vehículo durante su vida útil.

```
TCO = Costo_Aduisición + Suma(Costos_Operativos) + Suma(Costos_Mantenimiento) - Valor_Residual

Donde:
- Costo_Aduisición = Precio de compra del vehículo
- Suma(Costos_Operativos) = Total de OperatingCost del vehículo
- Suma(Costos_Mantenimiento) = Total de MaintenanceRecord del vehículo
- Valor_Residual = Valor estimado de reventa al final de la vida útil
```

### 4.2 Costo por Kilómetro (CPK)

```
CPK = Costos_Totales_del_Período / Kilómetros_Recorridos_en_el_Período

Variantes:
- CPK_Diario = Costos_Hoy / Km_Hoy
- CPK_Mensual = Costos_Mes / Km_Mes
- CPK_Vehículo = TCO_Acumulado / Km_Totales_Acumulados
```

### 4.3 Ganancia por Vehículo (período)

```
Ganancia_Neta = Ingresos_Total - Costos_Operativos_Total - Costos_Mantenimiento_Total

Ingresos_Total = Suma(revenue de todos los Trips del período)
Costos_Operativos_Total = Suma(amount de OperatingCost del período)
Costos_Mantenimiento_Total = Suma(total_cost de MaintenanceRecord del período)
```

### 4.4 Margen Operativo

```
Margen_Operativo_% = (Ganancia_Neta / Ingresos_Total) × 100
```

### 4.5 Valor Actual Neto (VAN / NPV)

El VAN calcula el valor presente de todos los flujos de caja futuros de un vehículo o flota, descontados a una tasa de descuento.

```python
def calcular_van(inversion_inicial, flujos_de_caja, tasa_descuento):
    """
    Args:
        inversion_inicial: Costo de adquisición (negativo)
        flujos_de_caja: Lista de ganancias_netas por período [mes1, mes2, ..., mesN]
        tasa_descuento: Tasa mensual o anual (ej: 0.10 para 10%)
    
    Returns:
        VAN: Si > 0, la inversión es rentable
    """
    van = inversion_inicial
    for t, flujo in enumerate(flujos_de_caja, 1):
        van += flujo / ((1 + tasa_descuento) ** t)
    return van
```

**Ejemplo práctico:**
- Vehículo adquirido: $30,000,000 ARS
- Vida útil esperada: 5 años (60 meses)
- Ganancia neta mensual promedio: $800,000 ARS
- Tasa de descuento anual: 80% (inflación Argentina)
- Tasa mensual: ~5%

```
VAN = -30,000,000 + 800,000/(1.05)^1 + 800,000/(1.05)^2 + ... + 800,000/(1.05)^60
```

**Interpretación:**
- VAN > 0: El vehículo genera valor, conviene mantenerlo
- VAN < 0: El vehículo destruye valor, considerar reemplazo
- VAN = 0: Punto de equilibrio

### 4.6 Tasa Interna de Retorno (TIR / IRR)

La TIR es la tasa de descuento que hace que el VAN sea igual a cero.

```python
def calcular_tir(inversion_inicial, flujos_de_caja):
    """
    Args:
        inversion_inicial: Costo de adquisición (negativo)
        flujos_de_caja: Lista de ganancias_netas por período
    
    Returns:
        TIR: Tasa interna de retorno (ej: 0.15 = 15%)
    
    Método: Newton-Raphson o bisección
    """
    from scipy.optimize import newton
    
    def van_func(rate):
        van = inversion_inicial
        for t, flujo in enumerate(flujos_de_caja, 1):
            van += flujo / ((1 + rate) ** t)
        return van
    
    # Semilla inicial
    tir = newton(van_func, x0=0.1)
    return tir
```

**Interpretación:**
- TIR > Tasa_de_Descuento: Inversión rentable
- TIR < Tasa_de_Descuento: Inversión no rentable
- TIR = Tasa_de_Descuento: Punto de equilibrio

### 4.7 Payback Period (Período de Recuperación)

```python
def calcular_payback(inversion_inicial, flujos_de_caja):
    """
    Calcula en cuántos períodos se recupera la inversión inicial
    """
    acumulado = 0
    for t, flujo in enumerate(flujos_de_caja, 1):
        acumulado += flujo
        if acumulado >= abs(inversion_inicial):
            # Interpolación lineal para el mes exacto
            excedente = acumulado - abs(inversion_inicial)
            fraccion = 1 - (excedente / flujo)
            return t - 1 + fraccion
    return None  # No se recupera
```

### 4.8 Indicadores por Flota (Fleet KPIs)

```
Utilización_% = (Vehículos_Activos / Vehículos_Total) × 100

Disponibilidad_% = (Horas_Operativas / Horas_Totales) × 100

Kilómetros_Totales = Suma(distance_km de todos los Trips)

Costo_Promedio_por_Viaje = Suma(Costos) / Cantidad_de_Viajes

Ingreso_Promedio_por_Viaje = Suma(Revenue) / Cantidad_de_Viajes

Rentabilidad_por_Vehículo = Ganancia_Neta / Cantidad_Vehículos
```

---

## 5. Arquitectura del Sistema

### 5.1 Diagrama de Componentes

```
┌───────────────────────────────────────────────────────────────────────┐
│                    CLIENTES                                     │
├───────────────────────────────────────────────────────────────────────┤
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐     │
│  │ Web Dashboard  │  │  App Móvil   │  │ Chatbot IA  │  │  App Chofer  │     │
│  │  (React 19)   │  │  (React/PWA)│  │  (Chat UI)   │  │  (Flutter/   │     │
│  └───────┬───────┘  └───────┬───────┘  └───────┬───────┘  └───────┬───────┘     │
│           │               │               │               │          │
└──────────────┬──────────────┬──────────────┬──────────────┘          │
                   │               │               │                          │
                   └────────────┬────────────┘                          │
                                │                                          │
                                ▼                                          │
┌───────────────────────────────────────────────────────────────────────┐
│                    BACKEND (FastAPI)                              │
├───────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │  API REST (CRUD + Reportes + Finanzas + GPS)                    │     │
│  │  - Autenticación JWT                                             │     │
│  │  - Vehicles, Drivers, Trips, Costs, Maintenance                  │     │
│  │  - Financial Reports (VAN, TIR, Payback, KPIs)                   │     │
│  │  - GPS Tracking Integration                                       │     │
│  │  - AI Assistant (RAG + LLM)                                       │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────────────┘     │
                                │                                          │
                                ▼                                          │
┌───────────────────────────────────────────────────────────────────────┐
│                    DATA LAYER                                      │
├───────────────────────────────────────────────────────────────────────┤
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐     │
│  │ PostgreSQL    │  │   Redis     │  │  Qdrant    │  │  MinIO     │     │
│  │ (Datos        │  │  (Cache +  │  │  (Vector   │  │  (Archivos │     │
│  │  operativos)  │  │   Sesiones)│  │   DB/RAG)  │  │  recibos)  │     │
│  └────────────┘  └────────────┘  └────────────┘  └────────────┘     │
└─────────────────────────────────────────────────────────────────────┘     │
                                │                                          │
                                ▼                                          │
┌───────────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                               │
├───────────────────────────────────────────────────────────────────────┤
│  ┌────────────┐  ┌────────────┐  ┌────────────┐          │
│  │ Google Maps  │  │  Ollama/   │  │  WhatsApp  │          │
│  │ API (Routes + │  │  OpenAI    │  │  Business  │          │
│  │  Geocoding)   │  │  (LLM)     │  │  API       │          │
│  └────────────┘  └────────────┘  └────────────┘          │
└─────────────────────────────────────────────────────────────────────┘          │
                                                                            │
┌───────────────────────────────────────────────────────────────────────┐
│                    GPS DEVICES                                     │
├───────────────────────────────────────────────────────────────────────┤
│  ┌────────────┐  ┌────────────┐                               │
│  │ GPS Tracker  │  │ Smartphone  │  (App chofer envía GPS)          │
│  │ (Hardware)   │  │  (App)      │                               │
│  └────────────┘  └────────────┘                               │
└─────────────────────────────────────────────────────────────────────┘                               │
                                                                            │
GPS → Backend → PostgreSQL (tabla GPSPosition) → WebSocket → Dashboard     
```

### 5.2 Stack Tecnológico Completo

| Capa | Tecnología | Versión |
|------|-----------|---------|
| **Frontend Web** | React + TypeScript + Vite | 19.x, 5.7.x, 6.x |
| **Frontend Mobile** | React Native / Flutter | - |
| **Backend** | FastAPI + Python | 3.12+, 0.115+ |
| **Base de Datos** | PostgreSQL + TimescaleDB | 15+ |
| **Cache** | Redis | 7+ |
| **Vector DB** | Qdrant | 1.8+ |
| **Storage** | MinIO / S3 | - |
| **GPS/Maps** | Google Maps API | v3 |
| **LLM** | Ollama (local) + OpenAI | - |
| **Mensajería** | WhatsApp Business API | - |
| **Container** | Docker + docker-compose | - |
| **Deploy** | Google Cloud Run | - |

---

## 6. OpenAPI Spec (Esqueleto)

```yaml
openapi: 3.1.0
info:
  title: Fleet Optimizer API
  description: API para gestión de flotas, costos, mantenimiento y análisis financiero
  version: 1.0.0
  contact:
    name: Nelson Acosta
    email: aliagenttucuman@gmail.com

servers:
  - url: http://localhost:8000/api/v1
    description: Development
  - url: https://fleet-api.tudominio.com/api/v1
    description: Production

paths:
  # === VEHICLES ===
  /vehicles:
    get:
      summary: Listar vehículos
      tags: [Vehicles]
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [active, maintenance, inactive, sold]
        - name: page
          in: query
          schema: { type: integer, default: 1 }
        - name: page_size
          in: query
          schema: { type: integer, default: 20 }
      responses:
        200:
          description: Lista de vehículos
          content:
            application/json:
              schema:
                type: object
                properties:
                  items: { type: array, items: { $ref: '#/components/schemas/Vehicle' } }
                  total: { type: integer }
                  page: { type: integer }
                  page_size: { type: integer }
    
    post:
      summary: Crear vehículo
      tags: [Vehicles]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/VehicleCreate' }
      responses:
        201:
          description: Vehículo creado
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Vehicle' }

  /vehicles/{vehicle_id}:
    get:
      summary: Obtener vehículo
      tags: [Vehicles]
      parameters:
        - name: vehicle_id
          in: path
          required: true
          schema: { type: string, format: uuid }
      responses:
        200:
          description: Detalle del vehículo
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Vehicle' }

  /vehicles/{vehicle_id}/location:
    get:
      summary: Última posición GPS
      tags: [GPS]
      responses:
        200:
          description: Posición actual
          content:
            application/json:
              schema: { $ref: '#/components/schemas/GPSPosition' }

  /vehicles/{vehicle_id}/costs:
    get:
      summary: Costos del vehículo
      tags: [Costs]
      parameters:
        - name: from_date
          in: query
          schema: { type: string, format: date }
        - name: to_date
          in: query
          schema: { type: string, format: date }
        - name: category
          in: query
          schema:
            type: string
            enum: [fuel, toll, parking, maintenance, repair, insurance, license, tire, oil, cleaning, driver_salary, other]
      responses:
        200:
          description: Costos filtrados
          content:
            application/json:
              schema:
                type: object
                properties:
                  items: { type: array, items: { $ref: '#/components/schemas/OperatingCost' } }
                  total_amount: { type: number }

  /vehicles/{vehicle_id}/financial-summary:
    get:
      summary: Resumen financiero del vehículo
      tags: [Financial]
      parameters:
        - name: period
          in: query
          schema:
            type: string
            enum: [daily, weekly, monthly, yearly, all_time]
            default: monthly
      responses:
        200:
          description: Resumen financiero
          content:
            application/json:
              schema: { $ref: '#/components/schemas/VehicleFinancialSummary' }

  # === TRIPS ===
  /trips:
    get:
      summary: Listar viajes
      tags: [Trips]
      parameters:
        - name: vehicle_id
          in: query
          schema: { type: string, format: uuid }
        - name: driver_id
          in: query
          schema: { type: string, format: uuid }
        - name: status
          in: query
          schema:
            type: string
            enum: [planned, in_progress, completed, cancelled]
        - name: from_date
          in: query
          schema: { type: string, format: date }
        - name: to_date
          in: query
          schema: { type: string, format: date }
      responses:
        200:
          description: Lista de viajes
          content:
            application/json:
              schema:
                type: object
                properties:
                  items: { type: array, items: { $ref: '#/components/schemas/Trip' } }
                  total: { type: integer }
    
    post:
      summary: Crear viaje
      tags: [Trips]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/TripCreate' }
      responses:
        201:
          description: Viaje creado
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Trip' }

  /trips/{trip_id}/complete:
    post:
      summary: Completar viaje
      tags: [Trips]
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                end_time: { type: string, format: date-time }
                distance_km: { type: number }
                revenue: { type: number }
      responses:
        200:
          description: Viaje completado
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Trip' }

  # === COSTS ===
  /costs:
    post:
      summary: Registrar costo
      tags: [Costs]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/OperatingCostCreate' }
      responses:
        201:
          description: Costo registrado
          content:
            application/json:
              schema: { $ref: '#/components/schemas/OperatingCost' }

  # === MAINTENANCE ===
  /maintenance:
    get:
      summary: Listar registros de mantenimiento
      tags: [Maintenance]
      parameters:
        - name: vehicle_id
          in: query
          schema: { type: string, format: uuid }
        - name: status
          in: query
          schema:
            type: string
            enum: [scheduled, in_progress, completed, cancelled]
      responses:
        200:
          description: Lista de mantenimientos
          content:
            application/json:
              schema:
                type: object
                properties:
                  items: { type: array, items: { $ref: '#/components/schemas/MaintenanceRecord' } }
    
    post:
      summary: Crear orden de mantenimiento
      tags: [Maintenance]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/MaintenanceRecordCreate' }
      responses:
        201:
          description: Orden creada
          content:
            application/json:
              schema: { $ref: '#/components/schemas/MaintenanceRecord' }

  # === FINANCIAL ANALYSIS ===
  /financial/van:
    post:
      summary: Calcular VAN (Valor Actual Neto)
      tags: [Financial Analysis]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/VANCalculationRequest' }
      responses:
        200:
          description: Resultado VAN
          content:
            application/json:
              schema: { $ref: '#/components/schemas/VANCalculationResult' }

  /financial/tir:
    post:
      summary: Calcular TIR (Tasa Interna de Retorno)
      tags: [Financial Analysis]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/TIRCalculationRequest' }
      responses:
        200:
          description: Resultado TIR
          content:
            application/json:
              schema: { $ref: '#/components/schemas/TIRCalculationResult' }

  /financial/fleet-kpis:
    get:
      summary: KPIs de la flota
      tags: [Financial Analysis]
      parameters:
        - name: from_date
          in: query
          schema: { type: string, format: date }
        - name: to_date
          in: query
          schema: { type: string, format: date }
      responses:
        200:
          description: KPIs de la flota
          content:
            application/json:
              schema: { $ref: '#/components/schemas/FleetKPIs' }

  # === AI ASSISTANT ===
  /ai/ask:
    post:
      summary: Consultar al asistente IA
      tags: [AI Assistant]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                question: { type: string, example: "¿Cuánto ganó el camión ABC123 este mes?" }
                context: { type: string, description: "Contexto adicional (opcional)" }
      responses:
        200:
          description: Respuesta del asistente
          content:
            application/json:
              schema:
                type: object
                properties:
                  answer: { type: string }
                  data: { type: object, description: "Datos estructurados relacionados" }
                  confidence: { type: number, description: "Confianza de la respuesta" }

  /ai/report:
    post:
      summary: Generar reporte con IA
      tags: [AI Assistant]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                type: { type: string, enum: [daily, weekly, monthly, custom] }
                vehicle_ids: { type: array, items: { type: string } }
                format: { type: string, enum: [text, json, pdf], default: text }
      responses:
        200:
          description: Reporte generado
          content:
            application/json:
              schema:
                type: object
                properties:
                  report: { type: string }
                  summary: { type: string }
                  recommendations: { type: array, items: { type: string } }

  # === GPS ===
  /gps/track:
    post:
      summary: Recibir posición GPS (desde app del chofer)
      tags: [GPS]
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/GPSPositionCreate' }
      responses:
        201:
          description: Posición registrada

  /gps/vehicles:
    get:
      summary: Posiciones actuales de todos los vehículos
      tags: [GPS]
      responses:
        200:
          description: Lista de posiciones
          content:
            application/json:
              schema:
                type: object
                properties:
                  vehicles: { type: array, items: { $ref: '#/components/schemas/VehicleWithLocation' } }

  # === WEBSOCKET (Real-time) ===
  /ws/gps:
    get:
      summary: WebSocket para tracking en tiempo real
      tags: [GPS]
      responses:
        101:
          description: Switching Protocols

components:
  schemas:
    Vehicle:
      type: object
      properties:
        id: { type: string, format: uuid }
        plate: { type: string }
        brand: { type: string }
        model: { type: string }
        year: { type: integer }
        type: { type: string, enum: [truck, van, car, motorcycle, trailer] }
        capacity_kg: { type: number }
        fuel_type: { type: string, enum: [diesel, gasoline, cng, electric] }
        fuel_consumption_km_per_liter: { type: number }
        acquisition_cost: { type: number }
        acquisition_date: { type: string, format: date }
        depreciation_years: { type: integer }
        current_odometer_km: { type: number }
        status: { type: string, enum: [active, maintenance, inactive, sold] }
        gps_device_id: { type: string }
        assigned_driver_id: { type: string, format: uuid, nullable: true }
        insurance_policy: { type: string }
        insurance_expiry: { type: string, format: date }
        created_at: { type: string, format: date-time }
        updated_at: { type: string, format: date-time }

    VehicleCreate:
      type: object
      required: [plate, brand, model, year, type, acquisition_cost]
      properties:
        plate: { type: string }
        brand: { type: string }
        model: { type: string }
        year: { type: integer }
        type: { type: string, enum: [truck, van, car, motorcycle, trailer] }
        capacity_kg: { type: number }
        fuel_type: { type: string, enum: [diesel, gasoline, cng, electric], default: diesel }
        fuel_consumption_km_per_liter: { type: number }
        acquisition_cost: { type: number }
        acquisition_date: { type: string, format: date }
        depreciation_years: { type: integer, default: 5 }
        gps_device_id: { type: string }
        assigned_driver_id: { type: string, format: uuid }
        insurance_policy: { type: string }
        insurance_expiry: { type: string, format: date }

    Trip:
      type: object
      properties:
        id: { type: string, format: uuid }
        vehicle_id: { type: string, format: uuid }
        driver_id: { type: string, format: uuid }
        start_time: { type: string, format: date-time }
        end_time: { type: string, format: date-time, nullable: true }
        origin: { $ref: '#/components/schemas/GeoPoint' }
        destination: { $ref: '#/components/schemas/GeoPoint', nullable: true }
        distance_km: { type: number }
        route: { type: array, items: { $ref: '#/components/schemas/GeoPoint' } }
        status: { type: string, enum: [planned, in_progress, completed, cancelled] }
        revenue: { type: number }
        notes: { type: string }
        created_at: { type: string, format: date-time }

    TripCreate:
      type: object
      required: [vehicle_id, driver_id, origin]
      properties:
        vehicle_id: { type: string, format: uuid }
        driver_id: { type: string, format: uuid }
        origin: { $ref: '#/components/schemas/GeoPoint' }
        destination: { $ref: '#/components/schemas/GeoPoint' }
        estimated_distance_km: { type: number }
        revenue: { type: number }
        notes: { type: string }

    OperatingCost:
      type: object
      properties:
        id: { type: string, format: uuid }
        vehicle_id: { type: string, format: uuid }
        trip_id: { type: string, format: uuid, nullable: true }
        category: { type: string, enum: [fuel, toll, parking, maintenance, repair, insurance, license, tire, oil, cleaning, driver_salary, other] }
        amount: { type: number }
        currency: { type: string, default: ARS }
        date: { type: string, format: date }
        description: { type: string }
        receipt_url: { type: string, nullable: true }
        odometer_km_at_cost: { type: number }
        created_by: { type: string, format: uuid }
        created_at: { type: string, format: date-time }

    OperatingCostCreate:
      type: object
      required: [vehicle_id, category, amount, date]
      properties:
        vehicle_id: { type: string, format: uuid }
        trip_id: { type: string, format: uuid }
        category: { type: string, enum: [fuel, toll, parking, maintenance, repair, insurance, license, tire, oil, cleaning, driver_salary, other] }
        amount: { type: number }
        currency: { type: string, default: ARS }
        date: { type: string, format: date }
        description: { type: string }
        odometer_km_at_cost: { type: number }

    MaintenanceRecord:
      type: object
      properties:
        id: { type: string, format: uuid }
        vehicle_id: { type: string, format: uuid }
        type: { type: string, enum: [preventive, corrective, inspection] }
        scheduled_date: { type: string, format: date }
        completed_date: { type: string, format: date, nullable: true }
        description: { type: string }
        labor_cost: { type: number }
        parts_cost: { type: number }
        total_cost: { type: number }
        odometer_km_at_service: { type: number }
        next_service_odometer_km: { type: number, nullable: true }
        next_service_date: { type: string, format: date, nullable: true }
        status: { type: string, enum: [scheduled, in_progress, completed, cancelled] }
        performed_by: { type: string }
        created_at: { type: string, format: date-time }

    MaintenanceRecordCreate:
      type: object
      required: [vehicle_id, type, scheduled_date, description]
      properties:
        vehicle_id: { type: string, format: uuid }
        type: { type: string, enum: [preventive, corrective, inspection] }
        scheduled_date: { type: string, format: date }
        description: { type: string }
        labor_cost: { type: number }
        parts_cost: { type: number }
        next_service_odometer_km: { type: number }
        next_service_date: { type: string, format: date }

    GPSPosition:
      type: object
      properties:
        id: { type: string, format: uuid }
        vehicle_id: { type: string, format: uuid }
        latitude: { type: number }
        longitude: { type: number }
        altitude: { type: number, nullable: true }
        speed_kmh: { type: number }
        heading: { type: number }
        timestamp: { type: string, format: date-time }
        ignition_status: { type: boolean }

    GPSPositionCreate:
      type: object
      required: [vehicle_id, latitude, longitude, timestamp]
      properties:
        vehicle_id: { type: string, format: uuid }
        latitude: { type: number }
        longitude: { type: number }
        altitude: { type: number }
        speed_kmh: { type: number }
        heading: { type: number }
        timestamp: { type: string, format: date-time }
        ignition_status: { type: boolean }

    GeoPoint:
      type: object
      properties:
        lat: { type: number }
        lng: { type: number }
        address: { type: string, nullable: true }

    VehicleWithLocation:
      allOf:
        - { $ref: '#/components/schemas/Vehicle' }
        - type: object
          properties:
            current_location: { $ref: '#/components/schemas/GPSPosition' }
            last_seen_minutes_ago: { type: integer }

    VehicleFinancialSummary:
      type: object
      properties:
        vehicle_id: { type: string, format: uuid }
        period: { type: string }
        total_revenue: { type: number }
        total_operating_costs: { type: number }
        total_maintenance_costs: { type: number }
        net_profit: { type: number }
        profit_margin_pct: { type: number }
        cost_per_km: { type: number }
        km_traveled: { type: number }
        trips_count: { type: integer }
        fuel_consumed_liters: { type: number }
        fuel_cost: { type: number }
        tco_to_date: { type: number }
        depreciation_to_date: { type: number }

    VANCalculationRequest:
      type: object
      required: [vehicle_id, discount_rate, projection_months]
      properties:
        vehicle_id: { type: string, format: uuid }
        discount_rate_annual: { type: number, description: "Tasa anual (ej: 0.80 para 80%)" }
        projection_months: { type: integer, default: 60 }
        include_residual_value: { type: boolean, default: true }

    VANCalculationResult:
      type: object
      properties:
        vehicle_id: { type: string, format: uuid }
        van: { type: number }
        investment: { type: number }
        projected_cash_flows: { type: array, items: { type: number } }
        discount_rate_monthly: { type: number }
        recommendation: { type: string, enum: [keep, replace, analyze_further] }

    TIRCalculationRequest:
      type: object
      required: [vehicle_id, projection_months]
      properties:
        vehicle_id: { type: string, format: uuid }
        projection_months: { type: integer, default: 60 }

    TIRCalculationResult:
      type: object
      properties:
        vehicle_id: { type: string, format: uuid }
        tir_monthly: { type: number }
        tir_annual: { type: number }
        investment: { type: number }
        projected_cash_flows: { type: array, items: { type: number } }
        recommendation: { type: string }

    FleetKPIs:
      type: object
      properties:
        period: { type: string }
        total_vehicles: { type: integer }
        active_vehicles: { type: integer }
        utilization_pct: { type: number }
        availability_pct: { type: number }
        total_km: { type: number }
        total_revenue: { type: number }
        total_costs: { type: number }
        net_profit: { type: number }
        fleet_margin_pct: { type: number }
        avg_cost_per_km: { type: number }
        avg_revenue_per_trip: { type: number }
        total_trips: { type: integer }
        maintenance_pending: { type: integer }
        fuel_consumed_liters: { type: number }
        fuel_efficiency_km_per_liter: { type: number }
        top_performing_vehicles: { type: array, items: { type: object } }
        underperforming_vehicles: { type: array, items: { type: object } }

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

---

## 7. Módulo de IA (Asistente Conversacional)

### 7.1 Capacidades del Asistente

El asistente debe poder responder preguntas como:

**Consultas operativas:**
- "¿Dónde está el camión ABC123 ahora?"
- "¿Qué vehículos están en mantenimiento?"
- "¿Cuántos viajes hizo el chofer Juan esta semana?"

**Consultas financieras:**
- "¿Cuánto ganó el vehículo XYZ789 este mes?"
- "¿Cuál es el costo por kilómetro de la flota?"
- "¿Cuál vehículo tiene mayor margen de ganancia?"
- "Calculá el VAN del camión ABC123 con tasa 80%"

**Análisis y reportes:**
- "Generá un reporte mensual de la flota"
- "¿Qué vehículos debería reemplazar?"
- "¿Cuáles son las tendencias de combustible?"

**Predicciones (con IA):**
- "¿Cuándo necesita mantenimiento el vehículo ABC123?"
- "¿Cuál es el costo estimado del próximo mes?"
- "¿Qué ruta es más eficiente desde A hasta B?"

### 7.2 Arquitectura del Asistente

```
Pregunta del usuario
    ↓
[NLU] - Entender intención y entidades
    ↓
[Query Builder] - Convertir a SQL/API call
    ↓
[Database / API] - Obtener datos
    ↓
[Formatter] - Formatear resultado
    ↓
[LLM] - Generar respuesta natural
    ↓
Respuesta al usuario
```

### 7.3 Implementación (RAG)

- **Documentos indexados:** Manual de usuario, glosario de términos financieros, reglas de negocio
- **Vector DB:** Qdrant para embeddings de preguntas frecuentes
- **LLM:** Ollama (local) para respuestas rápidas + OpenAI como fallback
- **Contexto:** Cada conversación incluye el estado actual de la flota

---

## 8. Flujo de Datos Típico

### Escenario: Un día de operación

```
06:00 AM - Chofer abre app, inicia viaje
    → POST /trips (status: in_progress)
    → App envía GPS cada 30 segundos → POST /gps/track

08:30 AM - Chofer carga combustible $15.000
    → POST /costs (category: fuel, amount: 15000)
    → App sube foto del ticket → MinIO

10:00 AM - Paga peaje $2.500
    → POST /costs (category: toll, amount: 2500)

12:00 PM - Completa entrega, finaliza viaje
    → POST /trips/{id}/complete
    → Sistema calcula: distancia, costos, ganancia

01:00 PM - Gerente pregunta a IA: "¿Cuánto llevamos hoy?"
    → POST /ai/ask
    → IA responde con resumen en tiempo real

05:00 PM - Fin del día
    → Sistema genera resumen diario automático
    → Alertas de mantenimiento próximo
    → KPIs actualizados
```

---

## 9. Roadmap de Implementación

### Fase 1: MVP (Mínimo Producto Viable) - 4 semanas
- [ ] Backend: CRUD de Vehículos, Viajes, Costos
- [ ] Base de datos PostgreSQL
- [ ] App del chofer: inicio/fin de viaje + carga de costos
- [ ] Dashboard básico: lista de vehículos, costos diarios
- [ ] Reporte simple: ganancia neta por vehículo (diario/semanal)

### Fase 2: Finanzas Avanzadas - 3 semanas
- [ ] Cálculo automático de TCO, CPK, margen
- [ ] Módulo VAN y TIR
- [ ] Dashboard de KPIs de flota
- [ ] Comparativas entre vehículos
- [ ] Proyecciones financieras

### Fase 3: GPS y Tracking - 3 semanas
- [ ] Integración Google Maps API
- [ ] App del chofer envía posición GPS
- [ ] Mapa en tiempo real en dashboard
- [ ] Historial de rutas
- [ ] Geofencing (alertas por zona)

### Fase 4: IA y Asistente - 3 semanas
- [ ] RAG con documentación de la flota
- [ ] Chatbot con acceso a datos en tiempo real
- [ ] Reportes generados por IA
- [ ] Predicciones de mantenimiento
- [ ] Recomendaciones de eficiencia

### Fase 5: Mantenimiento y Alertas - 2 semanas
- [ ] Programación de mantenimiento
- [ ] Alertas automáticas (km, tiempo, costos)
- [ ] Historial de servicios
- [ ] Órdenes de trabajo

### Fase 6: Escalabilidad - 2 semanas
- [ ] Multi-cliente (tenant)
- [ ] Roles y permisos
- [ ] API pública para integraciones
- [ ] WhatsApp Business para notificaciones

**Total estimado:** ~17 semanas (4 meses) para versión completa

---

## 10. Modelo de Negocio Sugerido

### SaaS (Software as a Service)

| Plan | Vehículos | Precio (ARS/mes) | Incluye |
|------|-----------|------------------|---------|
| **Starter** | Hasta 5 | $50.000 | Tracking, costos básicos, 1 usuario |
| **Professional** | Hasta 20 | $120.000 | + VAN/TIR, mantenimiento, IA básica, 5 usuarios |
| **Enterprise** | Ilimitado | $300.000 | + IA avanzada, API, WhatsApp, soporte 24/7, ilimitado |

### Implementación Inicial
- **Setup:** $200.000 - $500.000 ARS (configuración, importación de datos, capacitación)
- **Tiempo de implementación:** 2-4 semanas

---

## 11. Ventaja Competitiva

| Característica | Samsara | Geotab | Wialon | **Fleet Optimizer** |
|----------------|---------|--------|--------|---------------------|
| GPS Tracking | ✅ | ✅ | ✅ | ✅ |
| Costos | ✅ | ✅ | ⚠️ | ✅ |
| Mantenimiento | ✅ | ⚠️ | ⚠️ | ✅ |
| VAN/TIR | ❌ | ❌ | ❌ | ✅ |
| Asistente IA | ❌ | ❌ | ❌ | ✅ |
| WhatsApp | ❌ | ❌ | ❌ | ✅ |
| Precio LatAm | $$$$ | $$$$ | $$$ | **$$** |
| Idioma Español | ⚠️ | ⚠️ | ✅ | ✅ |

**Diferenciador único:** Ninguno ofrece VAN/TIR + Asistente IA conversacional en español.

---

## 12. Checklist de Inicio

- [ ] Definir nombre comercial del producto
- [ ] Registrar dominio
- [ ] Crear cuenta de Google Cloud (para Maps API)
- [ ] Configurar WhatsApp Business API
- [ ] Definir flota piloto (1-3 vehículos para prueba)
- [ ] Reunión con cliente potencial para validar necesidades
- [ ] Presupuesto inicial de desarrollo
- [ ] Asignar equipo: Beto (spec), Ricky (backend), Nico (frontend), Diego (infra)

---

**Autor:** Nelson Acosta (CEO) + JARVIS
**Fecha:** 2026-05-12
**Versión:** 1.0.0
**Siguiente paso:** Validar especificación con Pablo + Gino (gestión) + Luigi (finanzas)
