# Decisión: Implementar Lectura en Cascada de Catálogos (Entidades → Parquet)

**Fecha:** 20 Agosto 2026  
**Estado:** ✅ **ANALIZADO Y FACTIBLE**  
**Prioridad:** Media  
**Ámbito:** Categorizador Global (`categ_gl_git`)

---

## 1. Resumen Ejecutivo

Se ha analizado la **factibilidad de implementar una variante de lectura en cascada** en el categorizador global para intentar leer de catálogos de entidades (tablas de Hive/Glue) ANTES de intentar leer desde parquet. Si la lectura de entidades falla sin que falle el proceso completo, se intenta leer desde parquet como fallback.

### Conclusiones
✅ **FACTIBLE Y RECOMENDADO**

El proyecto de **etiquetas (`kpfm-etiquetas`)** ya implementa este patrón con éxito (líneas 144-177 de `catalog_reader.py`). El **categorizador global** puede aplicar el mismo patrón a sus catálogos.

---

## 2. Análisis Actual

### 2.1 Estado en `kpfm-etiquetas` (Implementado ✅)

```python
# Orden de intentos (línea 144-177 en catalog_reader.py):
1. Intenta leer desde parquet:     _load_from_parquet_path()
2. Si falla SIN lanzar excepción:  _load_from_entity_catalog()
3. Si falla de nuevo:               _load_from_local_catalog()
```

**Método clave:** `LabelsCatalogReader.load_labels()`
- Captura excepciones `CatalogLoadError` para cada intento
- Registra warnings (no errores) para intentos fallidos
- Solo lanza excepción si TODOS los intentos fallan

### 2.2 Estado en `categ_gl_git` (NO Implementado ❌)

**Lectura actual:**
- `catalog_reader.py` línea 34-36: `_load_parquet_table()`
- Lee directamente desde **parquet file-path**, SIN fallback
- Si la ruta no existe → falla el proceso

**Método clave:** `CatalogReader._load_catalog()`
```python
def _load_catalog(cls, config, catalog_conf, cutoff_date):
    try:
        df = cls._load_parquet_table(config, catalog_conf).specific_partition(...)
        catalog = catalog_conf.reader.as_catalog(df, ...)
    except Exception as e:
        cls.__logger.error(f"- Failed to load {catalog_conf.name}. {e}")
        catalog = None  # Devuelve None (manejado por REQUIRE_ALL_CATALOGS)
```

**Problema:** No intenta leer de catálogos de entidades antes de fallar.

---

## 3. Ventajas de Implementar Lectura en Cascada

| Aspecto | Beneficio |
|--------|----------|
| **Robustez** | Fallback automático si parquet no está disponible |
| **Operabilidad** | Permite leer de maestras de entidades en desarrollo/test |
| **Compatibilidad** | Mantiene comportamiento actual (parquet primero) |
| **Reutilización** | Aplica patrón ya probado en `kpfm-etiquetas` |
| **No-breaking** | NO afecta parámetros de configuración existentes |
| **Logging** | Warnings informativos, sin fallar silenciosamente |

---

## 4. Arquitectura Propuesta

### 4.1 Patrón de Lectura (Cascada)

```
┌─ Intenta Lectura PARQUET
│  └─ Si FALLA → log warning
│
└─ Intenta Lectura de ENTIDAD (Catálogo Hive)
   └─ Si FALLA → log warning
   └─ Si ÉXITO → usa DataFrame
   
Si ambas fallan:
  └─ Maneja según REQUIRE_ALL_CATALOGS (comportamiento actual)
```

### 4.2 Configuración Requerida (0 cambios)

**NO se agregan parámetros** porque:
- El nombre de tabla/BD puede derivarse del `path` actual
- Pattern: `path = "database.table"` en lugar de `/path/to/parquet`
- La lectura intenta ambas automáticamente

### 4.3 Métodos a Agregar a `CatalogReader`

```python
@classmethod
def _load_from_entity_catalog(cls, config: ConfigTree, catalog_conf: CatalogConf, cutoff_date: date) -> DataFrame:
    """Intenta leer tabla de Hive/Glue desde path interpretado como 'db.table'"""
    path = config.get_string(catalog_conf.env_var)
    # Interpreta path como "db.table" si es válido
    # De otra forma → None o excepción
    
@classmethod
def _load_catalog_with_fallback(cls, config, catalog_conf, cutoff_date):
    """Intenta parquet PRIMERO, si falla intenta entidad"""
    try:
        # Intento 1: Parquet (comportamiento actual)
        return _load_catalog(config, catalog_conf, cutoff_date)
    except Exception as e:
        cls.__logger.warning(f"Parquet load failed for {catalog_conf.name}: {e}")
        try:
            # Intento 2: Entidad
            df = _load_from_entity_catalog(config, catalog_conf, cutoff_date)
            return catalog_conf.reader.as_catalog(df, ...)
        except Exception as e2:
            cls.__logger.warning(f"Entity load failed for {catalog_conf.name}: {e2}")
            return None  # Manejo por REQUIRE_ALL_CATALOGS
```

---

## 5. Métodos Disponibles en `adautils`

**Dependencia:** `gl_cs_adv_aif_pyl_adautils>=0.4.3` (ya importada)

**Módulos disponibles:**
- `gl_cs_adv_aif_pyl_adautils.catalog`: funciones de particiones (fetch_partitions, etc.)
- `gl_cs_adv_aif_pyl_adautils.io.input`: AdviceDataFrameReader (ya usado)
- `gl_cs_adv_aif_pyl_adautils.io.output`: AdviceDataFrameWriter (ya usado)

**Recomendación:**
- No crear dependencia pesada en `adautils`
- Usar directamente `spark.table("db.table")` (ya disponible en PySpark)
- Si se necesitan particiones, usar `adautils.catalog.fetch_table_partitions()`

---

## 6. Cambios Mínimos Requeridos

### 6.1 En `glglcsadvpyglobaltagger/gl/catalogs/catalog_reader.py`

1. **Agregar método** `_load_from_entity_catalog()` (∼20 líneas)
2. **Refactor** `_load_catalog()` para usar try-except-try-except
3. **Logging** para informar fallback automático
4. **Docstring** explicando nueva conducta

### 6.2 Tests Nuevos

```bash
tests/gl/catalogs/test_catalog_reader_entity_fallback.py
├── test_entity_fallback_when_parquet_missing()
├── test_parquet_preferred_over_entity()
├── test_both_missing_returns_none()
└── test_entity_with_cutoff_date_partition()
```

### 6.3 Documentación

- Actualizar `docs/` con new behavior
- Docstring en `CatalogReader`
- README si has operability notes

---

## 7. Impacto en Otros Proyectos

| Proyecto | Impacto | Decisión |
|----------|--------|---------|
| `kpfm-etiquetas` | **INSPIRACIÓN** (ya lo hace) | Aporta patrón probado |
| `categ_gl_git` consumidores | Cambio de comportamientos interno | Sin cambios de interface |
| Otros globales | Reutilizable | Si necesitan, copy-paste pattern |

---

## 8. Riesgos y Mitigaciones

| Riesgo | Severidad | Mitigación |
|--------|-----------|-----------|
| Performance: intentar 2x lecturas | BAJA | Warnings en log, intento rápido si path no válido |
| Confusión ops: qué se lee | MEDIA | Logging claro sobre qué fue intentado y origen |
| Cambio en orden de precedencia | BAJA | Documentar: parquet siempre primero |
| Interpretación path como db.table | MEDIA | Validación: si contiene `/`, es path; si no, es `db.table` |

---

## 9. Criterios de Aceptación

- [ ] `CatalogReader._load_from_entity_catalog()` implementado
- [ ] `_load_catalog()` intenta fallback automático
- [ ] Tests unitarios pasan (entity load, parquet fallback, both missing)
- [ ] Logging informativos y sin ruido
- [ ] Documentación (docstring + README) clara
- [ ] Backward compatible: parquet primero
- [ ] No cambia configuración requerida
- [ ] Acceptance tests comportamiento sin cambios

---

## 10. Pasos Siguientes (Backlog)

### Fase 1: Prototipo
1. Checkout branch: `feature/catalog-entity-fallback`
2. Implementar `_load_from_entity_catalog()`
3. Tests unitarios locales

### Fase 2: Integración
4. Refactor `_load_catalog()` con fallback
5. Tests integration con catálogos reales
6. Review con team

### Fase 3: Operación
7. Deploy a staging
8. Validar logs y behavior
9. Deploy a producción

### Fase 4: Documentación
10. Actualizar `docs/`
11. Agregar runbook operacional
12. Comunicar a equipo

---

## 11. Referencias

- **Patrón Base:** `kpfm-etiquetas/kpfm_ho_cs_fou_gdh_pys_movementstagger/io/catalog/catalog_reader.py` (líneas 144-177)
- **Dependencia:** `gl_cs_adv_aif_pyl_adautils>=0.4.3`
- **Repositorio:** `/Users/t022458/PycharmProjects/gl/categ_gl_git`
- **Instrucciones Globales:** [[_global/copilot/INSTRUCCIONES_BASE]]
- **Archivo Copilot:** `.github/copilot-instructions.md`

---

**Decisión:** ✅ **APROBAR IMPLEMENTACIÓN**  
**Responsable:** Team categorizador global  
**Fecha Revisión:** 20 Agosto 2026  
**Estado:** Pendiente implementación (Backlog Feature)

