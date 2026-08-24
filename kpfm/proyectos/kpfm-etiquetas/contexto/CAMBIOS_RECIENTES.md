# Cambios Recientes - kpfm-etiquetas

**Registro de features, fixes y decisiones importantes**

---

## Sesion 21 Agosto 2026 (continuación) - Alineación de es_daily_tagging con cambios del categorizador global

### Summary
Se alineó la configuración del engine `es_daily_tagging` con los cambios aplicados en `categ_gl_git` (categorizador global):
- Simplificación de lectura desde entity catalog (solo `database_name` + `table_name`)
- Refactor de logging para usar `self.__logger` del job
- Generalización de mensajes de log
- Reestructuración de `tables.t_kpfm_mov_tags_arc` para enriquecimiento de tags

### Changes implemented
- `es_daily_tagging/eskpfmtaggerdaytagjobwgd2ww/custom.conf`
  - **`tables.t_kpfm_movements`:** Removidas propiedades de escritura (`save_mode`, `enforce_schema`, etc.)
    - Ahora solo contiene: `database_name`, `table_name`
    - Alineación: lectura pura desde entity catalog, validación de schema interna
  - **`tables.t_kpfm_mov_tags_arc`:** Reestructurada para enriquecimiento
    - Agregados: `enabled`, `total_jobs`, `strict_mode` (lectura desde parámetros)
    - Removidas: `save_mode`, `enforce_schema`, `partition_by`, etc. (no se usan en lectura)
  - **`paremeter`:** Corregidos nombres de placeholders
    - `MOV_LABELS_ARC_*` → `MOV_TAGS_ARC_*` (normalización de nomenclatura)
  - **Top-level config:** Removidas referencias redundantes a `MOV_TAGS_ARC_PATH_TEMPLATE`, etc.

### Configuration validation
✅ Configuración parseada correctamente (application.conf + custom.conf)
✅ Estructura de tablas validada:
- `t_kpfm_movements`: database_name=es_master, table_name=t_kpfm_movement
- `t_kpfm_mov_tags_arc`: enabled=false (default), path=__undefined__

### Decision
- Los cambios en es_daily_tagging son **no-funcionales** en esta sesión (el enriquecimiento de tags está deshabilitado por defecto)
- La estructura permite habilitar enriquecimiento cuando se necesite sin cambios de código
- Consistencia lograda entre categorizador global y engine específico de país

## Sesion 21 Agosto 2026 - Simplificacion de lectura de movimientos por entity catalog en `categ_gl_git`

### Summary
Se simplifico la lectura de `t_kpfm_movements` en el categorizador global cuando el origen es catalogo de entidades. La construccion de `TableConfiguration` ahora usa solo `database_name` y `table_name`, y se corrigio la invocacion de `read_mov_tags_arc()` para alinearla con su firma real.

### Changes implemented
- `glglcsadvpyglobaltagger/gl/runtime/batch/jobs/tagging_jobs.py`
  - Se elimino `path` de la configuracion de lectura de movimientos.
  - Se eliminaron tambien `schema_url`, `enforce_schema` y `partition_by` del `TableConfiguration` usado por `AdviceDataFrameReader().table(...)`.
  - Se mantiene `self.movements_table.transform_read(movements)` como proyeccion al schema canonico de entrada.
  - Se corrigio la llamada a `read_mov_tags_arc()` removiendo el parametro `spark` que no pertenecia a la firma del metodo.
- `tests/gl/runtime/batch/job/test_tagging_jobs_mov_tags.py`
  - Se actualizaron los tests para reflejar la construccion simplificada de `TableConfiguration`.
  - Se ajustaron todas las llamadas a `read_mov_tags_arc()` a la firma real del helper.

### Validation
- `pytest tests/gl/runtime/batch/job/test_tagging_jobs_mov_tags.py -v` → `17 passed`
- `pytest tests/gl/runtime/batch/job/test_tagging_jobs_v2.py -v` → `22 passed`
- `pytest tests/ -q` → `214 passed`

### Decision
- Para lecturas via entity catalog con `AdviceDataFrameReader().table(...)`, la configuracion minima relevante es `database_name` + `table_name`.
- `transform_read()` sigue teniendo sentido incluso leyendo desde catalogo: impone el schema canonico esperado por el pipeline (proyeccion/orden/fail-fast si falta una columna requerida).

### Links
- [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
- [[daily/2026-08-21]]

## Sesion 20 Agosto 2026 - Análisis: Lectura en Cascada de Catálogos de Entidades

### Summary
Análisis exhaustivo de la factibilidad de implementar lectura en cascada (entidades → parquet) en el categorizador global. Se investigó la implementación actual en `kpfm-etiquetas` y se determinó que el patrón es **factible, seguro y operacionalmente valioso**.

### Investigation Performed
- ✅ Revisión de código en `categ_gl_git`: `catalog_reader.py` (lectura actual solo parquet)
- ✅ Análisis de patrón probado en `kpfm-etiquetas`: `LabelsCatalogReader.load_labels()` (cascada 3-way)
- ✅ Verificación de dependencias: `adautils>=0.4.3` ya disponible
- ✅ Evaluación de riesgos: Bajo riesgo, backward compatible, sin cambios config
- ✅ Documentación creada: Decisión arquitectónica + prototipo código implementable

### Key Findings

**✅ FACTIBLE:**
- Patrón idéntico al usado en etiquetas (labels)
- Métodos necesarios: `spark.table()` (PySpark nativo) + `adautils.catalog`
- Cambios aislados: ~40 líneas en `catalog_reader.py`
- Sin breaking changes: Parquet primero, fallback transparente

**✅ SEGURO:**
- Backward compatible 100%
- Cambios sin impacto en configuración
- Logging transparente de source usado
- Tests unitarios y de integración cubren casos

**✅ VALIOSO:**
- Resuelve problema: Rutas parquet no disponibles en dev/test
- Permite usar tablas Hive en lugar de replicar parquet
- Reduce duplicación de datos (parquet en prod, entity en dev)

### Architecture Proposal

```
Lectura en Cascada:
├─ Intenta PARQUET (comportamiento actual)
│  └─ Si ÉXITO → usa parquet
│  └─ Si FALLA → log warning, continúa
├─ Intenta ENTIDAD (nuevo fallback)
│  └─ Si ÉXITO → usa entidad
│  └─ Si FALLA → log warning, continúa
└─ Si ambas fallan → manejo por REQUIRE_ALL_CATALOGS (sin cambios)
```

### Implementation Blueprint

**Métodos a agregar:**
1. `_load_from_entity_catalog()` (~30 líneas) - lee tabla Hive/Glue
2. Refactor `_load_catalog()` (~40 líneas) - implementa fallback automático
3. Tests (~80 líneas) - unit + integration
4. Docstrings y ejemplos

**Ejemplo de uso (sin cambios config):**
```hocon
# Actual (parquet)
LABELS_CATALOG_PATH = "/data/catalogs/labels"

# Con fallback (comparte config, intenta ambas)
# Comportamiento: intenta parquet, si falla → intenta "data.catalogs.labels" como tabla Hive
```

### Documentation Created

1. **`decisiones/LECTURA_CASCADA_CATALOGO_ENTIDADES.md`** (Decisión Arquitectónica - 220 líneas)
   - Resumen ejecutivo
   - Análisis actual (estado en ambos proyectos)
   - Arquitectura propuesta
   - Métodos de adautils disponibles
   - Riesgos y mitigaciones
   - Criterios de aceptación

2. **`decisiones/PROTOTIPO_LECTURA_CASCADA.md`** (Implementation Guide - 290 líneas)
   - Código exacto modificado/agregado
   - Testing strategy (unit + integration)
   - Configuration examples
   - Migration path operacional
   - Logging examples (success/fallback/failure)
   - Performance considerations

### Status

✅ **Análisis COMPLETADO - IMPLEMENTACIÓN FACTIBLE**
- Recomendación: APROBAR e implementar
- Prioridad: Media (no urgente, pero de alto valor)
- Timeline: 1-2 días (prototipo + testing + docs)
- Risk Level: BAJO (backward compatible, patrón probado)

### Next Steps

1. Visto bueno de tech lead categorizador global
2. Crear ticket en backlog: "feat: Add entity catalog fallback to CatalogReader"
3. Ejecutar según blueprint: prototipo → tests → review → merge

### References

- Decision docs:
  - `[[kpfm/proyectos/kpfm-etiquetas/decisiones/LECTURA_CASCADA_CATALOGO_ENTIDADES]]`
  - `[[kpfm/proyectos/kpfm-etiquetas/decisiones/PROTOTIPO_LECTURA_CASCADA]]`
- Patrón probado:
  - `kpfm-etiquetas/io/catalog/catalog_reader.py` líneas 144-177 (load_labels)
- Código actual:
  - `categ_gl_git/glglcsadvpyglobaltagger/gl/catalogs/catalog_reader.py` líneas 34-116

### Decision

✅ **APROBAR PARA BACKLOG**

El equipo de categorizador global debe evaluar si quiere implementar esta mejora. El análisis queda documentado en vault + documentación de código para decisiones futuras.

---

## Sesion 11 Agosto 2026 - Full schema alignment to `gf_pfm_mov_tag_arc` + acceptance coverage

### Summary
The output schema was fully aligned to the new canonical array field `gf_pfm_mov_tag_arc`, including acceptance scenarios and step definitions.

### Changes implemented
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/output_schema.py`
  - Updated output array field name from `gf_mov_tags_arc` to `gf_pfm_mov_tag_arc`.
- `tests/features/rule_engine/rule_engine.feature`
  - Updated array field references to `gf_pfm_mov_tag_arc`.
  - Updated status assertion field to `gf_pfm_tag_status_type`.
- `tests/features/steps/rule_engine_steps.py`
  - Updated tag array access to the new field.
  - Updated tag code assertion from `gf_pfm_tag_code` to `gf_pfm_tag_code_id`.
- `tests/features/e2e_pipeline/e2e_pipeline.feature`
  - Updated empty-array assertion to `gf_pfm_mov_tag_arc`.
- `tests/features/steps/pipeline_steps.py`
  - Updated output validations to use `GF_MOV_TAGS_ARC.name` (`gf_pfm_mov_tag_arc`).
  - Updated tag code/status checks to `gf_pfm_tag_code_id` and `gf_pfm_tag_status_type`.
- `tests/features/steps/geography_simulation_steps.py`
  - Updated historical tags fixtures to new fields:
    - `gf_pfm_tag_code_id`
    - `gf_pfm_tag_visible_type`
    - `gf_pfm_tag_scope_type`
    - `gf_pfm_tag_status_type`
  - Updated visibility assertions to string semantics (`"true"` / `"false"`).
- `docs/04_ESQUEMA_SALIDA_ESCRITURA.md`
  - Updated documentation to `gf_pfm_mov_tag_arc` nomenclature.

### Validation
- `pytest -q tests/` -> **226 passed, 1 skipped**
- `behave tests/features --no-capture` -> **4 features passed, 30 scenarios passed, 145 steps passed**

### Decision
- Canonical output array field for this project is now `gf_pfm_mov_tag_arc`.
- Acceptance tests remain synchronized with the runtime schema to avoid drift.

### Links
- `[[kpfm/proyectos/kpfm-etiquetas/ESTADO]]`
- `[[daily/2026-08-11]]`

---

## Sesion 11 Agosto 2026 - Output Schema Refactor: From Boolean to String Types

### Summary
Completed refactoring of the output schema to align with the new PFM standard: changed tag visibility/scope/status fields from boolean/specific types to string types for consistency with downstream systems.

### Changes implemented

#### 1. Schema Update
**File:** `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/output_schema.py`
- Already contained the new schema structure (gf_pfm_tag_code_id, gf_pfm_tag_visible_type, gf_pfm_tag_scope_type, gf_pfm_tag_status_type)
- All fields using StringType() instead of BooleanType()
- TAG_ENTRY_SCHEMA correctly structured with new field names

#### 2. Documentation Update
**File:** `docs/04_ESQUEMA_SALIDA_ESCRITURA.md`
- Updated section 4.1 (Estructura actual) to reflect current schema with:
  - GF_PFM_TAG_CODE_ID (not GF_PFM_TAG_CODE)
  - GF_PFM_TAG_VISIBLE_TYPE, GF_PFM_TAG_SCOPE_TYPE, GF_PFM_TAG_STATUS_TYPE (all StringType)
- Updated section 4.5 (Struct gf_mov_tags_arc) with:
  - Field descriptions now mention string values ("true"/"false" for visible_type)
  - Example JSON shows string literals instead of boolean
- Updated section 4.7 (Ejemplo completo) with gf_pfm_tag_code_id and string type values

#### 3. Test Fix
**File:** `tests/test_io/test_regex_patterns.py`
- Updated test_invalid_pattern_var_name_raises to expect "uppercase" message instead of "mayusculas"
- Ensures test matches actual validation error message in schemas.py

### Validation
- All tests pass: `pytest -q`
  - Result: 226 passed, 1 skipped
- No errors in schema, rule_engine, or test files (minor IDE warning on lambda expression is expected)

### Decision
- Output schema is now officially aligned with PFM naming convention
- Field visibility/scope/status are string types for consistency with downstream infrastructure
- Documentation is the source of truth for field names and types

### Links
- `[[kpfm/proyectos/kpfm-etiquetas/ESTADO]]`
- `[[daily/2026-08-11]]`

---

## Sesion 11 Agosto 2026 - Full behave suite migrated to English

### Summary
Acceptance testing assets were fully standardized to English for the whole behave suite, not only geography simulation.

### Changes implemented
- `tests/features/catalog_loading/catalog_loading.feature`
  - Fully translated to English.
  - Scenarios aligned with current catalog priority: parquet -> entity -> local.
- `tests/features/e2e_pipeline/e2e_pipeline.feature`
  - Fully translated to English.
  - Expected tag code aligned with current catalog (`ELECT`).
- `tests/features/rule_engine/rule_engine.feature`
  - Fully translated to English.
  - Rule id wording updated to block key (`label_electrolineras`).
- `tests/features/steps/catalog_steps.py`
  - Step phrases translated to English.
  - Added checks for parquet priority and entity non-query in priority scenario.
- `tests/features/steps/pipeline_steps.py`
  - Step phrases translated to English.
  - Rule config updated to `labels` + `LABELS_PARAM.LABELS_TO_APPLY` with `label_electrolineras`.
  - Added passthrough columns in `apply_tagging_rules()` to preserve `g_movement_id` in assertions.
- `tests/features/steps/rule_engine_steps.py`
  - Step phrases translated to English.
  - Rule config updated to string-based catalog/rule ids (`ELECT`, `label_electrolineras`).
  - Added passthrough columns in `apply_tagging_rules()`.
- `tests/features/geography_simulation/geography_simulation.feature`
  - Already in English from this date session and kept consistent.
- `.github/copilot-instructions.md`
  - Added explicit instruction: behave acceptance tests must be written in English.
- `kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md`
  - Added/updated same BDD language rule in English.

### Tests
- Command: `behave tests/features --no-capture`
- Result: `4 features passed, 30 scenarios passed, 145 steps passed`.

### Decision
- BDD acceptance artifacts (`.feature` + steps) are now English-first across the full suite.

### Links
- `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`
- `[[daily/2026-08-11]]`

---

## Sesion 11 Agosto 2026 - Error/assert message normalization in non-acceptance tests

### Summary
Follow-up cleanup to normalize remaining Spanish validation/error messages and test docstrings to English outside acceptance step files.

### Changes implemented
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/schemas.py`
  - Translated all `ValueError` messages to English (`LabelSchema`, `RuleSchema`, `VariantsSchema`, `CatalogSchema`, `RegexPatternSchema`).
- `tests/test_io/schemas/test_schemas.py`
  - Updated message assertions to match new English validation texts.
- `tests/business/labels/test_catalog_loader.py`
  - Translated module-level docstring to English.

### Tests
- `pytest -q tests/test_io/schemas/test_schemas.py tests/business/labels/test_catalog_loader.py`
- Result: `22 passed`.

### Decision
- Keep all validation and assertion-facing text in English across repo tests and runtime schemas.

---

## Sesion 11 Agosto 2026 - Repo-wide comments/docstrings cleanup to English

### Summary
A full pass was executed across the `etiquetas` repository to standardize remaining Spanish comments/docstrings to English, with explicit confirmation that acceptance step separators are kept.

### Changes implemented
- Acceptance files kept and standardized with separator blocks (`# ---------------------------------------------------------------------------` + section title):
  - `tests/features/environment.py`
  - `tests/features/steps/catalog_steps.py`
  - `tests/features/steps/pipeline_steps.py`
  - `tests/features/steps/rule_engine_steps.py`
  - `tests/features/steps/geography_simulation_steps.py`
- Additional repository-wide comment/docstring translation to English:
  - `tests/business/labels/test_electrolineras.py`
  - `tests/business/labels/custom/test_catalog_integration.py`
  - `tests/runtime/job/test_movement_tagger_job.py`
  - `tests/test_io/config/test_config_reader.py`
  - `tests/test_io/config/test_config.py`
  - `tests/test_io/test_file_utils.py`

### Validation
- Text scan: no accented Spanish remains in Python comments/docstrings (`grep` check).
- Acceptance tests: `behave tests/features --no-capture`
  - Result: `4 features passed, 30 scenarios passed, 145 steps passed`.
- Targeted pytest on touched modules:
  - `pytest -q tests/business/labels/test_electrolineras.py tests/test_io/test_file_utils.py tests/test_io/config/test_config.py tests/test_io/config/test_config_reader.py`
  - Result: `64 passed, 1 skipped`.

### Decision
- Keep separator blocks as a stable structural convention in acceptance step files.

---

## Sesion 11 Agosto 2026 - Acceptance test comments standardized to English + section separators

### Summary
All acceptance test comments/docstrings/assertion messages were normalized to English, and separator blocks were standardized in acceptance step files.

### Changes implemented
- `tests/features/environment.py`
  - Hook comments and docstrings translated to English.
  - Added separator blocks for `BEFORE ALL`, `BEFORE SCENARIO`, and `AFTER ALL`.
- `tests/features/steps/catalog_steps.py`
  - Translated remaining Spanish comments/docstrings/assertion messages to English.
- `tests/features/steps/pipeline_steps.py`
  - Translated remaining Spanish comments/docstrings/assertion messages to English.
- `tests/features/steps/rule_engine_steps.py`
  - Translated remaining Spanish comments/docstrings/assertion messages to English.
- `tests/features/steps/geography_simulation_steps.py`
  - Added separator blocks (`CONSTANTS`, `HELPERS`, `GIVEN`, `WHEN`, `THEN`).
  - Translated remaining Spanish comments/assertion messages to English.

### Tests
- Command: `behave tests/features --no-capture`
- Result: `4 features passed, 30 scenarios passed, 145 steps passed`.

### Decision
- Keep separator block style as the acceptance test standard across all related step files.

---

## Sesion 11 Agosto 2026 - Acceptance tests en ingles (behave)

### Resumen
Se migran los nuevos escenarios BDD de simulacion geografica/paralelismo a ingles y se documenta la regla para mantener los tests de aceptacion en ese idioma.

### Cambios implementados
- `tests/features/geography_simulation/geography_simulation.feature`
  - Traduccion completa de `# language: es` a `# language: en`.
  - Conservados los 11 escenarios de ES/MX, paralelismo, apagado de etiquetas, visibilidad y alta de etiqueta nueva.
- `tests/features/steps/geography_simulation_steps.py`
  - Traduccion de todos los decorators `@given/@when/@then` a frases en ingles para que coincidan con la feature.
  - Sin cambios de logica funcional del pipeline de pruebas.
- `.github/copilot-instructions.md`
  - Nueva regla: acceptance tests con behave (`*.feature` y steps) en ingles.
- `kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md`
  - Se agrega la misma regla de idioma BDD para `tests/features/`.

### Tests
- Comando: `behave tests/features/geography_simulation/geography_simulation.feature --no-capture`
- Resultado: `1 feature passed, 11 scenarios passed, 57 steps passed`.

### Decision
- Estandarizar BDD en ingles para mejorar consistencia con tooling y futuros casos de uso de acceptance.

### Links
- `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`
- `[[daily/2026-08-11]]`

---

##  Sesión 22 Julio 2026 - Acceso Copilot al Vault + Setup de Replicación

###  Resumen
Se documenta y formaliza que Copilot **tiene acceso de lectura/escritura** a todos los archivos `.md` del vault BBVA. Se crea guía global de setup para replicar en otros proyectos (kagr, etc).

### ✅ Cambios implementados

#### En el vault (`/Users/t022458/Documents/BBVA_vault/`)
1. **`_global/copilot/INSTRUCCIONES_BASE.md`** (v1.1)
   - Agregada sección " Acceso al Vault de Obsidian (Obligatorio)" al inicio
   - Detalló capacidades: lectura/escritura de `.md` usando rutas absolutas
   - Clarificó que NO es automático, requiere instrucción explícita
   - Agregó link a nuevo documento [[_global/copilot/ACCESO_VAULT_SETUP]]
   - Agregó sección " Instrucciones Específicas por Proyecto" con referencias a kpfm-etiquetas y kagr

2. **`kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md`** (actualizado)
   - Agregada sección " Acceso Vault Obsidian" con capacidades explícitas
   - Documentó estructura de carpetas del proyecto en vault
   - Linkea a instrucciones globales en INSTRUCCIONES_BASE.md

3. **`_global/copilot/ACCESO_VAULT_SETUP.md`** (NUEVO - Documento Global Reutilizable)
   - Guía paso a paso para setup de nuevo proyecto
   - Template para `.github/copilot-instructions.md` con sección acceso vault
   - Template para `contexto/COPILOT_INSTRUCTIONS.md`
   - Flujo de actualización esperado (lectura inicial, escritura final)
   - Checklist completo para agregar nuevo proyecto
   - Ejemplos con kpfm-etiquetas (completado) y kagr (próximo)

#### En el repositorio (`/Users/t022458/PycharmProjects/kpfm/lib/etiquetas/.github/`)
1. **`copilot-instructions.md`** - Sección "Operacion de contexto en Obsidian"
   - Reemplazadas rutas genéricas `~/Documents/BBVA_vault/` por absolutas `/Users/t022458/Documents/BBVA_vault/`
   - Agregada subsección "### Capacidades de Copilot en el Vault" con ✅ Lectura/✅ Escritura
   - Clarificado que es OBLIGATORIO mantener actualizado

###  Tests
- N/A (cambios de documentación/configuración solo)

###  Decisión Arquitectónica
- **Rutas absolutas obligatorias** en instrucciones de Copilot (garantiza compatibilidad con herramientas de lectura/escritura)
- **Patrón "lectura → cambios → escritura"** para mantener contexto sincronizado entre sesiones
- **Documento global reutilizable** (`ACCESO_VAULT_SETUP.md`) para escalar a otros proyectos sin duplicación
- **Links internos (Wikilinks)** en todas las referencias para facilitar navegación en Obsidian

###  Documentación que Linkea
- `[[_global/copilot/INSTRUCCIONES_BASE#-Acceso-Vault-Obsidian]]`
- `[[_global/copilot/ACCESO_VAULT_SETUP]]`
- `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS#-Acceso-Vault-Obsidian]]`
- Daily note: `[[daily/2026-07-22]]`

---

##  Sesión 20 Julio 2026 - Lectura de entrada: parquet -> entidad

###  Resumen
Se ajusta la lectura de la tabla de entrada (`t_kpfm_movements`) para intentar primero ruta parquet y, si el parquet no existe, hacer fallback a la tabla de entidades.

### ✅ Cambios implementados
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/runtime/job/movement_tagger_job.py`
  - `_read_and_filter()` ahora intenta `spark.read.parquet(path)` cuando `tables.input_table.path` esta configurado.
  - si el error es de "path not found", cae a `reader.table(self.config.input_table_configuration)`.
  - mantiene error explicito para fallos de parquet no relacionados con inexistencia de ruta.
- `resources/application.conf`
  - nueva variable opcional `INPUT_PATH`.
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/resources/config/geography_config/es_config.conf`
  - `tables.input_table.path = ${?config.INPUT_PATH}`.
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/resources/config/geography_config/mx_config.conf`
  - `tables.input_table.path = ${?config.INPUT_PATH}`.
- `tests/runtime/job/test_movement_tagger_job.py`
  - nuevos tests para prioridad parquet, fallback a entidad y error no-fallback.

###  Tests
- `pytest -q tests/runtime/job/test_movement_tagger_job.py tests/test_io/config/test_config.py`
- Resultado: 45 passed

###  Decision
- Prioridad de entrada para movimientos: **parquet -> entidad**.
- Fallback a entidad solo en caso de parquet no encontrado.

---

##  Sesión 20 Julio 2026 - Regla Daily Note por fecha

###  Resumen
Se formaliza en instrucciones una regla de cierre transversal: actualizar siempre una daily note por fecha, incluso cuando en el mismo dia se trabaje en multiples proyectos.

### ✅ Cambios implementados
- Se actualizo `kpfm/lib/etiquetas/.github/copilot-instructions.md` con:
  - regla obligatoria de daily note al cierre
  - checklist minimo para `daily/YYYY-MM-DD.md`
  - alineacion de rutas del vault por UUAA
- Se actualizo `BBVA_Vault/_global/copilot/INSTRUCCIONES_BASE.md` con la regla global de una nota por dia.
- Se actualizo `BBVA_Vault/kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md` con el flujo de cierre y contenido minimo de daily.
- Se creo `[[daily/2026-07-20]]` como nota diaria de la sesion.

###  Tests
- No aplica (cambios de documentacion/instrucciones).

###  Decision
- La trazabilidad diaria se registra en una unica note por fecha.
- `CAMBIOS_RECIENTES` y `ESTADO` del proyecto se siguen actualizando en paralelo.

---

##  Sesión 20 Julio 2026 - Instrucciones Copilot + Code Smell Fix

###  Resumen
Sesión enfocada en **code smell fixes** y **consolidación de documentación** del vault. Se elimina redundancia en excepciones y se actualiza la guía de Copilot con regla obligatoria para lectura de config desde ZIP.

### ✅ Cambios Implementados

#### 1. Fix Code Smell - Manejo Redundante de Excepciones
**Archivo:** `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/catalog/catalog_reader.py` (línea 137)

**Problema:** Captura redundante
```python
# ANTES
except (FileNotFoundError, OSError) as exc:
```

**Solución:** Eliminar redundancia (FileNotFoundError hereda de OSError)
```python
# DESPUÉS
except OSError as exc:
```

**Por qué:** Capturar la clase padre es suficiente y más limpio. Semántica idéntica.

#### 2. Actualización Instrucciones Copilot
**Archivo:** `.github/copilot-instructions.md`

**Nueva sección:** `### Lectura de archivos de config desde ZIP`

**Contenido:**
- Regla obligatoria: Usar siempre `_get_resource_path()`
- Ejemplo correcto para filesystem Y ZIP
- Explicación del mecanismo (extracción automática a temp dir)
- Lista de antipatrones (qué NO hacer)

**Beneficio:** Copilot seguirá este patrón en futuras sesiones

#### 3. Consolidación Vault
**Archivos actualizados:**
- `proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md` (36 → 400+ líneas)
  - Llevado contenido completo del legacy al namespace del proyecto
  - Una fuente de verdad centralizada
- `proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026.md` (CREADO)
  - Auditoría completa: Duplicaciones, Wikilinks rotos, Consolidación recomendada
  - Plan de 3 fases para limpiar el vault

###  Tests

- ✅ No hay tests impactados (cambio solo en manejo de excepciones)
- Validación: Sin errores

###  Archivos Tocados

| Archivo | Cambio |
|---------|--------|
| `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/catalog/catalog_reader.py` | Line 137: Fix |
| `.github/copilot-instructions.md` | Nueva sección ZIP |
| `proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md` | Consolidación (36 → 400+ líneas) |
| `proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026.md` | CREADO |

###  Decisión Arquitectónica

1. **`_get_resource_path()` es el patrón único** para lectura de config desde ZIP
   - Implementado en: `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/config/config.py`
   - Maneja automáticamente: filesystem local + ZIP extraction
   
2. **Instrucciones de Copilot codificadas** como regla obligatoria
   - Ventaja: Consistencia en futuras sesiones
   - Visibilidad: Documentado en `.github/copilot-instructions.md`

3. **Vault consolidado** con una fuente de verdad por proyecto
   - COPILOT_INSTRUCTIONS: Movido del legacy al namespace `proyectos/`
   - Facilita mantenimiento y evita divergencias

---

##  Sesión 29 Junio 2026 - Tag Merging + Updated Date Field

###  Resumen
Implementación completa de merging inteligente de etiquetas con preservación de historia. Se agregó campo `gf_pfm_tag_updated_date` para rastrear cuándo cambió la propiedad de una etiqueta.

### ✅ Features Completados

#### 1. Método `merge_with_existing_tags()`
**Archivo:** `io/rule_engine.py`
**Líneas:** 54 nuevas
**Propósito:** Reconciliar etiquetas nuevas con previas

**Cambios:**
- ✅ Nueva método estático `merge_with_existing_tags(df_new, df_input)`
- ✅ Preserva etiquetas no-reevaluadas
- ✅ Preserva `created_date` original
- ✅ Actualiza visibilidad y otros campos si cambiaron
- ✅ Centraliza timestamps via `_execution_tag_date()`

**Test Coverage:** 7 tests específicos para merge

#### 2. Schema Extension: `gf_pfm_tag_updated_date`
**Archivo:** `io/schemas/output_schema.py`
**Cambios:**
- ✅ Nuevo StructField en `TAG_ENTRY_SCHEMA` (posición 3)
- ✅ Tipo: `TimestampType()`, nullable
- ✅ Semántica: null si sin cambios, timestamp si actualizó

**Validación:** Schema tests aseguran presencia, orden y tipo

#### 3. Integración en Job
**Archivo:** `runtime/job/movement_tagger_job.py`
**Cambios:**
- ✅ Agregar `GF_MOV_TAGS_ARC.name` a `passthrough_columns`
- ✅ Invocar `engine.merge_with_existing_tags()` después de `apply_tagging_rules()`

#### 4. Documentación Completa
**Archivos Actualizados:**
- `docs/03_MOTOR_REGLAS_RULEENGINE.md` (→ secciones 3.11, 3.12)
- `.github/copilot-instructions.md` (→ reglas RuleEngine)
- `README.md` proyecto

**Incluye:**
- ✅ 7-pasos internos explicados
- ✅ Documentación cada función Spark usada
- ✅ Ejemplos mentales entrada/salida
- ✅ Reglas de semántica de `updated_date`

###  Tests Implementados

**Archivo:** `tests/test_io/test_rule_engine.py`

| Caso | Descripción | Status |
|------|-------------|--------|
| `test_merge_preserves_existing_tags_when_not_reevaluated` | Tags no-reevaluados intactos | ✅ |
| `test_merge_preserves_created_date_on_visibility_change` | `created_date` siempre conservado | ✅ |
| `test_merge_updates_visibility_when_changed` | Visibilidad actualiza correctamente | ✅ |
| `test_merge_appends_new_tags_to_existing` | Nuevas etiquetas se agregan | ✅ |
| `test_merge_first_time_tagging_empty_prior_array` | Funciona con array vacío previo | ✅ |
| `test_merge_preserves_existing_updated_date_when_tag_does_not_change` | `updated_date` preservado sin cambios | ✅ NEW |
| `test_merge_replaces_previous_updated_date_with_current_process_date_when_tag_changes` | `updated_date` actualizado en cambios | ✅ NEW |

**Comando:** `pytest -q` 
**Resultado:** 17 tests passed ✅

###  Impacto

| Aspecto | Antes | Después |
|---------|-------|---------|
| Pérdida de historia | ❌ Sí | ✅ No |
| Auditoría de cambios | ❌ No | ✅ Sí (via updated_date) |
| Tags no-reevaluados | ❌ Desaparecen | ✅ Se preservan |
| `created_date` | ❌ Sobrescrito | ✅ Preservado |

---

##  Estado Actual (29 Junio - Última Actualización Anterior)

- ✅ Merge de etiquetas implementado
- ✅ Campo `gf_pfm_tag_updated_date` incorporado
- ✅ Vault migrando a estructura multi-proyecto
-  Consolidación de documentación en progreso

---

##  Referencias Rápidas

**Para próximas sesiones:**
1. Leer: [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
2. Reglas: [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]
3. Decisión: [[kpfm/proyectos/kpfm-etiquetas/decisiones/TAG_MERGING_LOGIC]]
4. Auditoría: [[kpfm/proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026]]

---

**Última Actualización:** 20 Julio 2026  
**Status:** ✅ Sesiones Completadas - Listo para Producción