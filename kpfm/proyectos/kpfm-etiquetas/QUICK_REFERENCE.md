# 🚀 Quick Reference - kpfm-etiquetas (Actualizado 20 Julho)

## ⚡ Inicio Rápido (Próximas Sesiones)

### 1️⃣ Antes de Implementar
```
1. Revisar: [[_global/copilot/INSTRUCCIONES_BASE]]
2. Leer: [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]
3. Estado: [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
4. Cambios: [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]
```

### 2️⃣ Mientras Implementas
**Regla obligatoria:** Usar `_get_resource_path()` para lectura de config desde ZIP
```python
from kpfm_ho_cs_fou_gdh_pys_movementstagger.io.config.config import _get_resource_path

resource_path = _get_resource_path("catalog/labels_catalog/t_kmrc_labels.csv")
with open(resource_path, mode="r", encoding="utf-8") as f:
    # procesar
```

### 3️⃣ Al Finalizar
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]] con tu sesión
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]] con estado real
- Ejecutar tests: `pytest -q`

---

## 🎯 Reglas Críticas (Copiar-Pegar)

### País
- **Única fuente:** `UNIVERSE_COUNTRY_ID` (env var o parámetro)
- **NO permitir:** Fallbacks a otro país

### Etiquetas
- **Control exclusivo:** `LABELS_PARAM.LABELS_TO_APPLY`
- **Si vacío:** NO se evalúa nada
- **NO sugerir:** "Lista vacía = evaluar todo"

### Schema Output
```python
TAG_ENTRY_SCHEMA = StructType([
    ("gf_pfm_tag_code", StringType()),                    # [1]
    ("gf_pfm_tag_created_date", TimestampType()),         # [2]
    ("gf_pfm_tag_updated_date", TimestampType()),         # [3] ← NUEVO
    ("gf_pfm_tag_deleted_date", TimestampType()),         # [4]
    ("gf_pfm_tag_visible", BooleanType()),                # [5]
    ("gf_pfm_tag_scope", StringType()),                   # [6]
    ("gf_pfm_tag_status", StringType()),                  # [7]
])
```

---

## 🔗 URLs Clave (Vault - Nueva estructura por UUAA)

| Documento | URL | Uso |
|-----------|-----|-----|
| Instrucciones (ACTIVA) | `kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS` | Consulta siempre primero |
| Cambios Recientes | `kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES` | Historial + sesiones |
| Estado Proyecto | `kpfm/proyectos/kpfm-etiquetas/ESTADO` | Status actual |
| Auditoría | `kpfm/proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026` | Problemas encontrados |
| Consolidación | `CONSOLIDACION_20JUL2026` | Resumen cambios vault |
| Validación | `VALIDACION_FINAL_20JUL2026` | Check de integridad |
| Migración UUAA | `MIGRACION_ESTRUCTURA_UUAA_20JUL2026` | Nueva estructura |

---

## 📁 Archivos Importantes (Repositorio)

### Configuración
- `resources/application.conf` → Config global
- `resources/config/labels.conf` → Etiquetas globales
- `resources/config/geography_config/{COUNTRY}_config.conf` → Config por país

### Lectura Config (SIEMPRE usar `_get_resource_path()`)
- ✅ `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/config/config.py` → `_get_resource_path()`
- ❌ `open()` directo sin `_get_resource_path()`
- ❌ `importlib.resources` sin wrapper

### RuleEngine (Core)
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/rule_engine.py`
  - `apply_tagging_rules()` → Evalúa etiquetas
  - `merge_with_existing_tags()` → Reconcilia con historia

### Schemas
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/` → Validaciones

### Tests
- `tests/test_io/` → Config + schemas + RuleEngine
- `tests/runtime/` → Job integration
- Comando: `pytest -q` o `kaa test`

---

## ✅ Checklist por Sesión

```
☐ Revisar [[_global/copilot/INSTRUCCIONES_BASE]]
☐ Revisar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
☐ Revisar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]

☐ Implementar cambio
☐ Escribir tests
☐ Ejecutar: pytest -q
☐ Actualizar CAMBIOS_RECIENTES
☐ Actualizar ESTADO
☐ Documentar decisión arquitectónica si aplica
```

---

## 🚫 Antipatrones (NO Hacer)

- ❌ Reintroducir flag `enabled` en reglas
- ❌ "Lógica lista vacía = evaluar todo" para LABELS_TO_APPLY
- ❌ Fallbacks de país != UNIVERSE_COUNTRY_ID
- ❌ Usar `rules` en lugar de `labels`
- ❌ Cambios de arquitectura no solicitados
- ❌ Abrir archivos sin `_get_resource_path()` en contexto ZIP

---

## 📞 Apoyo

**Si algo no funciona:**
1. Consultar [[kpfm/proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026]] (análisis de problemas)
2. Revisar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]] (historial)
3. Ejecutar `pytest -q` (validar código)
4. Ver `.github/copilot-instructions.md` en repositorio

---

## 🎁 Bonus: Commandos Útiles

```bash
# Tests
pytest -q                    # Test rápido
kaa test                      # Test suite completo + coverage

# Ejecución
python worker.py            # Entry point
spark-submit ...             # En cluster

# Lint/Format
black .                      # Format
flake8 .                     # Lint

# Docs
cat docs/FLUJO_FUNCIONAL.md # Overview
cat docs/03_MOTOR_REGLAS_RULEENGINE.md  # RuleEngine detalles
```

---

## 🗓️ Última Actualización

- **Fecha:** 20 Julio 2026
- **Por:** Consolidación Vault + Migración Estructura UUAA
- **Status:** ✅ Ready

**Próxima actualización:** Cuando haya feature/bug major

---

**Impreso para referencia rápida. Mantener este documento sincronizado.**

---

## 📌 Nota sobre Nueva Estructura (20 Julio)

**Cambio importante:**
- Antes: `[[proyectos/kpfm-etiquetas/...]]`
- Ahora: `[[kpfm/proyectos/kpfm-etiquetas/...]]`

Todos los Wikilinks en el vault se han actualizado a la nueva estructura **por UUAA**. Esto mejora la escalabilidad y coherencia del vault.

## ⚡ Inicio Rápido (Próximas Sesiones)

### 1️⃣ Antes de Implementar
```
1. Revisar: [[_global/copilot/INSTRUCCIONES_BASE]]
2. Leer: [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]
3. Estado: [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
4. Cambios: [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]
```

### 2️⃣ Mientras Implementas
**Regla obligatoria:** Usar `_get_resource_path()` para lectura de config desde ZIP
```python
from kpfm_ho_cs_fou_gdh_pys_movementstagger.io.config.config import _get_resource_path

resource_path = _get_resource_path("catalog/labels_catalog/t_kmrc_labels.csv")
with open(resource_path, mode="r", encoding="utf-8") as f:
    # procesar
```

### 3️⃣ Al Finalizar
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]] con tu sesión
- Actualizar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]] con estado real
- Ejecutar tests: `pytest -q`

---

## 🎯 Reglas Críticas (Copiar-Pegar)

### País
- **Única fuente:** `UNIVERSE_COUNTRY_ID` (env var o parámetro)
- **NO permitir:** Fallbacks a otro país

### Etiquetas
- **Control exclusivo:** `LABELS_PARAM.LABELS_TO_APPLY`
- **Si vacío:** NO se evalúa nada
- **NO sugerir:** "Lista vacía = evaluar todo"

### Schema Output
```python
TAG_ENTRY_SCHEMA = StructType([
    ("gf_pfm_tag_code", StringType()),                    # [1]
    ("gf_pfm_tag_created_date", TimestampType()),         # [2]
    ("gf_pfm_tag_updated_date", TimestampType()),         # [3] ← NUEVO
    ("gf_pfm_tag_deleted_date", TimestampType()),         # [4]
    ("gf_pfm_tag_visible", BooleanType()),                # [5]
    ("gf_pfm_tag_scope", StringType()),                   # [6]
    ("gf_pfm_tag_status", StringType()),                  # [7]
])
```

---

## 🔗 URLs Clave (Vault)

| Documento | URL | Uso |
|-----------|-----|-----|
| Instrucciones (ACTIVA) | `proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS` | Consulta siempre primero |
| Cambios Recientes | `proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES` | Historial + sesiones |
| Estado Proyecto | `proyectos/kpfm-etiquetas/ESTADO` | Status actual |
| Auditoría | `proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026` | Problemas encontrados |
| Consolidación | `CONSOLIDACION_20JUL2026` | Resumen cambios vault |
| Validación | `VALIDACION_FINAL_20JUL2026` | Check de integridad |

---

## 📁 Archivos Importantes (Repositorio)

### Configuración
- `resources/application.conf` → Config global
- `resources/config/labels.conf` → Etiquetas globales
- `resources/config/geography_config/{COUNTRY}_config.conf` → Config por país

### Lectura Config (SIEMPRE usar `_get_resource_path()`)
- ✅ `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/config/config.py` → `_get_resource_path()`
- ❌ `open()` directo sin `_get_resource_path()`
- ❌ `importlib.resources` sin wrapper

### RuleEngine (Core)
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/rule_engine.py`
  - `apply_tagging_rules()` → Evalúa etiquetas
  - `merge_with_existing_tags()` → Reconcilia con historia

### Schemas
- `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/` → Validaciones

### Tests
- `tests/test_io/` → Config + schemas + RuleEngine
- `tests/runtime/` → Job integration
- Comando: `pytest -q` o `kaa test`

---

## ✅ Checklist por Sesión

```
☐ Revisar [[_global/copilot/INSTRUCCIONES_BASE]]
☐ Revisar [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
☐ Revisar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]

☐ Implementar cambio
☐ Escribir tests
☐ Ejecutar: pytest -q
☐ Actualizar CAMBIOS_RECIENTES
☐ Actualizar ESTADO
☐ Documentar decisión arquitectónica si aplica
```

---

## 🚫 Antipatrones (NO Hacer)

- ❌ Reintroducir flag `enabled` en reglas
- ❌ "Lógica lista vacía = evaluar todo" para LABELS_TO_APPLY
- ❌ Fallbacks de país != UNIVERSE_COUNTRY_ID
- ❌ Usar `rules` en lugar de `labels`
- ❌ Cambios de arquitectura no solicitados
- ❌ Abrir archivos sin `_get_resource_path()` en contexto ZIP

---

## 📞 Apoyo

**Si algo no funciona:**
1. Consultar [[kpfm/proyectos/kpfm-etiquetas/contexto/AUDITORIA_VAULT_20JUL2026]] (análisis de problemas)
2. Revisar [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]] (historial)
3. Ejecutar `pytest -q` (validar código)
4. Ver `.github/copilot-instructions.md` en repositorio

---

## 🎁 Bonus: Commandos Útiles

```bash
# Tests
pytest -q                    # Test rápido
kaa test                      # Test suite completo + coverage

# Ejecución
python worker.py            # Entry point
spark-submit ...             # En cluster

# Lint/Format
black .                      # Format
flake8 .                     # Lint

# Docs
cat docs/FLUJO_FUNCIONAL.md # Overview
cat docs/03_MOTOR_REGLAS_RULEENGINE.md  # RuleEngine detalles
```

---

## 🗓️ Última Actualización

- **Fecha:** 20 Julio 2026
- **Por:** Consolidación Vault + Code Smell Fix
- **Status:** ✅ Ready

**Próxima actualización:** Cuando haya feature/bug major

---

**Impreso para referencia rápida. Mantener este documento sincronizado.**

