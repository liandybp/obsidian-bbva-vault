# Engines - Aplicaciones Consumidoras

## ¿Qué son los Engines?

Son las **aplicaciones que consumen** `lib_agregador_github` y llevan **la configuración específica** por entorno.

Ejemplos:
- **Agregador** — Agregación diaria de movimientos
- **User Space** — API para datos de usuario
- **Proloan** — Procesamiento de préstamos

## Responsabilidades de un Engine

### 1. Aportar Configuración
```
engine/
├── resources/
│   ├── application.conf          # Define variables base
│   ├── catalogs.conf             # Overwrite path de catálogos
│   └── catalogs_proloan.conf     # Config específica Proloan
```

### 2. Definir Paths de Catálogos
```hocon
# catalogs.conf (engine)
BRANDS_REGEX_CATALOG_PATH = "s3://data-lake/catalogs/brands_regex.csv"
POSITIVE_REGEX_CATALOG_PATH = "s3://data-lake/catalogs/gastos_regex.csv"
NEGATIVE_REGEX_CATALOG_PATH = "s3://data-lake/catalogs/negative_regex.csv"
```

### 3. Usar la Librería
```python
# job en el engine
from lib_agregador_github.kagr.runtime.batch.jobs import DailyTaggingJob

job = DailyTaggingJob(spark, config)
job.run()  # Usa la lógica central de lib_agregador_github
```

## Configuración Por Entorno

### Development
```
catalogs.conf:
  BRANDS_REGEX_CATALOG_PATH = "file:///local/catalogs/brands_regex.csv"
```

### Production
```
catalogs.conf:
  BRANDS_REGEX_CATALOG_PATH = "s3://prod-data/catalogs/brands_regex.csv"
```

## Flujo Típico

```
Engine
  ├─ Lee config (application.conf + catalogs.conf)
  ├─ Instancia DailyTaggingJob(spark, config)
  ├─ DailyTaggingJob.logic() → Carga catálogos usando config
  ├─ DefaultTaggingLogic(**catálogos) → Usa lógica de lib_agregador_github
  └─ Procesa movimientos con la lógica compartida
```

## Ventajas

✅ **Una sola lógica de tagging** — Mantenimiento centralizado
✅ **Configuración flexible** — Cada engine define sus paths
✅ **Agnóstico** — La librería no sabe de deployments específicos
✅ **Reutilizable** — Múltiples engines, misma lógica

## Referencias

- [[kagr/proyectos/lib-agregador-github/README]]
- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION]]


