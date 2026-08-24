# Instrucciones Copilot - kpfm-etiquetas

## 📌 Base Obligatoria (Global)
- [[_global/copilot/INSTRUCCIONES_BASE]] ← Lee PRIMERO (incluye acceso a vault)
- [[_global/copilot/ANTI_PATRONES]]
- [[_global/standards/WIKILINKS_CONVENCION]]

## 🔗 Acceso Vault Obsidian

Copilot actualiza documentación en `/Users/t022458/Documents/BBVA_vault/` durante cada sesión:

- **Lectura/Escritura:** Todos los archivos `.md` del vault
- **Patrón:** Actualizar CAMBIOS_RECIENTES, ESTADO y daily notes
- **Estructura proyecto:**
  ```
  ~/Documents/BBVA_vault/kpfm/proyectos/kpfm-etiquetas/
  ├── README.md
  ├── ESTADO.md
  ├── QUICK_REFERENCE.md
  ├── contexto/
  │   ├── COPILOT_INSTRUCTIONS.md  ← Este archivo
  │   ├── CAMBIOS_RECIENTES.md     ← Se actualiza cada sesión
  │   ├── ARQUITECTURA.md
  │   └── AUDITORIA_VAULT_*.md
  ├── referencias/
  ├── decisiones/
  └── docs/
  ```

## 🔄 Flujo Obligatorio por Sesión

### Antes de implementar
1. Revisar [[_global/copilot/INSTRUCCIONES_BASE]]
2. Revisar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
3. Revisar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]

### Al finalizar
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
- Actualizar o crear la nota diaria del dia: `[[daily/YYYY-MM-DD]]`
- Si se trabajaron varios proyectos en la misma fecha, registrar todos en la misma daily note (una nota por dia)
- Registrar tests ejecutados/pendientes y decisión arquitectónica si aplica

### Minimo obligatorio en `[[daily/YYYY-MM-DD]]`
- proyectos trabajados en el dia
- resumen breve por proyecto
- tests ejecutados/pendientes
- bloqueos y proximos pasos

---

## 📋 Contexto del Proyecto

### Stack Principal
- **Python:** 3.11
- **Framework:** PySpark (Spark 3.x)
- **Validación:** Pydantic
- **Config:** PyHOCON

### Entry Points
- `worker.py` - Punto de entrada principal para Spark job
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/app.py` - Aplicación principal

### Flujo Funcional
```
Entrada: t_kpfm_movements
    ↓
[Config: UNIVERSE_COUNTRY_ID] → Selecciona geografía
    ↓
[RuleEngine.apply_tagging_rules()] → Evalúa condiciones
    ↓
[RuleEngine.merge_with_existing_tags()] → Reconcilia con etiquetas previas
    ↓
Salida: DataFrame con gf_mov_tags_arc poblado
```

---

## 🎯 Reglas Funcionales CRÍTICAS

### 1. Selección de País
- **Única fuente:** `UNIVERSE_COUNTRY_ID`
- **NO permitir:** Fallbacks a otros países
- **Ubicación:** Environment variable o parámetro de config

### 2. Selección de Etiquetas por Geografía
- **Control exclusivo:** `LABELS_PARAM.LABELS_TO_APPLY`
- **Si está vacío:** NO se evalúa ninguna etiqueta
- **Regla:** "Lista vacía = sin etiquetado"
- **NO sugerir:** Lógica de "lista vacía determina todas las etiquetas"

### 3. Estructura de Tagging
- **Etiquetas globales:** Definidas en `labels.conf`
- **Sobrescritura/Agregación:** Cada geografía puede extender via `{country}_config.conf`
- **Precedencia:** País > global

### 4. Schema de Salida - TAG_ENTRY_SCHEMA
```python
TAG_ENTRY_SCHEMA = StructType([
    ("gf_pfm_tag_code", StringType()),                      # [1] Código único etiqueta
    ("gf_pfm_tag_created_date", TimestampType()),           # [2] Fecha original de etiquetado
    ("gf_pfm_tag_updated_date", TimestampType()),           # [3] Fecha de actualización (null si no hay cambios)
    ("gf_pfm_tag_deleted_date", TimestampType()),           # [4] Fecha de soft-delete (null si activa)
    ("gf_pfm_tag_visible", BooleanType()),                  # [5] Visibilidad
    ("gf_pfm_tag_scope", StringType()),                     # [6] Ámbito de aplicación
    ("gf_pfm_tag_status", StringType()),                    # [7] Estado (active, inactive)
])
```

**IMPORTANTE - `gf_pfm_tag_updated_date`:**
- **Nuevas etiquetas:** `null`
- **Sin cambios de visibilidad:** Preservar valor anterior (o `null`)
- **Con cambio de visibilidad:** Actualizar a fecha/hora actual del proceso
- **Propósito:** Auditoría de cuándo cambió la visibilidad de un tag

---

## 🔧 Reglas de Configuración

### HOCON Syntax
- **Placeholder requerido:** `${key}` (fiel a estándar)
- **Placeholder opcional:** `${?key}` (devuelve null si no existe)
- **NO introducir:** Aliases redundantes si ya existen en `application.conf`

### Lectura de Archivos desde ZIP
**Regla obligatoria:** Siempre usar `_get_resource_path()` para leer archivos de configuración.

```python
from kpfm_ho_cs_fou_gdh_pys_movementstagger.io.config.config import _get_resource_path

# Lectura correcta que funciona en filesystem Y dentro de ZIP:
resource_path = _get_resource_path("catalog/labels_catalog/t_kmrc_labels.csv")
with open(resource_path, mode="r", encoding="utf-8") as f:
    # procesar archivo
```

**Por qué:** `_get_resource_path()` implementa:
- Intenta usar ruta real del filesystem si existe (desarrollo local)
- Si está dentro de ZIP, extrae automáticamente a temp dir compartido
- Retorna siempre un `Path` válido para operaciones de filesystem

**NO hacer:** No abrir archivos directamente con rutas literales o `importlib.resources` sin envolver en `_get_resource_path()`

### Archivos de Config
```
resources/
├── application.conf                    # Config global
├── config/
│   ├── labels.conf                     # Etiquetas globales
│   ├── geography_config/
│   │   ├── MX_config.conf             # México
│   │   ├── ES_config.conf             # España
│   │   └── ...
```

---

## 🏗️ Arquitectura - RuleEngine

### Métodos Principales

#### `apply_tagging_rules(df, passthrough_columns, active_rules=None)`
**Entrada:** DataFrame con movimientos
**Salida:** DataFrame con columna `gf_mov_tags_arc` (array de TAG_ENTRY_SCHEMA)

**Flujo:**
1. Evalúa condiciones de reglas activas
2. Crea struct TAG_ENTRY con:
   - `gf_pfm_tag_code` = label_id
   - `gf_pfm_tag_created_date` = timestamp ejecución
   - `gf_pfm_tag_updated_date` = null (nuevas etiquetas)
   - Resto de campos según esquema
3. Monta array si múltiples etiquetas aplican

#### `merge_with_existing_tags(df_new, df_input)`
**Entrada:** 
- `df_new` = DataFrame con etiquetas recalculadas
- `df_input` = DataFrame original con `gf_mov_tags_arc` previo

**Salida:** DataFrame reconciliado

**Lógica (7 pasos internos):**
1. Construir mapa de etiquetas previas indexadas por código
2. Obtener etiquetas no reevaluadas (conservar intactas)
3. Para cada etiqueta nueva:
   - Si NO existía: agregar como nueva (updated_date=null)
   - Si existía sin cambio visibilidad: preservar tag anterior
   - Si cambió visibilidad: actualizar con new visibility + updated_date=ahora
4. Concatenar etiquetas nuevas + no reevaluadas
5. Remover nulos si aplica
6. Castear a schema
7. Retornar resultado unido

**Regla de updated_date:**
```python
if prior_tag_exists:
    if visibility_changed:
        updated_date = RuleEngine._execution_tag_date()  # Setter
    else:
        updated_date = prior_tag.updated_date            # Preservar
else:
    updated_date = null                                  # Nuevo tag
```

---

## 📝 Convenciones de Código

### Nomenclatura Existente (MANTENER)
- `label_id` - Identificador de etiqueta
- `LABELS_PARAM` - Parámetro de configuración
- `gf_mov_tags_arc` - Columna de etiquetas acumuladas
- `gf_pfm_tag_*` - Prefijo para campos de tag struct

### Patrones Spark
- **Transformaciones distribuidas:** `f.transform()` + lambda
- **Condicionales:** `f.when()/.otherwise()`
- **Maps/Estructuras:** `f.map_from_entries()`, `f.struct()`
- **Arrays:** `f.array()`, `f.array_contains()`, `f.concat()`
- **Timestamps:** `f.current_timestamp()`, `RuleEngine._execution_tag_date()`

### Principios de Codificación
- ✅ Cambios pequeños y enfocados por módulo
- ✅ Funciones puras + validaciones en capa config/schemas
- ✅ Tests para toda lógica crítica (merge, rules, schemas)
- ❌ NO introducir nuevas dependencias sin justificación
- ❌ NO mezclar refactors grandes con cambios funcionales pequeños
- ❌ NO reintroducir flags `enabled` como criterio principal

---

## 🧪 Tests y Validación

### Estructura de Tests
```
tests/
├── test_io/
│   ├── config/           # Tests de configuración
│   ├── schemas/          # Tests de esquemas
│   └── test_rule_engine.py  # Tests de RuleEngine (merge, apply_rules)
├── runtime/              # Tests de job/runtime
├── business/             # Tests de lógica business
└── features/             # Behave tests
```

- BDD language rule: for `tests/features/` (`.feature` files and step definitions), use English.

### Comandos
```bash
pytest -q                 # Quick run
kaa test                  # Full test suite (con coverage)
```

### Tests Críticos para Merge
- ✅ `test_merge_preserves_existing_tags_when_not_reevaluated`
- ✅ `test_merge_preserves_created_date_on_visibility_change`
- ✅ `test_merge_updates_visibility_when_changed`
- ✅ `test_merge_preserves_existing_updated_date_when_tag_does_not_change`
- ✅ `test_merge_replaces_previous_updated_date_with_current_process_date_when_tag_changes`

---

## 📚 Documentación

### Archivos Principales (en `/docs`)
1. **FLUJO_FUNCIONAL.md** - Overview del flujo end-to-end
2. **01_CARGAR_CONSTRUIR_CONFIGURACION.md** - Carga de config
3. **02_CARGAR_VALIDAR_CATALOGO.md** - Validación de catálogo
4. **03_MOTOR_REGLAS_RULEENGINE.md** - Detalle de RuleEngine (CORE)
5. **04_ESQUEMA_SALIDA_ESCRITURA.md** - Schema de salida
6. **05_FILE_UTILS.md** - Utilidades de archivo

### Actualizar en cada Feature
- Que afecte comportamiento → Actualizar docs listadas arriba
- Que afecte schema → Actualizar 04_ESQUEMA_SALIDA_ESCRITURA.md + 03_MOTOR_REGLAS_RULEENGINE.md
- Que afecte merge → Actualizar 03_MOTOR_REGLAS_RULEENGINE.md (secciones 3.11 - 3.12)

---

## ⚠️ Errores Comunes - NO Sugerir

❌ Reintroducir `enabled` como criterio principal de ejecución  
❌ Lógica "lista vacía = evaluar todo" para `LABELS_TO_APPLY`  
❌ Fallbacks de país distintos de `UNIVERSE_COUNTRY_ID`  
❌ Cambios de arquitectura no solicitados (nuevos orquestadores)  
❌ Usar `rules` en lugar de `labels` en configuración  

---

## 📖 Documentos Locales
- Arquitectura: [[kpfm/proyectos/kpfm-etiquetas/contexto/ARQUITECTURA]]
- Roadmap: [[kpfm/proyectos/kpfm-etiquetas/contexto/ROADMAP]]
- Decisiones: [[kpfm/proyectos/kpfm-etiquetas/decisiones/TAG_MERGING_LOGIC]]

---

**Última actualización:** 20 Julio 2026  
**Estado:** Consolidado - Una fuente de verdad para kpfm-etiquetas
