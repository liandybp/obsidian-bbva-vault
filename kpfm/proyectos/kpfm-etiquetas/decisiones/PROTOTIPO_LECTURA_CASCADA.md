# Prototipo: Implementación de Lectura en Cascada

**Archivo:** `glglcsadvpyglobaltagger/gl/catalogs/catalog_reader.py`  
**Cambios:** Agregar fallback de entidad cuando parquet falla

---

## Cambios Propuestos

### 1. Agregar Nuevo Método `_load_from_entity_catalog()`

Ubicación: Después de `_load_parquet_table()` (línea ~36)

```python
@classmethod
def _load_from_entity_catalog(cls, config: ConfigTree, catalog_conf: CatalogConf, 
                               cutoff_date: date) -> DataFrame:
    """
    Attempt to load catalog from Hive/Glue entity table.
    
    Interprets the path as "database.table" format.
    Only applies filter by cutoff_date if the catalog is marked as "common".
    
    Args:
        config: Configuration tree
        catalog_conf: Catalog configuration with env_var pointing to "db.table"
        cutoff_date: Partition date to filter by (if is_common=True)
    
    Returns:
        PySpark DataFrame from entity table
    
    Raises:
        Exception: If table not found or unable to read
    
    Example:
        >>> config_val = "es_master.t_kmrc_labels"  # From ENV var
        >>> df = _load_from_entity_catalog(config, catalog_conf, cutoff_date)
    """
    path = config.get_string(catalog_conf.env_var)
    
    # Validate path looks like a table reference (not a filesystem path)
    if not path or "/" in path:
        raise ValueError(
            f"Entity catalog path must be format 'database.table', got: {path}"
        )
    
    cls.__logger.debug(f"Attempting entity catalog load from: {path}")
    
    try:
        # Read table directly from Hive/Glue
        df = cls._datio_pyspark_session.table(path)
        
        # Apply cutoff_date filter if this is a common catalog
        if catalog_conf.is_common and cutoff_date:
            df = df.filter(f.col(GF_CUTOFF_DATE.name) == cutoff_date)
            cls.__logger.debug(f"Applied cutoff_date filter: {cutoff_date}")
        
        return df
    
    except Exception as e:
        cls.__logger.debug(f"Failed to load entity table '{path}': {e}")
        raise
```

### 2. Refactor `_load_catalog()` para Fallback

Reemplazar (líneas 104-116) con:

```python
@classmethod
def _load_catalog(cls, config: ConfigTree, catalog_conf: CatalogConf, cutoff_date: date):
    """
    Load catalog with automatic fallback: try parquet, then entity table.
    
    Attempts to load catalog from parquet first (current behavior).
    If that fails, automatically attempts to load from entity table (Hive/Glue).
    
    Args:
        config: Configuration tree
        catalog_conf: Catalog configuration
        cutoff_date: Partition date for common catalogs
    
    Returns:
        Catalog object (result of reader.as_catalog) or None if all attempts fail
    
    Behavior:
        - If load succeeds from either source, returns catalog
        - If load fails from both sources, logs errors and returns None
        - Caller is responsible for handling None based on REQUIRE_ALL_CATALOGS flag
    """
    catalog_name = catalog_conf.name
    env_var = catalog_conf.env_var
    config_value = config.get_string(env_var) if env_var in config else ""
    
    # ========== ATTEMPT 1: Parquet (Current Behavior) ==========
    cls.__logger.info(f"- Loading {catalog_name} from parquet: {config_value}")
    try:
        if catalog_conf.is_common:
            df = cls._load_parquet_table(config, catalog_conf).specific_partition(
                GF_CUTOFF_DATE.name, cutoff_date
            )
        else:
            df = cls._load_parquet_table(config, catalog_conf).last_partition(
                GF_CUTOFF_DATE.name
            )
        
        catalog = catalog_conf.reader.as_catalog(df, **catalog_conf.reader_params)
        cls.__logger.info(f"✓ Successfully loaded {catalog_name} from parquet")
        return catalog
    
    except Exception as e:
        cls.__logger.warning(
            f"Parquet load failed for {catalog_name}: {type(e).__name__}: {e}"
        )
        parquet_error = e
    
    # ========== ATTEMPT 2: Entity Catalog (New Fallback) ==========
    cls.__logger.info(f"- Attempting entity catalog fallback for {catalog_name}")
    try:
        df = cls._load_from_entity_catalog(config, catalog_conf, cutoff_date)
        catalog = catalog_conf.reader.as_catalog(df, **catalog_conf.reader_params)
        cls.__logger.info(
            f"✓ Successfully loaded {catalog_name} from entity catalog (fallback)"
        )
        return catalog
    
    except Exception as e:
        cls.__logger.warning(
            f"Entity catalog load failed for {catalog_name}: {type(e).__name__}: {e}"
        )
        entity_error = e
    
    # ========== BOTH FAILED ==========
    error_msg = (
        f"- Failed to load {catalog_name}. "
        f"Parquet: {type(parquet_error).__name__}; "
        f"Entity: {type(entity_error).__name__}"
    )
    cls.__logger.error(error_msg)
    return None
```

### 3. Imports Necesarios

Agregar al inicio del archivo (si no están):

```python
from datetime import date
import pyspark.sql.functions as f
```

---

## Testing Strategy

### Unit Tests
Archivo: `tests/gl/catalogs/test_catalog_reader_entity_fallback.py`

```python
def test_parquet_load_success_uses_parquet():
    """When parquet exists, should use it (not try entity)"""
    # Mock successful parquet read
    # Assert entity fallback NOT called
    pass

def test_entity_load_success_when_parquet_fails():
    """When parquet missing but entity exists, should use entity"""
    # Mock failed parquet, successful entity
    # Assert catalog returned
    pass

def test_both_missing_returns_none():
    """When both missing, returns None (caller handles REQUIRE_ALL_CATALOGS)"""
    # Mock both fail
    # Assert None returned
    # Assert warnings logged
    pass

def test_cutoff_date_applied_to_entity():
    """Verify cutoff_date filter applied when is_common=True"""
    # Mock entity load
    # Assert filter called with correct date
    pass

def test_invalid_entity_path_raises_error():
    """When entity path looks like filesystem path, raise ValueError"""
    # Mock config with "/" in path
    # Assert ValueError raised
    pass
```

---

## Integration Testing

- Test con catálogos reales en test environment
- Verificar logging: warnings informativos, sin fallos silenciosos
- Verificar precedencia: parquet primero, fallback a entidad

---

## Configuration Examples

### Example 1: Parquet Ruta (Comportamiento Actual)
```hocon
catalogs {
    payment_methods = "/data/catalogs/payment_methods"
    # Comportamiento: intenta parquet, si falla → intenta "data.catalogs.payment_methods" (fallback)
}
```

### Example 2: Entity Table (Nuevo)
```hocon
catalogs {
    payment_methods = "db_master.t_payment_methods_catalog"
    # Comportamiento: intenta como parquet (/db_master/...), si falla → intenta tabla Hive
}
```

---

## Migration Path for Ops

### Pre-Migration
- Documentar rutas parquet actuales
- Identificar tablas Hive equivalentes

### During Migration
- Cambiar config gradualmente por geography
- Validar logs: verificar qué source se usó
- Metricas: latencia de lectura (entity vs parquet)

### Post-Migration
- Si todo funciona desde entity: considerar remover rutas parquet
- Mantener parquet como fallback en producción

---

## Logging Examples

### Success (Parquet)
```
[INFO] - Loading payment_methods from parquet: /data/catalogs/payment_methods
[INFO] ✓ Successfully loaded payment_methods from parquet
```

### Success (Entity Fallback)
```
[INFO] - Loading payment_methods from parquet: /data/catalogs/payment_methods
[WARN] Parquet load failed for payment_methods: FileNotFoundError: ...
[INFO] - Attempting entity catalog fallback for payment_methods
[INFO] ✓ Successfully loaded payment_methods from entity catalog (fallback)
```

### Both Failed
```
[INFO] - Loading payment_methods from parquet: /data/catalogs/payment_methods
[WARN] Parquet load failed for payment_methods: FileNotFoundError: ...
[INFO] - Attempting entity catalog fallback for payment_methods
[WARN] Entity catalog load failed for payment_methods: TableNotFound: ...
[ERROR] - Failed to load payment_methods. Parquet: FileNotFoundError; Entity: TableNotFound
```

---

## Backward Compatibility

✅ **100% Compatible**
- Parquet path comportamiento identical
- Si parquet funciona, se usa (no cambia)
- Solo agrega fallback transparente
- No requiere cambios de configuración
- Si entity no existe, comportamiento same as before

---

## Performance Considerations

**Overhead:** Mínimo en caso normal (parquet existe)
- Parquet read fallida → excepción capturada
- Entity read attempt → rápido si path inválido (validación en inicio)

**Optimization:** Si known entity path
- Saltar validación de parquet si path contiene "."
- O: agregar flag `entity_only` en CatalogConf

---

## Documentación Requerida

1. **Docstring ejemplos** en `catalog_reader.py`
2. **README update:** "Catalog Loading Fallback" sección
3. **Ops runbook:** Cómo verificar qué source se usó
4. **Breaking changes:** None (pero mencionar para transparency)

---

**Propuesta creada:** 20 Agosto 2026  
**Autor:** GitHub Copilot  
**Status:** Listo para implementación  
**Review:** Equipo categorizador global

