# lib_agregador_github

## Descripción

`lib_agregador_github` es la **librería central (dependencia reutilizable) que gestiona toda la lógica de tagging y catálogos** para los procesos de agregación de movimientos bancarios.

⚠️ **Importante**: Esta librería **NO lleva configuración propia**. La configuración reside en los **engines** (aplicaciones consumidoras) que usan esta librería como dependencia.

## 🔑 Nota Clave

> **Todas las regex de Agregador, User Space y Proloan se gestionan desde esta librería.** Es el punto único de verdad (single source of truth) para la **lógica** de patrones de identificación de categorías, marcas, métodos de pago y plataformas de pago.
> 
> La **configuración específica** (paths, valores por entorno) está en los engines que consumen esta librería.

## Localización

```
/Users/t022458/PycharmProjects/kagr/lib/lib_agregador_github/
```

## Estructura Principal

### Módulo: `eskagrtaggerf6d3lcdgsmfgisp`

Es el núcleo del proyecto con la siguiente estructura:

#### 1. **Lógica de Tagging** (`kagr/default_logic/`)
- `tagging_logic.py`: Lógica por defecto para etiquetar movimientos
- `brand/`: Lógica de identificación de marcas
- `category/`: Lógica de categorización (incluye regex)
- `payment_method/`: Métodos de pago
- `payment_platform/`: Plataformas de pago

#### 2. **Catálogos Regex** (`resources/`)
Archivos de ejemplo y plantillas (la lógica que consumen los engines):
- `kagr/catalogs/`: Archivos CSV de ejemplo para regex
  - `gastos_regex.csv`: Patrones para categorización de gastos (ejemplo)
  - `ingresos_regex.csv`: Patrones para categorización de ingresos (ejemplo)

⚠️ Los archivos `.conf` (aplicacion.conf, catalogs.conf, proloans_catalogs.conf) **NO están en la librería** — están en los engines consumidores.

#### 3. **Jobs de Tagging** (`kagr/runtime/batch/jobs/`)
- `tagging_jobs.py`: Define `DailyTaggingJob` y `DailyTaggingJobNoCust`
- Carga los catálogos en `CATALOGS_CONFIG`

## Catálogos Gestionados

Se configuran en `CATALOGS_CONFIG` (tagging_jobs.py, líneas 87-95):

| Catálogo | Config Key | Reader | Path |
|----------|-----------|--------|------|
| brands_regex_catalog | `BRANDS_REGEX_CATALOG_PATH` | RuleBrandsCatalogReader | En conf |
| brands_catalog | `BRANDS_CATALOG_PATH` | BrandsCatalogReader | En conf |
| coherence_catalog | `BRANDS_COHERENCE_CATALOG_PATH` | CoherenceCatalogReader | En conf |
| payment_methods_regex_catalog | `PAYMENT_METHODS_REGEX_CATALOG_PATH` | PaymentMethodsRegexCatalogReader | En conf |
| payment_methods_catalog | `PAYMENT_METHODS_CATALOG_PATH` | PaymentMethodsCatalogReader | En conf |
| payment_platforms_regex_catalog | `PAYMENT_PLATFORMS_REGEX_CATALOG_PATH` | PaymentPlatformsRegexCatalogReader | En conf |
| payment_platforms_catalog | `PAYMENT_PLATFORMS_CATALOG_PATH` | PaymentPlatformsCatalogReader | En conf |
| **positive_regex_catalog** | `POSITIVE_REGEX_CATALOG_PATH` | CategoryRegexCatalogReader | `kagr/catalogs/` |
| **negative_regex_catalog** | `NEGATIVE_REGEX_CATALOG_PATH` | CategoryRegexCatalogReader | `kagr/catalogs/` |

## Configuración (en los Engines, NO en la librería)

⚠️ **Aclaración crítica**: Los archivos de configuración están en los **engines consumidores**, NO en `lib_agregador_github`.

La librería es agnóstica — los engines definen:
- Dónde están los CSVs de catálogos
- Qué paths usar por entorno
- Cómo overwritear configuración

Ejemplo de files en engines que consumen esta librería:
- `application.conf` — Define claves de paths (`BRANDS_REGEX_CATALOG_PATH`, `POSITIVE_REGEX_CATALOG_PATH`, etc.)
- `catalogs.conf` — Paths reales por entorno (dev, sandbox, prod)
- `proloans_catalogs.conf` — Configuración específica para Proloan

## Ejemplos de Archivos CSV

### `gastos_regex.csv`
```csv
REGEX,SUBCATEGORY
"(?i).*(\b)(alquiler).*(\b)(coche)(\b).*",90
"(?i).*(\b)(viaje)(\b).*",91
"(?i).*(\b)(lazeria)(\b).*",54
```

### `ingresos_regex.csv`
Patrones similares para ingresos.

## Envíos y Flujos de Uso

### 1. **DailyTaggingJob**
- Carga catálogos desde config
- Instancia `DefaultTaggingLogic` con todos los catálogos
- Procesa movimientos diarios

### 2. **DailyTaggingJobNoCust**
- Variante sin procesamiento de clientes específicos

## Modificación de Regex (¿Dónde?)

### En la Librería (`lib_agregador_github`)
Si modificas la **lógica o estructura** de regex:
1. Edita archivos de la librería en `/Users/t022458/PycharmProjects/kagr/lib/lib_agregador_github/`
2. Redeploy de la librería como dependencia (`pip install`)

### En los Engines (Consumidores)
Si modificas **qué regex usar** o **dónde están los CSVs**:
1. Edita `catalogs.conf` o `proloans_catalogs.conf` del engine
2. Redeploy del engine (aplicación)

### Archivos de Catálogos CSV
- Pueden estar en la librería (como ejemplos)
- Típicamente se gestionar desde los engines o desde repositorios de datos externos

## Dependencias Principales

- `pyspark`: Procesamiento distribuido
- `pyhocon`: Parsing de configuración HOCON
- `glglcsadvpyglobaltagger`: Librería global de tagging (dependencia externa)
- `memoized_property`: Caching de propiedades

## Notas Importantes

- ✅ **Single Source of Truth para Lógica**: Todos los catálogos de regex pasan por aquí (una sola implementación)
- ✅ **Librería agnóstica**: Sin estado ni configuración — los engines la consumen según sus necesidades
- ⚠️ **Redeploy necesario**: Cambios en la lógica de esta librería requieren redeploy de todos los engines
- ⚠️ **Configuración en engines**: No busques archivos `.conf` aquí — están en las aplicaciones consumidoras
- 🔄 **Reutilizable**: Múltiples engines pueden consumir la misma versión con diferentes configuraciones

## Referencias Relacionadas

- [[kpfm/proyectos/kpfm-etiquetas/referencias/CONFIGURACION]]
- [[kpfm/proyectos/kpfm-etiquetas/contexto/ARQUITECTURA]]
- [[kpfm/proyectos/kpfm-etiquetas/referencias/SPARK_FUNCTIONS]]






