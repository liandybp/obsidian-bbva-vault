# Catálogos - lib-agregador-github

## ¿Qué son los Catálogos?

Conjuntos de reglas (regex) y datos maestros que usa la librería para clasificar movimientos bancarios en:
- **Categorías** (gastos, ingresos, finanzas, etc.)
- **Marcas** (Carrefour, Amazon, etc.)
- **Métodos de pago** (Tarjeta, Bizum, etc.)
- **Plataformas de pago** (Visa, MasterCard, etc.)

## Catálogos Principales

### 1. **Regex de Categorización** (category)

#### `positive_regex_catalog`
Patrones que **caracterizan movimientos como pertenecientes a una categoría**.

Ejemplo: `(?i).*(\b)(viaje)(\b).*` → Categoría "Viajes"

**Path config**: `POSITIVE_REGEX_CATALOG_PATH`

#### `negative_regex_catalog`
Patrones que **excluyen movimientos de una categoría**.

Ejemplo: `(?i).*(\b)(loter.a)(\b).*` → NO es un gasto normal

**Path config**: `NEGATIVE_REGEX_CATALOG_PATH`

### 2. **Catálogos de Marcas**

#### `brands_regex_catalog`
Regex para identificar marcas por descripción.

**Reader**: `RuleBrandsCatalogReader`
**Path config**: `BRANDS_REGEX_CATALOG_PATH`

#### `brands_catalog`
Datos maestros de marcas (IDs, nombres, categorías).

**Reader**: `BrandsCatalogReader`
**Path config**: `BRANDS_CATALOG_PATH`

#### `coherence_catalog`
Validación de coherencia entre marcas.

**Reader**: `CoherenceCatalogReader`
**Path config**: `BRANDS_COHERENCE_CATALOG_PATH`

### 3. **Catálogos de Métodos de Pago**

#### `payment_methods_regex_catalog`
Regex para identificar métodos de pago.

**Reader**: `PaymentMethodsRegexCatalogReader`
**Path config**: `PAYMENT_METHODS_REGEX_CATALOG_PATH`

#### `payment_methods_catalog`
Datos maestros de métodos de pago.

**Reader**: `PaymentMethodsCatalogReader`
**Path config**: `PAYMENT_METHODS_CATALOG_PATH`

### 4. **Catálogos de Plataformas de Pago**

#### `payment_platforms_regex_catalog`
Regex para plataformas (Visa, MasterCard, Amex, etc.).

**Reader**: `PaymentPlatformsRegexCatalogReader`
**Path config**: `PAYMENT_PLATFORMS_REGEX_CATALOG_PATH`

#### `payment_platforms_catalog`
Datos de plataformas.

**Reader**: `PaymentPlatformsCatalogReader`
**Path config**: `PAYMENT_PLATFORMS_CATALOG_PATH`

## Ejemplo: `gastos_regex.csv`

```csv
REGEX,SUBCATEGORY
"(?i).*(\b)(alquiler).*(\b)(coche)(\b).*",90
"(?i).*(\b)(viaje)(\b).*",91
"(?i).*(\b)(lazeria|casino)(\b).*",54
"(?i).*(\b)(guarderia)(\b).*",32
```

Columnas:
- `REGEX`: Patrón regex (case-insensitive)
- `SUBCATEGORY`: ID de subcategoría

## ¿Dónde Están los Catálogos?

### En la Librería
- Archivos CSV de **ejemplo**: `/resources/kagr/catalogs/`
- **Readers** (código): `/kagr/default_logic/*/catalogs/`

### En los Engines (Configuración)
- **Paths** a catálogos: `application.conf` (engine)
- **Valores por entorno**: `catalogs.conf`, `proloans_catalogs.conf` (engine)
- **CSVs reales**: En data stores, data lakes, o repos de datos (engine decide)

## Cómo se Cargan

En `tagging_jobs.py`:

```python
catalogs = CatalogReader.load_catalogs(self.config, CATALOGS_CONFIG)
```

1. `CatalogReader` lee `CATALOGS_CONFIG` (lista de catálogos a cargar)
2. Para cada catálogo, obtiene el path desde `self.config` (config del engine)
3. Usa el `reader` especificado para transformar datos
4. Retorna diccionario de catálogos cargados

## Modificación de Regex

### Escenario 1: Cambiar lógica de lectura
- **Edita**: Librería (`lib_agregador_github`)
- **Redeploy**: Librería

### Escenario 2: Cambiar qué regex usar
- **Edita**: Config del engine (`catalogs.conf`)
- **Redeploy**: Engine

### Escenario 3: Cambiar contenido de CSV
- **Edita**: CSV en data store/repo
- **Redeploy**: Ninguno (las config apuntan al archivo nuevo)

## Referencias

- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION]]
- [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA]]


