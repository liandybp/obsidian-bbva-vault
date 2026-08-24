a# Instrucciones Copilot - lib-agregador-github

**Contexto**: Librería central de tagging y catálogos para agregación de movimientos bancarios  
**UUAA**: KAGR  
**Rol**: Backend Python/PySpark - Librería reutilizable  

---

## 🎯 Propósito Principal

`lib-agregador-github` es la **librería agnóstica** que centraliza toda la lógica de tagging y catálogos. Los engines (Agregador, User Space, Proloan) la consumen.

**Nota clave**: Todas las regex de Agregador, User Space y Proloan se gestionan desde esta librería.

---

## 📋 Responsabilidades de Copilot

### 1. Lógica de Tagging
- ✅ Mejorar/mantener lógica en `kagr/default_logic/`
- ✅ Refactorizar `tagging_logic.py` si es necesario
- ✅ Mantener coherencia entre brand, category, payment_method, payment_platform
- ❌ NO cambiar estructura de catálogos sin coordinar

### 2. Catálogos
- ✅ Revisar/mejorar readers en `kagr/default_logic/*/catalogs/`
- ✅ Actualizar ejemplos de CSV en `resources/kagr/catalogs/`
- ❌ NO cambiar paths de catálogos (eso es responsabilidad de engines)

### 3. Jobs de Tagging
- ✅ Mantener `tagging_jobs.py` funcional
- ✅ Actualizar `CATALOGS_CONFIG` si se agregan catálogos
- ✅ Asegurar que `DailyTaggingJob` y `DailyTaggingJobNoCust` funcionen

### 4. Tablas y Schemas
- ✅ Mejorar definiciones en `kagr/tables/`
- ✅ Mantener schemas consistentes
- ✅ Documentar cambios en schemas

### 5. Utilidades
- ✅ Mejorar código en `utils/`
- ✅ Refactorizar helpers comunes
- ✅ Mantener bajo acoplamiento

---

## 🚫 Restricciones Importantes

### NO Hacer:
- ❌ Cambiar estructura de directorios sin documentar
- ❌ Agregar configuración específica de entorno (eso va en engines)
- ❌ Hardcodear paths (deben venir de config)
- ❌ Modificar archivos `.conf` (están en engines)
- ❌ Romper compatibilidad sin deprecation warning
- ❌ Agregar dependencias sin justificar

### HACER:
- ✅ Mantener librería agnóstica y stateless
- ✅ Documentar cambios en breaking changes
- ✅ Mantener tests actualizados
- ✅ Usar type hints
- ✅ Mantener imports limpios

---

## 📂 Estructura Crítica

```
eskagrtaggerf6d3lcdgsmfgisp/
├── kagr/default_logic/
│   ├── tagging_logic.py         ← LÓGICA PRINCIPAL
│   ├── brand/, category/, payment_method/, payment_platform/, subscription/
│   └── */catalogs/              ← Readers
├── kagr/runtime/batch/jobs/
│   └── tagging_jobs.py          ← Jobs (DailyTaggingJob, etc.)
├── kagr/tables/
│   └── [Definiciones de tablas]
├── resources/
│   └── kagr/catalogs/           ← CSVs de ejemplo
└── utils/                       ← Helpers
```

---

## 🔑 Patrones a Seguir

### 1. Agregación de Catálogos
```python
# En tagging_jobs.py
CATALOGS_CONFIG = [
    CatalogConf('name', 'CONFIG_KEY', ReaderClass, optional_schema_key, extra_config_dict),
    ...
]
```

### 2. Uso de Catálogos
```python
# Los engines pasan config con paths
catalogs = CatalogReader.load_catalogs(self.config, CATALOGS_CONFIG)
logic = DefaultTaggingLogic(config=self.config, **catalogs)
```

### 3. Type Hints
```python
def process_movement(movement: DataFrame, category_catalog: CategoryRegexCatalog) -> DataFrame:
    ...
```

---

## 📊 Testing

- **Ubicación**: `/tests/`
- **Framework**: pytest
- **Cobertura**: Mantener >80% en lógica crítica
- **Fixtures**: En `conftest.py` y `fixtures.py`

**Tests a ejecutar antes de PR**:
```bash
pytest tests/ -v
pytest tests/ --cov=eskagrtaggerf6d3lcdgsmfgisp
```

---

## 🔄 Workflow de PR

1. **Branch**: `feature/nombre-descriptivo` o `bugfix/descripción`
2. **Tests**: Todo debe pasar
3. **Documentación**: Actualizar si hay cambios en APIs
4. **Breaking changes**: Documentar con deprecation warnings
5. **Review**: Al menos 1 aprobación antes de merge

---

## 🎓 Referencia Rápida

| Acción | Dónde | Config |
|--------|-------|--------|
| Cambiar lógica tagging | `kagr/default_logic/` | Librería |
| Agregar catálogo | `tagging_jobs.py` + reader | Librería |
| Cambiar path catálogo | `catalogs.conf` (engine) | Engine |
| Cambiar CSV contenido | Data store externo | Engine |
| Cambiar job | `tagging_jobs.py` | Librería |
| Cambiar schema | `kagr/tables/` | Librería |

---

## 🔗 Enlaces Útiles

- [[kagr/proyectos/lib-agregador-github/README|README del Proyecto]]
- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION|Descripción Técnica]]
- [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA|Estructura]]
- [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS|Catálogos]]
- [[kagr/proyectos/lib-agregador-github/referencias/ENGINES|Engines Consumidores]]
- [[INDEX_BBVA|Índice BBVA]]

---

## ⚠️ Casos de Uso Comunes

### Caso 1: Agregar nueva regex
```
1. Agregar patrón en CSV de ejemplo (resources/kagr/catalogs/)
2. Actualizar tests
3. Documentar en CATÁLOGOS.md
4. ¡No cambiar paths!
```

### Caso 2: Mejorar lógica de categorización
```
1. Editar category_logic.py
2. Ejecutar tests
3. Si es breaking: deprecate + warning
4. Documentar en PR
```

### Caso 3: Agregar nuevo catálogo
```
1. Crear reader en default_logic/
2. Agregar CatalogConf en CATALOGS_CONFIG
3. Documentar en CATÁLOGOS.md
4. Notificar a equipos de engines
```

---

**Última actualización**: 30 Junio 2026  
**Versión**: 1.0

