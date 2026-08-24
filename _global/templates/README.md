# 🎨 Templates - Plantillas Reutilizables

**Todos los templates están listos para copiar y personalizar**

---

## 📚 Templates Disponibles

### 1. PROJECT_TEMPLATE.md
**Uso:** Cuando agregues nuevo proyecto

```bash
# Paso 1: Copiar
cp PROJECT_TEMPLATE.md ../proyectos/nuevo-proyecto/README.md

# Paso 2: Editar
# Personaliza [NOMBRE_PROYECTO], [Stack], etc.

# Paso 3: Crear subfolders
mkdir -p ../proyectos/nuevo-proyecto/{contexto,referencias,decisiones,docs}

# Paso 4: Crear otros archivos en carpetas
# - contexto/COPILOT_INSTRUCTIONS.md
# - contexto/ARQUITECTURA.md
# - contexto/CAMBIOS_RECIENTES.md
# - contexto/ROADMAP.md
# - referencias/NOMENCLATURA.md
# - referencias/[TECH].md
# - etc.
```

### 2. DECISION_TEMPLATE.md
**Uso:** Cuando documentes decisión arquitectónica importante

```bash
# Paso 1: Copiar
cp DECISION_TEMPLATE.md ../proyectos/[proyecto]/decisiones/NOMBRE_DECISION.md

# Paso 2: Editar
# Title, problema, solución, impacto, etc.

# Paso 3: Link desde CAMBIOS RECIENTES
# En proyecto/contexto/CAMBIOS_RECIENTES.md agrega:
# - [[decisiones/NOMBRE_DECISION]] - descripción
```

### 3. FEATURE_TEMPLATE.md
**Uso:** Cuando implementes feature completa y quieras documentarla

```markdown
# Feature: [Nombre]
- Archivo: proyectos/[proyecto]/decisiones/feature-[nombre].md
- Copia template y rellena
- Incluye tests, code snippets, links a PRs
```

### 4. BUG_FIX_TEMPLATE.md
**Uso:** Bugs importantes que afectaron comportamiento

```markdown
# Bug Fix: [Descripción]
- Archivo: proyectos/[proyecto]/decisiones/bug-[nombre].md
- Raíz cause, señales, fix implementado, tests agregados
```

---

## 🔧 Cómo Agregar Template Nuevo

### Si Descubres Patrón Reutilizable
```
1. Crear: _global/templates/NUEVO_TEMPLATE.md
2. Docstring: "Cuándo usar, cómo copiar, ejemplo"
3. Placeholder: [COMO_ESTO] para personalizar
4. Link desde aquí (README.md)
5. Usar en tus proyectos
6. Refinar según experiencias
```

### Ejemplo: Testing Template
```markdown
# TEST_TEMPLATE.md

**Uso:** Cuando necesites escribir suite completa de tests

## Structure
```python
class TestMyFeature:
    def setup_method(self):
        # Setup
        pass
    
    def test_happy_path(self):
        # Test normal case
        pass
    
    def test_edge_case_empty(self):
        # Test edge
        pass
    
    def test_error_case(self):
        # Test error
        pass
```

Listo. Otros lo usarán.
```

---

## 📋 Templates Por Categoría

### Estructura Proyecto
- **PROJECT_TEMPLATE.md** - README proyecto
- (Próximamente: MICROSERVICE_TEMPLATE, API_TEMPLATE, etc.)

### Documentación
- **DECISION_TEMPLATE.md** - Decisión arquitectónica
- **FEATURE_TEMPLATE.md** - Feature documentada
- **BUG_FIX_TEMPLATE.md** - Bug importante
- (Próximamente: REFACTORING_TEMPLATE, etc.)

### Código
- **TEST_TEMPLATE.md** - Suite de tests
- **CONFIG_TEMPLATE.md** - Configuración nueva
- (Próximamente: API_ENDPOINT_TEMPLATE, CLASS_TEMPLATE, etc.)

---

## 🚀 Uso Rápido

### "Quiero agregar nuevo proyecto"
```bash
cp PROJECT_TEMPLATE.md ../proyectos/mi-proyecto/README.md
# Edita y rellena
```

### "Quiero documentar decisión"
```bash
cp DECISION_TEMPLATE.md ../proyectos/mi-proyecto/decisiones/DECISION_NAME.md
# Edita y rellena
```

### "Quiero escribir tests"
```bash
# (Si existe TEST_TEMPLATE.md)
cp TEST_TEMPLATE.md ../proyectos/mi-proyecto/tests/test_my_feature.py
# Edita y rellena
```

---

## 💡 Tips Sobre Templates

### ✅ Hacer
- ✅ Keep templates simple (no over-engineer)
- ✅ Usar placeholders: [COMO_ESTO]
- ✅ Incluir ejemplos reales
- ✅ Documentar "cuándo usar"
- ✅ Actualizar si encuentras mejora

### ❌ No Hacer
- ❌ Template 1:1 copy (personaliza siempre)
- ❌ Dejar placeholders sin rellenar
- ❌ Crear template para cada pequeña cosa
- ❌ Template con lógica hardcoded

---

## 🔗 Cómo Linkear Template

### Desde Proyecto
```markdown
# En proyectos/[proyecto]/README.md
Para documentar decisión: [[_global/templates/DECISION_TEMPLATE]]
Para template de proyecto: [[_global/templates/PROJECT_TEMPLATE]]
```

### Desde Obsidian
```
Click: _global/templates/DECISION_TEMPLATE.md
→ Copy content to your proyecto/decisiones/NEW_DECISION.md
```

---

## 📈 Crecimiento Esperado

### Mes 1 (Hoy)
```
templates/
├─ PROJECT_TEMPLATE.md
└─ DECISION_TEMPLATE.md
```

### Mes 3
```
templates/
├─ PROJECT_TEMPLATE.md
├─ DECISION_TEMPLATE.md
├─ FEATURE_TEMPLATE.md
├─ BUG_FIX_TEMPLATE.md
└─ TEST_TEMPLATE.md
```

### Mes 6+
```
templates/
├─ proyecto/
│  └─ [templates por tipo proyecto]
├─ documentacion/
│  └─ [templates docs]
├─ codigo/
│  └─ [templates code patterns]
└─ [otros]
```

---

## 🎓 Ejemplo Completo: Agregar Proyecto

### Paso 1: Copiar Template
```bash
cp PROJECT_TEMPLATE.md ../proyectos/analytics/README.md
```

### Paso 2: Personalizar
```markdown
# analytics - README

**Nombre Proyecto:** Analytics Engine
**Stack:** Python 3.11, Pandas, PostgreSQL
**Descripción:** Generates business analytics reports
**Ubicación Repo:** github.com/company/analytics
```

### Paso 3: Crear Carpetas
```bash
mkdir -p ../proyectos/analytics/{contexto,referencias,decisiones,docs}
```

### Paso 4: Crear COPILOT_INSTRUCTIONS
```bash
touch ../proyectos/analytics/contexto/COPILOT_INSTRUCTIONS.md
```

Contenido:
```markdown
# Instrucciones - Analytics

Sigue primero: [[_global/copilot/INSTRUCCIONES_BASE]]

## Stack
- Python 3.11
- Pandas 1.5+
- PostgreSQL 13+

## Local Rules
- Use type hints everywhere
- 100% test coverage for core
- Docstrings required
```

### Paso 5: Otros Archivos
```bash
touch ../proyectos/analytics/{ESTADO.md,contexto/ARQUITECTURA.md,contexto/CAMBIOS_RECIENTES.md,referencias/NOMENCLATURA.md}
```

### Paso 6: Update INDEX
```markdown
# En proyectos/ folder
- kpfm-etiquetas/
- analytics/           ← Nuevo
- [otros]
```

---

## 📞 Cuando Crear Nuevo Template

### Indicador: "Repito patrón en múltiples proyectos"

```
Ejemplo:
- 3 proyectos tienen misma estructura testing
- → Crear TEST_TEMPLATE.md

Ejemplo:
- 2 proyectos tienen API similar
- → Crear API_TEMPLATE.md

Recomendación:
- Después de repetir 2+ veces → template
- No antes (evita over-templating)
```

---

## ✅ Checklist: Template Bueno

- [ ] Clear "Cuándo usar"
- [ ] Step-by-step instructions
- [ ] Placeholders son obvios: [COMO_ESTO]
- [ ] Incluye 1-2 ejemplos reales
- [ ] Links a guías si aplica
- [ ] No tiene lógica hardcoded
- [ ] Testeable (puedo seguir pasos e implementar)

---

## 🔗 Referencias

- Main: [[INDEX]]
- How to navigate: [[NAVIGATION]]
- Global copilot: [[_global/copilot/INSTRUCCIONES_BASE]]
- Standards: [[../standards/]]

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026
**Status:** Listo para Usar
**Próximo:** Copiar PROJECT_TEMPLATE cuando agregues nuevo proyecto


