# Nomenclatura del Proyecto KPFM - Etiquetas

**Referencia rápida de variables, funciones y conceptos del proyecto**

---

## 📌 Variables Principales (CONSERVAR SIEMPRE)

### Variables de Etiqueta
| Nombre | Tipo | Descripción |
|--------|------|-------------|
| `label_id` | string | Identificador único de etiqueta (ej: "ELEC", "GAS") |
| `gf_pfm_tag_code` | string | Código de etiqueta en struct (mismo ≈ label_id) |
| `gf_pfm_tag_created_date` | timestamp | Cuando se aplicó la etiqueta por primera vez |
| `gf_pfm_tag_updated_date` | timestamp | Cuándo cambió la visibilidad (null si sin cambios) |
| `gf_pfm_tag_deleted_date` | timestamp | Soft delete (null si activa) |
| `gf_pfm_tag_visible` | boolean | ¿La etiqueta es visible? |
| `gf_pfm_tag_scope` | string | Ámbito: "MOVEMENT", "ACCOUNT", etc. |
| `gf_pfm_tag_status` | string | Estado: "active", "inactive" |

### Variables de Movimiento
| Nombre | Tipo | Descripción |
|--------|------|-------------|
| `g_movement_id` | string | ID único del movimiento |
| `g_inf_universe_country_id` | string | País (ej: "MX", "ES") |
| `g_bucket_id` | string | Bucket/partición |
| `gf_mov_tags_arc` | array<struct> | Array acumulativo de etiquetas |

### Parámetros de Configuración
| Nombre | Tipo | Ubicación | Descripción |
|--------|------|-----------|-------------|
| `UNIVERSE_COUNTRY_ID` | string | ENV o app.conf | País único para esta ejecución |
| `LABELS_PARAM.LABELS_TO_APPLY` | list[string] | app.conf | Qué etiquetas evaluar (vacío = ninguna) |
| `LABELS_PARAM.LABELS_TO_SKIP` | list[string] | app.conf | Etiquetas a ignorar (opcional) |

---

## 🔧 Funciones Clave del Proyecto

### RuleEngine (Ubicación: `io/rule_engine.py`)

#### `apply_tagging_rules(df, passthrough_columns, active_rules=None)`
```python
Entrada:  df (DataFrame), active_rules (List[LabelerConfig])
Salida:   df con columna gf_mov_tags_arc (array<struct>)
Acción:   Evalúa condiciones de reglas y crea etiquetas
```

**Cada etiqueta nueva tiene:**
- `gf_pfm_tag_created_date` = momento ejecución
- `gf_pfm_tag_updated_date` = **null** (nueva etiqueta)
- Resto campos según schema

#### `merge_with_existing_tags(df_new, df_input)`
```python
Entrada:  df_new (con etiquetas recalculadas)
          df_input (con gf_mov_tags_arc previo)
Salida:   df reconciliado
Acción:   Mezcla etiquetas nuevas + previas, preserva historia
```

**Lógica interna:**
```python
Para cada movimiento:
  prior_tags = df_input.gf_mov_tags_arc        # Etiquetas anteriores
  new_tags = df_new.gf_mov_tags_arc            # Etiquetas calculadas
  
  result.gf_mov_tags_arc = merge(new_tags, prior_tags)
    - Si tag nuevo: agregar (updated_date=null)
    - Si tag existe sin cambio visibility: preservar
    - Si tag existe con cambio visibility: actualizar + updated_date=ahora
    - Si tag no en new_tags: preservar del anterior (no reevaluado)
```

#### `_execution_tag_date()`
```python
Entrada:  (ninguna)
Salida:   timestamp (actual ejecución)
Acción:   Centraliza generación de timestamp para consistencia
Uso:      Para fijar updated_date cuando visibilidad cambia
```

### ConfigReader (Ubicación: `io/config/config_reader.py`)
| Método | Entrada | Salida | Descripción |
|--------|---------|--------|-------------|
| `load_config()` | path | LabelerConfig | Carga config desde HOCON |
| `get_active_rules()` | - | List[LabelerConfig] | Retorna reglas activas |
| `validate_labels_param()` | - | bool | Valida LABELS_PARAM |

---

## 🏛️ Arquitectura Conceptual

### Flujo Entrada → Salida
```
INPUT: t_kpfm_movements (Spark table)
  ├── g_movement_id (ID)
  ├── g_inf_universe_country_id (País)
  ├── gf_mov_tags_arc (etiquetas previas - null si primera vez)
  └── [otros campos]

         ↓ [RuleEngine.apply_tagging_rules()]

INTERMEDIATE: DataFrame con etiquetas nuevas
  ├── g_movement_id
  ├── gf_mov_tags_arc (nuevas etiquetas, updated_date=null)
  └── [passthrough cols]

         ↓ [RuleEngine.merge_with_existing_tags(df_new, df_input)]

OUTPUT: DataFrame reconciliado
  ├── g_movement_id
  ├── gf_mov_tags_arc (mezcla de nuevas + previas)
  │  └── updated_date actualizado solo si visibility cambió
  └── [passthrough cols]

         ↓ [Write to Hive/Delta]

PERSISTENCE: t_kpfm_movements_*_tagged (Spark table)
```

---

## 🔑 Configuración - Archivos Críticos

### `resources/application.conf`
```hocon
UNIVERSE_COUNTRY_ID = "MX"

LABELS_PARAM {
  LABELS_TO_APPLY = ["ELEC", "GAS", "SOLAR"]
  LABELS_TO_SKIP = []
}

SPARK_CONFIG {
  app.name = "kpfm_ho_cs_fou_gdh_pys_movementstagger"
  sql.adaptive.enabled = true
}
```

### `resources/config/labels.conf` (Global)
```hocon
labels {
  ELEC {
    label_id = "ELEC"
    label_name = "Electricity"
    condition = "amount > 1000"
  }
  GAS {
    label_id = "GAS"
    condition = "product_type == 'GAS'"
  }
}
```

### `resources/config/geography_config/MX_config.conf` (Por país)
```hocon
labels {
  ELEC {
    # Sobrescribe global
    condition = "amount > 500"  # Más bajo para MX
  }
  NEW_MX_LABEL {
    # Agregado solo para MX
    label_id = "MX_SPECIAL"
    condition = "..."
  }
}
```

---

## 📊 Schema de TAG_ENTRY_SCHEMA

**Ubicación:** `io/schemas/output_schema.py`

```python
StructType([
    StructField("gf_pfm_tag_code", StringType(), nullable=True),
    StructField("gf_pfm_tag_created_date", TimestampType(), nullable=True),
    StructField("gf_pfm_tag_updated_date", TimestampType(), nullable=True),  # ← NEW
    StructField("gf_pfm_tag_deleted_date", TimestampType(), nullable=True),
    StructField("gf_pfm_tag_visible", BooleanType(), nullable=True),
    StructField("gf_pfm_tag_scope", StringType(), nullable=True),
    StructField("gf_pfm_tag_status", StringType(), nullable=True),
])
```

**Regla de llenado (merge_with_existing_tags):**

| Campo | Nueva Etiqueta | Sin Cambio | Cambio Visibilidad |
|-------|----------------|------------|--------------------|
| `code` | label_id | ← preserved | ← updated |
| `created_date` | `now()` | `prior` | `prior` |
| `updated_date` | `null` | `prior` (o `null`) | `now()` |
| `deleted_date` | `null` | `prior` | ✓ puede cambiar |
| `visible` | from rule | `prior` | `from rule` |
| `scope` | from schema | `prior` | `from rule` |
| `status` | from schema | `prior` | `from rule` |

---

## 🎯 Términos Comunes

### Etiquetado
- **Etiqueta (Tag):** Clasificación aplicada a un movimiento
- **Regla (Rule):** Condición que determina si aplicar etiqueta
- **Etiquetado inicial:** Primera vez que se procesa un movimiento
- **Re-etiquetado:** Movimiento vuelve a procesarse

### Merging
- **Prior tags:** Etiquetas del procesamiento anterior
- **New tags:** Etiquetas del procesamiento actual
- **Non-reevaluated:** Etiquetas previas que no aparecen en nuevas
- **Tag reconciliation:** Proceso de mezclar prior + new

### Visibilidad
- **visible=true:** La etiqueta se muestra/es válida
- **visible=false:** La etiqueta existe pero está oculta
- **Cambio de visibilidad:** Cuando prior.visible ≠ new.visible

---

## 🧪 Patrones de Testing

### Setup Típico
```python
# Helper: crear etiqueta de prueba
def _make_tag(spark, code, created_date, visible=True, updated_date=None):
    return spark.createDataFrame([
        Row(
            gf_pfm_tag_code=code,
            gf_pfm_tag_created_date=created_date,
            gf_pfm_tag_updated_date=updated_date,
            gf_pfm_tag_visible=visible,
            gf_pfm_tag_scope="MOVEMENT",
            gf_pfm_tag_status="active",
            gf_pfm_tag_deleted_date=None,
        )
    ], schema=TAG_ENTRY_SCHEMA).collect()[0]

# Helper: crear df con tags
def _df_with_tags(spark, mov_id, tags):
    return spark.createDataFrame([
        Row(g_movement_id=mov_id, gf_mov_tags_arc=tags)
    ], schema=...)
```

### Test Merge
```python
# Setup
df_input = _df_with_tags(spark, "m1", [prior_tag])
df_new = _df_with_tags(spark, "m1", [new_tag])

# Execute
result = RuleEngine.merge_with_existing_tags(df_new, df_input)

# Assert
assert result_tag.gf_pfm_tag_updated_date == expected_date
```

---

## 🚫 Anti-Patrones (NO hacer)

| ❌ Anti-Patrón | ✅ Alternativa |
|---------------|--------------|
| Usar `rule_id` en struct | Usar `label_id` |
| Flag `enabled` como gatillo | Usar `LABELS_TO_APPLY` |
| Lista vacía = todas etiquetas | Lista vacía = ninguna |
| Fallback país automático | Usar env `UNIVERSE_COUNTRY_ID` |
| Config clave `rules` | Config clave `labels` |
| UDF Pandas | Spark Column DSL (`f.transform()`, etc) |
| Timestamp diferente por tag | `_execution_tag_date()` centralizado |

---

## 📚 Referencias Externas

### Spark Functions (Docs PySpark)
- `pyspark.sql.functions.transform()` - Apply function to array elements
- `pyspark.sql.functions.when()/.otherwise()` - Conditional expressions
- `pyspark.sql.functions.map_from_entries()` - Create map from [(k,v),...]
- `pyspark.sql.functions.struct()` - Create struct expression
- `pyspark.sql.functions.concat()` - Concatenate arrays/strings

### Pydantic
- `BaseModel` - Define configuration schemas
- `Field()` - Declare field with validation
- `validator` - Custom validators

### PyHOCON
- `ConfigFactory.parse_file()` - Load HOCON config
- `ConfigFactory.parse_string()` - Parse inline config
- Reference: `${key}`, `${?optional_key}`

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026

