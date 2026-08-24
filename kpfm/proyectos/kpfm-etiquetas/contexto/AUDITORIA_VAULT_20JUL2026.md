# Auditoría de Vault - kpfm-etiquetas (20 Julio 2026)

## 🔍 Hallazgos Principales

### 1. ⚠️ DUPLICACIÓN CRÍTICA: COPILOT_INSTRUCTIONS

**Archivos duplicados:**
- `[[contexto/COPILOT_INSTRUCTIONS]]` (263 líneas) - LEGACY
- `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]` (36 líneas) - NUEVO

**Problema:**
- La versión **LEGACY** tiene contenido completo, detallado y vigente (Stack, Reglas Funcionales, Config, RuleEngine, Tests, Docs)
- La versión **NUEVA** es apenas un índice de referencias sin contenido real
- **Resultado:** Copilot consultará la versión corta sin contexto funcional

**Impacto:**
- Pérdida de context crítico (RuleEngine, schemas, merge logic)
- Instrucciones incompletas para futuras sesiones
- Desconexión entre instrucciones globales y locales

**Solución:** 
→ **Consolidar**: Llevar todo el contenido útil de la versión LEGACY a la versión NUEVA (proyecto-namespaced)

---

### 2. ⚠️ DUPLICACIÓN: CAMBIOS_RECIENTES

**Archivos duplicados:**
- `[[contexto/CAMBIOS_RECIENTES]]` (237 líneas) - LEGACY + Histórico completo
- `[[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]` (10 líneas) - NUEVO + Básico

**Problema:**
- Historial detallado de sesiones vive en contexto global
- Proyecto nuevo tiene solo 2 líneas (creación vault + wikilinks)
- **Deuda:** Sesión 20 Julio 2026 (hoy) NO fue registrada

**Solución:**
→ **Migrar** historial completo de contexto global a proyecto  
→ **Actualizar** con sesión 20 julio (ZIP instructions + code smell fix)

---

### 3. ⚠️ DESCONEXIÓN: WIKILINKS INCOMPLETOS

**Problemas detectados:**

#### a) ESTADO.md → Promete actualizar notas
```markdown
## Siguiente paso
- Alinear notas antiguas de `contexto/`, `referencias/` y `decisiones/` 
  al nuevo namespace de `proyectos/kpfm-etiquetas/`.
```
**Realidad:** No se hizo. Los archivos siguen en `contexto/` (global) sin enlace desde el proyecto.

#### b) COPILOT_INSTRUCTIONS (nuevo) marca archivo legacy
```markdown
## Puente historico
- Legacy: [[contexto/COPILOT_INSTRUCTIONS]]
```
**Problema:** Este enlace rompe la navegación; debe ser marcar como obsoleto.

#### c) Falta de cross-links en graph view
- Documentos globales (`_global/`) no enlazan con proyecto específico
- Proyecto no enlaza documentos globales de forma clara (INDEX, NAVIGATION)

---

### 4. 📊 TABLA COMPARATIVA

| Tema | Legacy (global) | Nuevo (proyecto) | Status |
|------|-----------------|------------------|--------|
| COPILOT_INSTRUCTIONS | 263 líneas ✅ | 36 líneas (índice) ⚠️ | Duplicado + incompleto |
| CAMBIOS_RECIENTES | 237 líneas + historial | 10 líneas | Divergente |
| ESTADO | No existe | 15 líneas | OK (nuevo es suficiente) |
| README | No existe | 28 líneas | OK (nuevo es suficiente) |
| ARQUITECTURA | Parcial en legacy | Existe | Revisar |
| ROADMAP | Parcial en legacy | Existe | Revisar |

---

## 🛠️ Plan de Consolidación (Recomendado)

### Fase 1: Consolidar COPILOT_INSTRUCTIONS ✅ PRIORITARIO
**Acción:** Llevar contenido completo de `[[contexto/COPILOT_INSTRUCTIONS]]` (versión legacy) a `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`

**Estructura resultado:**
```markdown
# Instrucciones Copilot - kpfm-etiquetas

## 📌 Base Obligatoria (links a global)
- [[_global/copilot/INSTRUCCIONES_BASE]]
- [[_global/standards/WIKILINKS_CONVENCION]]

## 🎯 Contexto Local (contenido completo)
- Stack
- Reglas Funcionales
- Arquitectura RuleEngine
- Schemas
- Tests
- Etc.

## 🔗 Referencias Locales
- Docs: [[kpfm/proyectos/kpfm-etiquetas/docs/README]]
- Decisiones: [[kpfm/proyectos/kpfm-etiquetas/decisiones/]]
```

**Beneficio:** Una fuente de verdad, project-namespaced, con contexto completo.

---

### Fase 2: Consolidar CAMBIOS_RECIENTES
**Acción:** 
1. Copiar historial desde `[[contexto/CAMBIOS_RECIENTES]]` (líneas 1-237) a `[[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]`
2. Agregar nueva entrada: Sesión 20 julio 2026 (ZIP instructions + code smell)
3. Marcar `[[contexto/CAMBIOS_RECIENTES]]` como ARCHIVED (si no se usa para otros proyectos)

**Beneficio:** Historial centralizado en el proyecto mismo, no disperso.

---

### Fase 3: Limpiar Referencias Legacy
**Acciones:**
1. En `[[contexto/COPILOT_INSTRUCTIONS]]` → Marcar como ARCHIVED + redirect a proyecto
2. Revisar si hay otros docs en `contexto/`, `referencias/`, `decisiones/` (global) que pertenecen al proyecto
3. Migrar o enlazar explícitamente

---

## 🔗 Análisis de Wikilinks - Conexiones Verificadas

### ✅ OK - Enlaces internos correctos
- `[[_global/copilot/INSTRUCCIONES_BASE]]` ← Existe y es referencia obligatoria
- `[[_global/standards/WIKILINKS_CONVENCION]]` ← Existe, necesario
- `[[kpfm/proyectos/kpfm-etiquetas/ESTADO]]` ← Existe, OK
- `[[kpfm/proyectos/kpfm-etiquetas/README]]` ← Existe, OK

### ⚠️ ROTTOS O INCOMPLETOS
- `[[contexto/COPILOT_INSTRUCTIONS]]` → Debe ser ARCHIVED, no referenciado
- `[[kpfm/proyectos/kpfm-etiquetas/docs/README]]` → VERIFICAR si existe
- `[[kpfm/proyectos/kpfm-etiquetas/decisiones/]]` → VERIFICAR contenido

---

## 📋 Contenidos Duplicados a Revisar

### En el repositorio PAD (fuera de vault)
- `.github/copilot-instructions.md` → También contiene instrucciones
- Verificar qué está sincronizado y qué no

---

## 📌 Recomendación de Sesión Hoy (20 Julio)

**Prioridad 1:** Consolidar COPILOT_INSTRUCTIONS (versión proyecto con contenido legacy)  
**Prioridad 2:** Migrar CAMBIOS_RECIENTES + agregar entrada de hoy  
**Prioridad 3:** Limpiar referencias legacy, marcar ARCHIVED  

**Tiempo estimado:** 30-45 minutos para las 3 fases.

---

**Auditoría completada:** 20 Julio 2026, 11:20 AM  
**Auditor:** GitHub Copilot  
**Próximo paso:** Ejecutar consolidación (si se aprueba)

