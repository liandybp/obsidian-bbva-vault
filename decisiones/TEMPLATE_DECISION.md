# Template: Decisión Arquitectónica

**Copia este archivo cuando necesites documentar una decision importante**

---

## Decisión: [NOMBRE DESCRIPTIVO]

**Fecha:** [Hoy]
**Status:** 🔶 En progreso / ✅ Implementado / ⚠️ Rechazado
**Autor:** [Tu nombre / GitHub Copilot]
**Relacionado a:** [[TAG_MERGING_LOGIC]] (vincular decisiones previas si aplica)

---

## 🎯 Contexto

### Problema Identificado
Describe el problema que observaste o que te plantearon.

**Ejemplo:**
```
Un movimiento que ya fue etiquetado en el pasado vuelve a entrar al flujo
de etiquetado. El sistema sobrescribía completamente las etiquetas previas,
perdiendo información de creación original.
```

### Por Qué Importa
Explica el impacto si NO resuelves.

**Ejemplo:**
- ❌ Pérdida de auditoría (no saber cuándo se creó tag)
- ❌ Inconsistencias en datos históricos
- ❌ Imposible rastrear cambios en etiquetas

---

## 💡 Solución Propuesta

### Opción A: [Alternativa 1]
**Pros:**
- ✅ Ventaja 1
- ✅ Ventaja 2

**Contras:**
- ❌ Desventaja 1
- ❌ Desventaja 2

### Opción B: [Alternativa 2] ← ELEGIDA
**Pros:**
- ✅ Ventaja 1
- ✅ Ventaja 2

**Contras:**
- ❌ Desventaja 1

### Opción C: [Alternativa 3]
**Pros:**
- ✅ Ventaja 1

**Contras:**
- ❌ Desventaja 1
- ❌ Desventaja 2

---

## 🏗️ Implementación

### Archivos Afectados
```
kpfm_ho_cs_fou_gdh_pys_movementstagger/io/rule_engine.py
  ├── Líneas: X-Y
  └── Cambio: [Descripción]

kpfm_ho_cs_fou_gdh_pys_movementstagger/io/schemas/output_schema.py
  ├── Líneas: X-Y
  └── Cambio: [Descripción]

tests/test_io/test_rule_engine.py
  ├── Tests nuevos: 7
  └── Coverage: [X%]
```

### Pseudocódigo / Diagrama
```
Entrada: df_new, df_input
    ↓
Mapear etiquetas previas por código
    ↓
Identificar no-reevaluadas
    ↓
Resolver cada etiqueta nueva
    ├─ Si NO existe: agregar
    ├─ Si existe sin cambio: preservar
    └─ Si cambió: actualizar + timestamp
    ↓
Concatenar + devolver
```

### Integración en Job
```python
# workflow_movement_tagger_job.py
df_result = engine.apply_tagging_rules(...)
df_result = engine.merge_with_existing_tags(df_result, df_input)  # ← Nueva línea
```

---

## ✅ Validación

### Tests Implementados
- ✅ Test caso 1: [Descripción]
- ✅ Test caso 2: [Descripción]
- ✅ Test caso 3: [Descripción]

**Comando ejecución:**
```bash
pytest tests/test_io/test_rule_*.py -v
# Resultado: X passed ✅
```

### Validaciones Automáticas
- ✅ Schema validation
- ✅ Type hints correctos
- ✅ Docstrings completos
- ✅ No breaking changes existente

---

## 📊 Impacto

### Antes (Status Quo)
```
Entrada:  tags_anteriores
Salida:   tags_nuevos (SOBRESCRITO)
Problema: ❌ Pérdida de historia
```

### Después (Con Decisión)
```
Entrada:  tags_anteriores ← preservados
Salida:   tags_merged (con historia conservada)
Resultado: ✅ Auditoría completa disponible
```

### Métricas
| Métrica | Antes | Después | Cambio |
|---------|-------|---------|--------|
| Pérdida de datos | 100% | 0% | -100% |
| Time to query historia | ∞ | O(n) | ✅ |
| Complejidad código | Baja | Media | +1 |
| Performance | ms | +2ms | Aceptable |

---

## 🔄 Decisiones Relacionadas

- [[TAG_MERGING_LOGIC]] - Father decision (si aplica)
- [[SCHEMA_EVOLUTION]] - Schema changes (si aplica)
- [[OTRO]] - Otra decisión relacionada

---

## 📝 Documentación

### Archivos Actualizados
- ✅ `docs/03_MOTOR_REGLAS_RULEENGINE.md` (sección 3.11)
- ✅ `.github/copilot-instructions.md` (reglas)
- ✅ `README.md` (si cambios significativos)

### Ejemplos Incluidos (en docs)
```markdown
### Uso Básico
[Ejemplo 1]

### Caso de Uso Avanzado
[Ejemplo 2]

### Errores Comunes
[Error 1 y solución]
```

---

## 🚀 Próximos Pasos

### Corto Plazo
- [ ] Merge a main
- [ ] Deploy a staging
- [ ] Validar con data real

### Mediano Plazo
- [ ] Agregar métrica X
- [ ] Dashboard de monitoreo
- [ ] Alert si [condición]

### Largo Plazo
- [ ] Refactor si performance issue
- [ ] Generalizar para [otro caso uso]
- [ ] Machine learning: [idea]

---

## 👥 Reviews y Feedback

### Revisado por
- [ ] Code review: [@person]
- [ ] Architecture review: [@person]
- [ ] QA testing: [@person]

### Feedback Aplicado
- [Feedback 1 y resolución]
- [Feedback 2 y resolución]

---

## 🎓 Lecciones Aprendidas

### Qué Salió Bien
- ✅ [Aprendizaje 1]
- ✅ [Aprendizaje 2]

### Qué Podría Mejorar
- 🟡 [Mejora potencial 1]
- 🟡 [Mejora potencial 2]

### Patrones para Reutilizar
```
Patrón 1: [Descripción]
  Beneficio: [Qué ganamos]
  Casos de uso futuro: [Dónde aplica]
```

---

## 📞 Referencias

**Vinculación a documentación:**
- Specifikation: [[COPILOT_INSTRUCTIONS#RuleEngine]]
- Tests: `tests/test_io/test_rule_engine.py:50-150`
- Commits: `git log --oneline --grep="merge"`

---

## ✨ Conclusión

**Decisión:** [Resumen 1 línea]

**Resultado:** [Qué logramos]

**Status:** ✅ Implementado y Testeado

---

## Metadata

| Campo | Valor |
|-------|-------|
| Versión | 1.0 |
| Estado | ✅ Finalizado |
| Última actualización | [Fecha] |
| Compatibilidad | Python 3.11+, PySpark 3.x |
| Breaking changes | No (backward compatible) |

---

**Creada por:** [Git username]
**Fecha:** [ISO 8601]
**Vault:** [[Enlace a vault Obsidian]]

**Próximo archivo de decisión:** Use this template y copia en `/decisiones/NUEVA_DECISION.md`

b