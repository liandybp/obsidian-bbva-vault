# Decisión: Tag Merging con Historia Preservada

**Fecha:** Sesión 29 Junio 2026
**Estado:** ✅ Implementado y Testeado
**Autor:** Usuario + GitHub Copilot
**Clasificación:** Feature Core - Modo de Re-procesamiento

---

## 🎯 Problema Identificado

### Escenario
Un movimiento `m123` fue procesado y etiquetado en el pasado con:
```
gf_mov_tags_arc = [
  {code: "ELEC", created_date: "2026-06-01", visible: true, ...}
]
```

Semanas después, el movimiento vuelve a entrar al flujo de etiquetado (por sincronización, corrección de datos, etc). El sistema recalcula las etiquetas y obtiene:
```
gf_mov_tags_arc_new = [
  {code: "ELEC", created_date: "2026-06-29", visible: false, ...}
]
```

### Pregunta
¿Qué hacer?

**Opción A (Antes):** Sobrescribir completamente
- ❌ Pérdida de historia (cuándo fue creado el tag originalmente)
- ❌ Imposible auditar cambios
- ❌ Difícil rastrear si fue error o cambio legítimo

**Opción B (Implementada):** Merge inteligente
- ✅ Preservar `created_date` original
- ✅ Preservar tags no reevaluados
- ✅ Actualizar solo si hay cambio
- ✅ Auditoría de cambios via `updated_date`

---

## 💡 Solución: Merge with Preserved History

### Regla Principal
**"Para cada movimiento, reconciliar etiquetas nuevas con previas: preservar historia, actualizar solo lo necesario"**

### Algoritmo (7 pasos)
```
Entrada: df_new (etiquetas recalculadas), df_input (previas)

Paso 1: Mapear etiquetas previas
  prior_tags_map = indexar df_input.gf_mov_tags_arc por código

Paso 2: Identificar códigos nuevos vs no-reevaluados
  new_codes = conjunto de códigos en df_new
  prior_codes = conjunto de códigos en df_input
  non_reevaluated_codes = prior_codes - new_codes

Paso 3: Obtener etiquetas no-reevaluadas (PRESERVAR INTACTAS)
  non_reeval_tags = df_input.gf_mov_tags_arc
                    .filter(tag.code in non_reevaluated_codes)

Paso 4: Para cada etiqueta nueva, RESOLVER estado
  if NOT EXISTS en prior_tags:
    → Usar como nueva (updated_date = null)
  elif EXISTS y NO CAMBIO VISIBILITY:
    → Preservar prior completamente
  elif EXISTS y SÍ CAMBIO VISIBILITY:
    → Usar new tag pero preservar created_date
    → Fijar updated_date = ahora
  
Paso 5: Concatenar etiquetas resueltas + no-reevaluadas
  result = new_resolved_tags [ ] non_reevaluated_tags

Paso 6: Remover nulos si aplica

Paso 7: Retornar resultado
  return df.select(..., result.alias("gf_mov_tags_arc"), ...)
```

---

## 📏 Schema: `gf_pfm_tag_updated_date` (NUEVO)

### Motivación
Necesitábamos diferencial:
- `created_date` → Cuándo se aplicó la etiqueta por PRIMERA VEZ
- `updated_date` → Cuándo cambió su propiedad (visibilidad, estado)

**Ejemplo:**
```
Tag "ELEC":
  2026-06-01: Creado con visible=true
  2026-06-15: Re-procesado, visible=true → sin cambio (updated_date=null)
  2026-06-29: Re-procesado, visible=false → cambió (updated_date="2026-06-29 14:30")
```

### Regla de Llenado
| Escenario | created_date | updated_date |
|-----------|--------------|--------------|
| Nueva etiqueta | `current_process_ts` | `null` |
| Sin cambio | `prior_created_date` | `prior_updated_date` (o `null`) |
| Cambio visibility | `prior_created_date` | `current_process_ts` |
| Cambio deleted_date | `prior_created_date` | `current_process_ts` |

**Invariante:** `updated_date == null` OR `updated_date >= created_date`

---

## 🔧 Implementación

### Ubicación
- **Método:** `RuleEngine.merge_with_existing_tags()`
- **Archivo:** `kpfm_ho_cs_fou_gdh_pys_movementstagger/io/rule_engine.py`
- **Líneas:** ~54 líneas

### Pseudocódigo Spark
```python
@staticmethod
def merge_with_existing_tags(df_new: DataFrame, df_input: DataFrame) -> DataFrame:
    # Paso 1: Mapear previas por código
    df_prior_map = (
        df_input.select(mov_id, f.col(tags_col).alias("_prior_array"))
        .withColumn(prior_col,
            f.map_from_entries(
                f.transform(f.coalesce(f.col("_prior_array"), f.array()),
                    lambda t: f.struct(
                        t[GF_PFM_TAG_CODE.name].alias("key"),
                        t.alias("value")
                    )
                )
            )
        )
    )
    
    # Paso 3: Obtener no-reevaluadas
    df_non_reeval = (
        df_new.join(df_input.select(...), ...)
        .withColumn(non_reeval_col,
            f.filter(f.col(tags_col),
                lambda t: ~f.size(
                    f.filter(..., lambda n: n[CODE] == t[CODE])
                ) > 0
            )
        )
    )
    
    # Paso 4: Resolver cada nueva
    def _resolve_tag(new_tag, prior_map):
        code = new_tag[GF_PFM_TAG_CODE.name]
        prior_tag = prior_map[code]
        prior_exists = prior_tag.isNotNull()
        visible_changed = ...
        
        return (
            f.when(~prior_exists, new_tag)
                .when(visible_changed, updated_tag)
                .otherwise(prior_tag)
        ).cast(TAG_ENTRY_SCHEMA)
    
    # Paso 5: Concatenar
    result = (
        df_new
        .withColumn(resolved_col, f.transform(..., _resolve_tag))
        .withColumn(final_tags,
            f.concat(f.col(resolved_col), f.col(non_reeval_col))
        )
    )
    
    return result
```

---

## ✅ Test Coverage

### Tests Implementados (7 casos)
1. ✅ Preservar etiquetas no-reevaluadas intactas
2. ✅ Preservar `created_date` en tag reutilizado
3. ✅ Actualizar `visible` si cambió
4. ✅ Agregar nuevas etiquetas al array
5. ✅ Primera vez tagging (array previo vacío)
6. ✅ **Preservar `updated_date` cuando NO hay cambio** (NEW)
7. ✅ **Refresco `updated_date` cuando SÍ hay cambio** (NEW)

### Comando Ejecución
```bash
pytest tests/test_io/test_rule_engine.py::test_merge_* -v
# Resultado: 7 passed ✅
```

---

## 🎓 Decisiones de Diseño Explicadas

### D1: ¿Por qué preservar vs sobrescribir?
**Respuesta:** 
- Auditoría: El sistema necesita histórico completo de cambios
- Datos correctos: Si regla no reevalúa etiqueta, NO debería desaparecer
- Ejemplo: Tag "ELEC" creado 2026-06-01, vuelve a procesarse 2026-06-29 pero la nueva regla no aplica → sin cambio, preservar

### D2: ¿Por qué separar `created_date` y `updated_date`?
**Respuesta:**
- Trazabilidad: Saber cuándo se creó vs cuándo se cambió
- Reportes: "Etiquetas con > 30 días sin actualización"
- Auditoría: Diferenciar "cambios automáticos" de "etiquetas antiguas"

### D3: ¿Por qué `updated_date = null` para sin cambios?
**Respuesta:**
- Diferencial: `null` = nunca ha cambiado (vs date = cambió)
- Performance: No escribir unescesariamente (write-once para verdaderos cambios)
- Semántica clara: Si alguien ve `updated_date=null`, sabe que nunca fue actualizada

### D4: ¿Por qué `_execution_tag_date()` centralizado?
**Respuesta:**
- Consistencia: Todos los tags de un batch tienen **mismo timestamp**
- Mockable: Tests pueden fijar timestamp fijo sin esperar ejecución real
- Mantenibilidad: Un único lugar si quiero cambiar lógica (ej: UTC, zona horaria, etc)

### D5: ¿Por qué usar Spark Column DSL vs Pandas UDF?
**Respuesta:**
- Distribuido: Catalyst optimizer ejecuta automáticamente en todos nodos
- Performance: Sin serialización Python → sin overhead
- Legibilidad: Código declarativo, no imperativo
- Consistencia: Mismo patrón que `apply_tagging_rules()`

---

## 🚀 Integración en Job

### workflow_movement_tagger_job.py
```python
# Después de calcular nuevas etiquetas
df_new_tags = engine.apply_tagging_rules(
    df_rule_input,
    passthrough_columns=[
        G_MOVEMENT_ID.name,
        G_INF_UNIVERSE_COUNTRY_ID.name,
        G_BUCKET_ID.name,
        GF_MOV_TAGS_ARC.name,  # ← OBLIGATORIO: anterior
    ],
)

# Reconciliar con previas
df_final = engine.merge_with_existing_tags(df_new_tags, df_rule_input)

# Escribir resultado
df_final.write.mode("overwrite").saveAsTable("t_kpfm_movements_tagged")
```

---

## 📊 Impacto

### Antes (Sobrescribir)
```
Movimiento m123:
  Input: gf_mov_tags_arc = [ELEC(created:2026-06-01, visible:true)]
  Proceso recalcula: [ELEC(visible:false)]  ← new
  Output: ❌ Pérdida de created_date original
          ❌ Imposible auditar cambio
```

### Después (Merge)
```
Movimiento m123:
  Input: gf_mov_tags_arc = [ELEC(created:2026-06-01, visible:true, updated:null)]
  Proceso recalcula: [ELEC(visible:false)]
  Merge logic aplicada:
    - created_date: preservar 2026-06-01
    - updated_date: fijar 2026-06-29 (cambió visibility)
    - visible: actualizar a false
  Output: ✅ [ELEC(created:2026-06-01, visible:false, updated:2026-06-29)]
          ✅ Auditoría completa disponible
```

---

## 🔄 Alternativas Consideradas

### A1: Versioning completo (shadow table)
- ✅ Histórico 100% completo
- ❌ Complejidad extra (FK, join, etc)
- ❌ Mucho storage
- ❌ Queries más lentas
**Decisión:** No implementar, `updated_date` es suficiente

### A2: Soft delete todos los anteriores
- ✅ Histórico legible
- ❌ Array crece infinitamente
- ❌ Confusion: cuál es la "versión actual"
**Decisión:** No implementar, merge es más limpio

### A3: Merge pero ignorar no-reevaluadas
- ✅ Más simple
- ❌ Pérdida de datos (tags viejos desaparecen)
**Decisión:** Rechazado, patrimonio importante

---

## 📝 Próximos Pasos

### Corto Plazo (Si aplica)
- [ ] Métricas: Contar tags nuevos vs actualizados vs preservados
- [ ] Dashboard: Comparativa created_date vs updated_date en query histórico

### Mediano Plazo
- [ ] Soft delete: Cuando `deleted_date != null`, excluir del output
- [ ] Caducidad: Tags con `updated_date` > 90 días → auto-clean

### Largo Plazo
- [ ] Machine learning: Predecir etiquetas futuro basado en histórico
- [ ] Rebalancing: Optimizar merge si DataFrame >> 10GB

---

**Versión:** 1.0 - Final
**Estado:** ✅ Implementado, Testeado, Documentado
**Última Actualización:** 29 Junio 2026

