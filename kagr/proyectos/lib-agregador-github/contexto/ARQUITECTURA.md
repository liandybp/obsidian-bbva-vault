# Arquitectura - lib-agregador-github

## Vista Conceptual

```
┌─────────────────────────────────────────────────────────┐
│                    ENGINES (Consumidores)               │
│  (Agregador, User Space, Proloan - Tienen configuración) │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼ (dependen de)
┌─────────────────────────────────────────────────────────┐
│           lib-agregador-github (KAGR)                   │
│         (Lógica centralizada, sin configuración)        │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  DefaultTaggingLogic (orquestador)               │  │
│  │  - Coordina brand, category, payment_method, ..  │  │
│  │  - Usa catálogos pasados por engine              │  │
│  └────────────────┬─────────────────────────────────┘  │
│                   │                                      │
│  ┌────────────────┴──────────────────────────────────┐  │
│  │              Clasificadores                       │  │
│  │  ├─ BrandLogic                                   │  │
│  │  ├─ CategoryLogic (con regex)                    │  │
│  │  ├─ PaymentMethodLogic                           │  │
│  │  ├─ PaymentPlatformLogic                         │  │
│  │  └─ SubscriptionLogic                            │  │
│  └────────────────┬─────────────────────────────────┘  │
│                   │                                      │
│  ┌────────────────┴──────────────────────────────────┐  │
│  │         Catálogos (Readers)                       │  │
│  │  ├─ BrandsCatalogReader                          │  │
│  │  ├─ CategoryRegexCatalogReader                   │  │
│  │  ├─ PaymentMethodsCatalogReader                  │  │
│  │  ├─ PaymentPlatformsCatalogReader                │  │
│  │  └─ CoherenceCatalogReader                       │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │         Tablas (Schema Definitions)              │  │
│  │  ├─ MovementsTable                               │  │
│  │  ├─ TaggingTable                                 │  │
│  │  └─ ClassificationsTable                         │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │           Utilidades                             │  │
│  │  ├─ config.py (helpers)                          │  │
│  │  ├─ spark.py (helpers)                           │  │
│  │  ├─ decorators.py                                │  │
│  │  ├─ exceptions.py                                │  │
│  │  └─ metrics/                                     │  │
│  └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## Flujo de Datos Típico

```
Engine (config con paths)
    │
    ▼
DailyTaggingJob.logic()
    │
    ├─ CatalogReader.load_catalogs(config, CATALOGS_CONFIG)
    │   ├─ Lee paths desde config del engine
    │   ├─ Instancia readers (BrandsCatalogReader, etc.)
    │   └─ Retorna dict de catálogos
    │
    ▼
DefaultTaggingLogic(**catalogs)
    │
    ├─ BrandLogic.apply(movements, brands_catalog)
    ├─ CategoryLogic.apply(movements, category_catalog)
    ├─ PaymentMethodLogic.apply(movements, payment_methods_catalog)
    ├─ PaymentPlatformLogic.apply(movements, payment_platforms_catalog)
    └─ SubscriptionLogic.apply(movements, subscription_config)
    
    ▼
Movimientos etiquetados → Engine
```

## Componentes Clave

### 1. DefaultTaggingLogic (`tagging_logic.py`)
- **Responsabilidad**: Orquestación principal
- **Input**: Movimientos + catálogos
- **Output**: Movimientos etiquetados
- **Interacción**: Coordinador entre clasificadores

### 2. Clasificadores (`brand/`, `category/`, etc.)
- **Responsabilidad**: Lógica específica de cada dominio
- **Input**: Movimientos + catálogo relevante
- **Output**: Movimientos con etiqueta específica
- **Pattern**: Implementan interfaz común

### 3. Readers (`*/catalogs/`)
- **Responsabilidad**: Transformar datos a formato usable
- **Input**: DataFrame desde CSV/DB
- **Output**: Diccionario tipado (ej: `CategoryRegexCatalog`)
- **Agnóstico**: No conocen fuente específica

### 4. Jobs (`tagging_jobs.py`)
- **Responsabilidad**: Orquestación de Spark
- **Define**: `CATALOGS_CONFIG` (lista de catálogos)
- **Implementa**: `DailyTaggingJob`, `DailyTaggingJobNoCust`
- **Interfaz**: Hereda de `TaggingJob` (librería global)

### 5. Tablas (`kagr/tables/`)
- **Responsabilidad**: Definiciones de esquema
- **Interacción**: Se usan en jobs para write/read

---

## Dependencias Externas

### Librerías Propias
- `glglcsadvpyglobaltagger` — Librería global de tagging
  - Proporciona: `TaggingJob`, `CatalogReader`, readers base
  - Usada por: `DailyTaggingJob`, etc.

### Librerías Externas
- `pyspark` — Procesamiento distribuido
- `pyhocon` — Parsing de configuración
- `memoized_property` — Caching de propiedades

---

## Puntos de Extensión

### 1. Agregar Nuevo Clasificador
```
1. Crear carpeta en kagr/default_logic/nuevo_dominio/
2. Implementar lógica (heredar patrón existente)
3. Agregar CatalogConf en CATALOGS_CONFIG
4. Integrar en DefaultTaggingLogic
```

### 2. Agregar Nuevo Catálogo
```
1. Crear reader en default_logic/*/catalogs/
2. Agregar CSV de ejemplo en resources/kagr/catalogs/
3. Documentar en CATÁLOGOS.md
4. Actualizar CATALOGS_CONFIG
```

### 3. Cambiar Lógica de Tagging
```
1. Editar clasificador relevante
2. NO cambiar estructura de catálogos
3. Mantener compatibilidad con engines
4. Documentar breaking changes
```

---

## Principios Arquitectónicos

✅ **Single Responsibility**: Cada componente tiene una responsabilidad clara
✅ **Separation of Concerns**: Lógica ≠ Configuración (config en engines)
✅ **Agnóstica**: No sabe de deployments específicos
✅ **Stateless**: Sin estado compartido
✅ **Reutilizable**: Múltiples engines pueden consumirla
✅ **Testeable**: Componentes desacoplados

---

## Referencias

- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION]]
- [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA]]
- [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS]]
- [[kagr/proyectos/lib-agregador-github/referencias/ENGINES]]

