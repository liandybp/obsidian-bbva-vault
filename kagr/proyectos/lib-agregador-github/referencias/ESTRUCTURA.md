# Estructura - lib-agregador-github

## Árbol de Directorios

```
lib_agregador_github/
├── eskagrtaggerf6d3lcdgsmfgisp/
│   ├── __init__.py
│   ├── app.py
│   ├── kagr/
│   │   ├── default_logic/          # Lógica de tagging
│   │   │   ├── tagging_logic.py
│   │   │   ├── brand/
│   │   │   ├── category/
│   │   │   ├── payment_method/
│   │   │   ├── payment_platform/
│   │   │   └── subscription/
│   │   ├── runtime/
│   │   │   └── batch/
│   │   │       └── jobs/
│   │   │           └── tagging_jobs.py  # Jobs principales
│   │   ├── tables/                 # Definiciones de tablas
│   │   ├── taxonomy/               # Taxonomía de categorías
│   │   └── schemas.py
│   ├── resources/
│   │   ├── application.conf        # Config base (EN ENGINES)
│   │   ├── catalogs.conf           # Config por entorno (EN ENGINES)
│   │   ├── proloans_catalogs.conf  # Config Proloan (EN ENGINES)
│   │   ├── kagr/
│   │   │   └── catalogs/
│   │   │       ├── gastos_regex.csv
│   │   │       └── ingresos_regex.csv
│   │   └── config/
│   └── utils/                      # Utilidades comunes
├── scripts/
├── tests/
├── pyproject.toml
├── requirements.txt
└── README.md
```

## Puntos Clave

### `/kagr/default_logic/`
Contiene toda la lógica de transformación y clasificación:
- `tagging_logic.py` — Orquestador central
- `category/` — Categorización con regex
- `brand/`, `payment_method/`, `payment_platform/` — Clasificadores específicos

### `/kagr/runtime/batch/jobs/tagging_jobs.py`
**Archivo crítico** — Define:
- `CATALOGS_CONFIG`: Lista de catálogos a cargar
- `DailyTaggingJob`: Job diario completo
- `DailyTaggingJobNoCust`: Variante sin clientes

```python
CATALOGS_CONFIG = [
    CatalogConf('brands_regex_catalog', 'BRANDS_REGEX_CATALOG_PATH', RuleBrandsCatalogReader, ...),
    CatalogConf('positive_regex_catalog', 'POSITIVE_REGEX_CATALOG_PATH', CategoryRegexCatalogReader),
    CatalogConf('negative_regex_catalog', 'NEGATIVE_REGEX_CATALOG_PATH', CategoryRegexCatalogReader),
    # ... más catálogos
]
```

### `/resources/kagr/catalogs/`
Archivos CSV de ejemplo:
- `gastos_regex.csv` — Regex para gastos
- `ingresos_regex.csv` — Regex para ingresos

⚠️ **Nota**: Los archivos `.conf` (application.conf, catalogs.conf, proloans_catalogs.conf) **están en los engines**, NO en la librería.

## Referencias

- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION]]
- [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS]]


