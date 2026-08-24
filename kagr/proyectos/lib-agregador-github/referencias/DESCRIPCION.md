# Descripción Técnica - lib-agregador-github

## ¿Qué es?

`lib_agregador_github` es la **librería central (dependencia reutilizable) que gestiona toda la lógica de tagging y catálogos** para los procesos de agregación de movimientos bancarios.

## Rol

- **Librería agnóstica**: Sin configuración propia
- **Eje central de lógica**: Todos los engines (Agregador, User Space, Proloan) la consumen
- **Single source of truth**: Evita duplicación de código de tagging

## Estructura Principal

### Módulo: `eskagrtaggerf6d3lcdgsmfgisp`

Es el núcleo del proyecto:

#### 1. **Lógica de Tagging** (`kagr/default_logic/`)
- `tagging_logic.py`: Lógica por defecto
- `brand/`: Identificación de marcas
- `category/`: Categorización (incluye regex)
- `payment_method/`: Métodos de pago
- `payment_platform/`: Plataformas de pago
- `subscription/`: Lógica de suscripciones

#### 2. **Catálogos de Ejemplo** (`resources/kagr/catalogs/`)
- `gastos_regex.csv`: Patrones de gastos (ejemplo)
- `ingresos_regex.csv`: Patrones de ingresos (ejemplo)

#### 3. **Jobs de Tagging** (`kagr/runtime/batch/jobs/`)
- `tagging_jobs.py`: Define `DailyTaggingJob` y `DailyTaggingJobNoCust`
- Orquesta la carga de catálogos mediante `CATALOGS_CONFIG`

## Catálogos Gestionados

| Catálogo | Reader | Propósito |
|----------|--------|-----------|
| brands_regex_catalog | RuleBrandsCatalogReader | Regex para marcas |
| brands_catalog | BrandsCatalogReader | Datos maestros de marcas |
| coherence_catalog | CoherenceCatalogReader | Coherencia de marcas |
| payment_methods_regex_catalog | PaymentMethodsRegexCatalogReader | Regex para métodos |
| payment_methods_catalog | PaymentMethodsCatalogReader | Datos de métodos |
| payment_platforms_regex_catalog | PaymentPlatformsRegexCatalogReader | Regex para plataformas |
| payment_platforms_catalog | PaymentPlatformsCatalogReader | Datos de plataformas |
| positive_regex_catalog | CategoryRegexCatalogReader | Regex positivas de categoría |
| negative_regex_catalog | CategoryRegexCatalogReader | Regex negativas de categoría |

## Dependencias Principales

- `pyspark`: Procesamiento distribuido
- `pyhocon`: Parsing HOCON
- `glglcsadvpyglobaltagger`: Librería global (dependencia externa)
- `memoized_property`: Caching

## Referencias

- [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA]]
- [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS]]


