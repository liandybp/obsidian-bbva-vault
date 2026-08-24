# Contexto Actual - Sesión 29 Junio 2026

**Versión:** v2.1.0 (Tag Merging + Updated Date)
**Status:** Estable - Listo para nuevas features

---

## 🎯 Estado del Proyecto

### Arquitectura General
- **Lenguaje:** Python 3.11
- **Engine:** PySpark 3.x
- **Patrón:** ETL - Spark Job (batch processing de movimientos)
- **Configuración:** PyHOCON + Pydantic

### Entry Points
1. `worker.py` - MAIN - Spark job orchestration
2. `app.py` - Application core
3. `rule_engine.py` - Motor de evaluación de etiquetas

---

## ✅ Features Completados (Última Sesión)

### 1. Tag Merging Logic
**Archivo:** `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/rule_engine.py`
**Cambio:** Nuevo método `merge_with_existing_tags(df_new: DataFrame, df_input: DataFrame) -> DataFrame`

**Propósito:**
Cuando un movimiento que ya fue etiquetado en el pasado vuelve a entrar al flujo:
- Preserva etiquetas previas que no se reevalúan
- Solo actualiza visibilidad si cambió
- Conserva fecha original de creación `gf_pfm_tag_created_date`
- Actualiza `gf_pfm_tag_updated_date` solo si ocurre cambio de visibilidad

**Lógica:**
```
Para cada movimiento:
  1. Mapear etiquetas previas por código
  2. Obtener etiquetas no reevaluadas (preservar)
  3. Para cada etiqueta nueva:
     - Si no existía: agregar (updated_date = null)
     - Si existía sin cambio: preservar anterior
     - Si cambió visibilidad: actualizar + fijar updated_date = ahora
  4. Concatenar (nuevas + no reevaluadas)
  5. Retornar resultado
```

**Integración en job:**
```python
# movement_tagger_job.py - after apply_tagging_rules()
df_result = engine.merge_with_existing_tags(df_result, df_rule_input)
```

### 2. Schema Extension: `gf_pfm_tag_updated_date`
**Archivo:** `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/output_schema.py`
**Cambio:** Nuevo campo en `TAG_ENTRY_SCHEMA` (posición 3)

```python
TAG_ENTRY_SCHEMA = StructType([
    ("gf_pfm_tag_code", StringType()),          # 1
    ("gf_pfm_tag_created_date", TimestampType()), # 2
    ("gf_pfm_tag_updated_date", TimestampType()), # 3 ← NEW
    ("gf_pfm_tag_deleted_date", TimestampType()), # 4
    ("gf_pfm_tag_visible", BooleanType()),        # 5
    ("gf_pfm_tag_scope", StringType()),           # 6
    ("gf_pfm_tag_status", StringType()),          # 7
])
```

**Semántica:**
| Escenario | Valor |
|-----------|-------|
| Etiqueta nueva | `null` |
| Sin cambios visibility | Preservar anterior (o `null`) |
| Cambio visibility | `current_timestamp()` |

### 3. Documentación Actualizada
**Archivos:**
- `docs/03_MOTOR_REGLAS_RULEENGINE.md` - Secciones 3.11 (Merge) y 3.12 (Use cases)
- `.github/copilot-instructions.md` - Instrucciones vigentes

**Incluye:**
- Explicación 7-pasos internos
- Documentación cada función Spark usada
- Ejemplos mentales input → output
- Integración en job.py

### 4. Test Coverage Completo
**Archivo:** `tests/test_io/test_rule_engine.py`
**Tests nuevos:** 7 casos de merge

```python
✅ test_merge_preserves_existing_tags_when_not_reevaluated
✅ test_merge_preserves_created_date_on_visibility_change
✅ test_merge_updates_visibility_when_changed
✅ test_merge_appends_new_tags_to_existing
✅ test_merge_first_time_tagging_empty_prior_array
✅ test_merge_preserves_existing_updated_date_when_tag_does_not_change  ← NEW
✅ test_merge_replaces_previous_updated_date_with_current_process_date_when_tag_changes ← NEW
```

**Status:** 17 tests passed ✅

---

## 📊 Schema Actual

### Input
```
t_kpfm_movements
├── g_movement_id
├── g_inf_universe_country_id
├── g_bucket_id
├── gf_mov_tags_arc (array<struct> desde procesamiento anterior)
└── [otros campos...]
```

### Processing
```
Triggers: LABELS_PARAM.LABELS_TO_APPLY
  ↓
RuleEngine.apply_tagging_rules()
  → Genera nuevas etiquetas (updated_date = null)
  ↓
RuleEngine.merge_with_existing_tags()
  → Reconcilia con previas (updated_date por visibilidad)
  ↓
Output: gf_mov_tags_arc poblado
```

### Output Schema (gf_mov_tags_arc)
```
array<struct>
├── gf_pfm_tag_code: string
├── gf_pfm_tag_created_date: timestamp
├── gf_pfm_tag_updated_date: timestamp (null si sin cambios)
├── gf_pfm_tag_deleted_date: timestamp (null si activa)
├── gf_pfm_tag_visible: boolean
├── gf_pfm_tag_scope: string
├── gf_pfm_tag_status: string
```

---

## 🔑 Configuración Vigente

### Entry Point Config
```hocon
UNIVERSE_COUNTRY_ID = "MX"  # O variable de ambiente
LABELS_PARAM {
  LABELS_TO_APPLY = ["ELEC", "GAS", "SOLAR"]  # Si vacío: no se etiqueta
}
```

### Files Críticos
- `resources/application.conf` - Base
- `resources/config/labels.conf` - Etiquetas globales
- `resources/config/geography_config/MX_config.conf` - Por país

---

## 🧪 Tests Pass Rate

```bash
$ pytest -q
tests/test_io/test_rule_engine.py::test_merge_* ........... [7 tests]
tests/test_io/schemas/test_output_schema.py .............. [2 tests]
tests/business/test_labeler.py ........................... [3 tests]
[... otros ...]

Total: 17 passed in 10.66s ✅
```

---

## 🔧 Stack Técnico Actual

### Python Packages (Principales)
- `pyspark==3.x` - Distributed computing
- `pydantic==2.x` - Schemas & validation
- `pyhocon` - HOCON config parsing
- `pytest` - Testing

### Spark Functions Usadas Frecuentemente
```python
# Data transformation
f.transform()           # Apply lambda to each array element
f.map_from_entries()    # Convert [(k1, v1), ...] → Map
f.struct()              # Create struct expressions
f.concat()              # Concatenate arrays

# Conditionals
f.when()/.otherwise()   # Spark if/elif/else
f.coalesce()            # Return first non-null

# Array operations
f.array()               # Create array literal
f.array_contains()      # Check if element in array
f.filter()              # Filter array elements

# Timestamps
f.current_timestamp()   # Current servertime
RuleEngine._execution_tag_date()  # Centralized process timestamp
```

---

## 📋 Archivos Críticos por Responsabilidad

| Responsabilidad | Archivo |
|-----------------|---------|
| Schemas | `io/schemas/output_schema.py` |
| RuleEngine (Core Logic) | `io/rule_engine.py` |
| Job Orchestration | `runtime/job/movement_tagger_job.py` |
| Configuration | `io/config/config_reader.py` |
| Tests | `tests/test_io/test_rule_engine.py` |
| Docs | `docs/03_MOTOR_REGLAS_RULEENGINE.md` |

---

## 🚀 Next Steps Recomendados

### Corto Plazo (Si aplica)
- [ ] Agregar métricas de rendimiento en merge (cuántas etiquetas no-reevaluadas)
- [ ] Dashboard de auditoría: comparar updated_date vs created_date
- [ ] Validación: asegurar updated_date > created_date siempre

### Mediano Plazo
- [ ] Soft-delete logic: cuando `gf_pfm_tag_visible=false`, marcar `deleted_date`
- [ ] Histórico completo: pasar `gf_mov_tags_arc_old` para comparación
- [ ] Estadísticas: contar tags nuevos vs actualizados vs preservados

### Largo Plazo
- [ ] Rebalancing: repartir carga de merge si DataFrame > 10GB

---

## 📖 Referencias Rápidas

### Leer primero (Orden recomendado)
1. Este archivo (contexto actual)
2. `COPILOT_INSTRUCTIONS.md` (reglas)
3. `docs/FLUJO_FUNCIONAL.md` (overview)
4. `docs/03_MOTOR_REGLAS_RULEENGINE.md` (detalle)

### Consultar mientras codeas
- `referencias/NOMENCLATURA.md` - Variables/nombres del proyecto
- `referencias/SPARK_FUNCTIONS.md` - Funciones Spark con ejemplos
- `referencias/CONFIGURACION_KEYS.md` - Claves de config

---

## 💾 Cómo Actualizar Este Documento

**Después de cada sesión productiva:**
1. Copiar esta sección "Estado del Proyecto" a `CAMBIOS_RECIENTES.md`
2. Actualizar features completados ✅
3. Agregar "Next Steps" si hay nuevos descubrimientos
4. Notar cualquier cambio en stack tecnológico
5. Actualizar referencias de archivos críticos

**Template:**
```markdown
## Sesión [FECHA]
**Features:** [Qué se hizo]
**Bugs Fixed:** [Qué se arregló]
**Decisiones:** [Qué se decidió]
**Tests:** [Qué se validó]
```

---

**Última Actualización:** 29 Junio 2026 - 15:30 UTC
**Sesión:** Tag Merging Implementation Complete
**Status:** ✅ Listo para producción

