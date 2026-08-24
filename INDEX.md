# 🏛️ Vault Obsidian - Arquitectura Multi-Proyecto Escalable

**Vault Centralizado para Múltiples Proyectos + Instrucciones Globales GitHub Copilot**

Versión: 2.1 (Multi-Proyecto + Consolidado)
Fecha: Última actualización 20 Julio 2026
**Estado:** ✅ Consolidado - Una fuente de verdad por proyecto

---

## 🎯 Visión General

Este vault está diseñado para **crecer** con tus proyectos:

```
Un único vault centralizado
    ↓
Almacena TODOS tus proyectos + instrucciones globales
    ├─ _global/           ← Reglas comunes (reutilizable)
    ├─ proyectos/         ← Cada proyecto separate pero conectado
    │  ├─ kpfm-etiquetas/
    │  ├─ otro-proyecto/
    │  └─ proyecto-n/
    └─ [Más proyectos según crecen]
```

**Ventajas:**
- ✅ Una única fuente de verdad
- ✅ Instrucciones Copilot globales + por-proyecto
- ✅ Cross-linking entre proyectos
- ✅ Escalable: agregar proyecto = copiar carpeta + template
- ✅ Búsqueda global (`Cmd+Shift+F` encontrará TODO)

---

## 📂 Estructura Completa

```
📁 kpfm-obsidian-vault/
│
├─ 📍 INDEX.md                           ← EMPIEZA AQUÍ (este archivo)
├─ 🚀 SETUP.md                           ← Configuración inicial
├─ 🔗 NAVIGATION.md                      ← Cómo navegar el vault
│
├─ 🌍 _global/                           ← REGLAS Y STANDARDS COMPARTIDAS
│  │
│  ├─ 📋 copilot/
│  │  ├─ INSTRUCCIONES_BASE.md          ← Lo que TODOS los proyectos respetan
│  │  ├─ PATRONES_GENERALES.md          ← Patrones reutilizables
│  │  ├─ ANTI_PATRONES.md               ← Lo que NUNCA hacer
│  │  └─ README_COPILOT.md              ← Guía para usar con Copilot
│  │
│  ├─ 📐 standards/
│  │  ├─ NOMENCLATURA_GLOBAL.md          ← Términos/variables comunes
│  │  ├─ ARQUITECTURA_PATTERNS.md        ← Patrones arquitectónicos
│  │  ├─ TESTING_GUIDELINES.md           ← Estándares de testing
│  │  └─ DOCUMENTATION_TEMPLATES.md      ← Plantillas docs
│  │
│  ├─ 🎨 templates/
│  │  ├─ DECISION_TEMPLATE.md            ← Template decisión arquitectónica
│  │  ├─ FEATURE_TEMPLATE.md             ← Template feature
│  │  ├─ BUG_FIX_TEMPLATE.md             ← Template bug fix
│  │  └─ PROJECT_TEMPLATE.md             ← Template nuevo proyecto
│  │
│  └─ 🔗 shared/
│     ├─ STACK_GLOBAL.md                 ← Tech stacks comunes
│     ├─ HERRAMIENTAS.md                 ← Tools que usamos
│     └─ LINKS_EXTERNOS.md               ← Referencias externas
│
├─ 📦 proyectos/                         ← PROYECTOS ESPECÍFICOS
│  │
│  └─ 📁 kpfm-etiquetas/                 ← Proyecto 1 (actual)
│     ├─ 🚀 README.md                    ← Overview proyecto
│     ├─ 📊 ESTADO.md                    ← Estado actual (update cada sesión)
│     │
│     ├─ 📋 contexto/                    ← Context específico proyecto
│     │  ├─ COPILOT_INSTRUCTIONS.md      ← Reglas proyecto + global
│     │  ├─ ARQUITECTURA.md              ← Arquitectura específica
│     │  ├─ CAMBIOS_RECIENTES.md         ← Histórico sesiones
│     │  └─ ROADMAP.md                   ← Planes futuros
│     │
│     ├─ 📚 referencias/                 ← Referencias rápidas
│     │  ├─ NOMENCLATURA.md              ← Variables específicas
│     │  ├─ SPARK_FUNCTIONS.md           ← Funciones Spark
│     │  ├─ CONFIGURACION.md             ← Config específica
│     │  └─ SNIPPETS.md                  ← Code snippets útiles
│     │
│     ├─ 🏗️ decisiones/                  ← Decisiones arquitectónicas
│     │  ├─ TAG_MERGING_LOGIC.md         ← Decisión 1
│     │  └─ [DECISION_N].md              ← Decisión N
│     │
│     └─ 📖 docs/                        ← Documentación links
│        └─ README.md                    ← Links a /docs del repo
│
│  ├─ 📁 otro-proyecto/                  ← Proyecto 2 (cuando lo agregues)
│  │  └─ [misma estructura que arriba]
│  │
│  └─ 📁 proyecto-n/                     ← Proyecto N
│     └─ [misma estructura que arriba]
│
├─ 🔧 settings/                          ← Configuración Obsidian
│  └─ README.md                          ← Plugins recomendados
│
└─ 📝 archivos-legacy/                   ← (Opcional) Archivos viejos
   └─ [Versiones anteriores si aplica]
```

---

## 🌟 Características Clave

### 1. Instrucciones Globales Copilot
```
_global/copilot/INSTRUCCIONES_BASE.md
    ↓
Contiene: Reglas que todos los proyectos respetan
    ├─ Stack tecnológico común
    ├─ Patrones de código globales
    ├─ Anti-patrones universales
    └─ Estándares de testing
```

### 2. Instrucciones Por-Proyecto
```
proyectos/[proyecto]/contexto/COPILOT_INSTRUCTIONS.md
    ↓
Contiene: Reglas específicas de [proyecto]
    ├─ Stack técnico local
    ├─ Convenciones proyecto
    ├─ Patrones específicos
    └─ Referencias a _global/copilot (cross-linking)
```

### 3. Cross-Linking Obsidian
Todo está conectado automáticamente:
```markdown
# En proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md
Consulta también: [[_global/copilot/INSTRUCCIONES_BASE]]
Patrones reutilizables: [[_global/standards/ARQUITECTURA_PATTERNS]]
```

### 4. Graph View Global
En Obsidian podrás ver:
- Cómo se conectan proyectos
- Patrones compartidos
- Decisiones relacionadas
- Cross-references automáticas

---

## 🚀 Cómo Usar Este Vault

### Sesión con Proyecto Existente
```
1. Abre: proyectos/[proyecto-name]/README.md
2. Lee: proyectos/[proyecto-name]/contexto/COPILOT_INSTRUCTIONS.md
   (incluye referencias a _global/)
3. Consulta: proyectos/[proyecto-name]/referencias/*
4. Trabaja normalmente
5. Actualiza: proyectos/[proyecto-name]/ESTADO.md
```

### Añadir Nuevo Proyecto
```
1. Copia: _global/templates/PROJECT_TEMPLATE.md
2. Coloca en: proyectos/nuevo-proyecto/README.md
3. Rellena template (automáticamente incluye referencias _global)
4. Crea carpetas: contexto/, referencias/, decisiones/
5. Agrega links a _global/ donde sea necesario
```

### Actualizar Instrucciones Globales
```
1. Abre: _global/copilot/INSTRUCCIONES_BASE.md
2. Edita
3. Automáticamente todos los proyectos verán cambios
   (vía cross-linking cuando lean COPILOT_INSTRUCTIONS.md)
4. Commit + push
```

---

## 🔗 Navegación Rápida

Convención de enlaces recomendada: [[_global/standards/WIKILINKS_CONVENCION]]

### Para Copilot (Global)
**Pasa al inicio de sesión:**
```
Contexto Global: _global/copilot/INSTRUCCIONES_BASE.md
Patrones: _global/standards/ARQUITECTURA_PATTERNS.md
Anti-patrones: _global/copilot/ANTI_PATRONES.md
```

### Para Copilot (Por-Proyecto)
**Pasa al pedir feature:**
```
Instrucciones: proyectos/[proyecto]/contexto/COPILOT_INSTRUCTIONS.md
(automáticamente linkeado a _global/)
```

### Para Búsqueda
```
Cmd+Shift+F → "label_id"           (encontrará en kpfm-etiquetas + otros)
Cmd+Shift+F → "pattern"             (encontrará patrones globales + locales)
Cmd+Shift+F → "RuleEngine"          (encontrará si existe en proyectos)
```

---

## 📊 Anatomía de un Proyecto

**Cada proyecto en `/proyectos/` tiene:**

### README.md
```markdown
# Proyecto: [Nombre]
- Stack: [Tech usado]
- Descripción: [Qué es]
- Ubicación repo: [Link]
- Owner: [Quién]
- Links: [[../../_global/...]]  ← Referencias globales
```

### contexto/COPILOT_INSTRUCTIONS.md
```markdown
# Instrucciones GitHub Copilot - [Proyecto]

## Contexto Global
Consulta también: [[../../../_global/copilot/INSTRUCCIONES_BASE]]

## Contexto Local
[Reglas específicas proyecto]

## Stack
- Global: Ver [[../../../_global/shared/STACK_GLOBAL]]
- Local: [Específicos Project]
```

### referencias/NOMENCLATURA.md
```markdown
# Nomenclatura - [Proyecto]

Términos comunes: [[../../../_global/standards/NOMENCLATURA_GLOBAL]]

## Específicos [Proyecto]
[Variables locales]
```

---

## 💡 Ejemplo: Agregar Proyecto Nuevo

### Paso 1: Copiar Template
```bash
cp -r _global/templates/PROJECT_TEMPLATE proyectos/nuevo-proyecto
```

### Paso 2: Actualizar README
```markdown
# Proyecto: Nuevo Proyecto

Stack: Python 3.9, FastAPI, SQLAlchemy
Descripción: API REST para [algo]
Ubicación: [link repo]

Links globales:
- [[../../_global/copilot/INSTRUCCIONES_BASE]]
- [[../../_global/standards/TESTING_GUIDELINES]]
```

### Paso 3: Actualizar COPILOT_INSTRUCTIONS.md
```markdown
# Instrucciones - Nuevo Proyecto

Sigue primero: [[../../../_global/copilot/INSTRUCCIONES_BASE]]

## Local - Nuevo Proyecto
[Reglas específicas]
```

### Paso 4: Usar Inmediatamente
```
1. Abre proyectos/nuevo-proyecto/contexto/COPILOT_INSTRUCTIONS.md
2. Pasa a Copilot
3. Copilot ve AMBAS instrucciones (global + local)
4. ¡Listo!
```

---

## 🎓 Ventajas de Esta Arquitectura

### Para Ti
- ✅ **Un solo lugar** para contexto de todos los proyectos
- ✅ **Búsqueda global** (`Cmd+Shift+F`) en todo vault
- ✅ **Reutilización**: patrones, templates, standards
- ✅ **Escalable**: agregar proyecto = copiar + rellenar
- ✅ **Graph view**: ver relaciones entre proyectos

### Para Copilot
- ✅ **Instrucciones globales** siempre disponibles
- ✅ **Instrucciones locales** especializadas
- ✅ **Coherencia**: mismos standards en todos los proyectos
- ✅ **Menos iteraciones**: contexto completo desde inicio

### Para Equipo (Futuro)
- ✅ **Onboarding**: "Lee `_global/copilot/`"
- ✅ **Documentación**: standardizada y reutilizable
- ✅ **Knowledge sharing**: decisiones documentadas
- ✅ **Auditoría**: histórico centralizado

---

## 📈 Crecimiento Esperado

### Mes 1 (Hoy)
```
_global/             (instrucciones base)
proyectos/
  └─ kpfm-etiquetas/ (proyecto 1)
```

### Mes 3
```
_global/             (instrucciones expandidas)
proyectos/
  ├─ kpfm-etiquetas/
  └─ otro-proyecto/  (proyecto 2)
```

### Mes 6
```
_global/             (standards completos)
proyectos/
  ├─ kpfm-etiquetas/
  ├─ otro-proyecto/
  ├─ proyecto-3/
  └─ proyecto-4/
```

---

## 🔒 Mantenimiento

### Actualización Instrucciones Globales
```bash
# Editar _global/copilot/INSTRUCCIONES_BASE.md
# Todos los proyectos auto-referencian vía links
# Solo commit + push
```

### Agregar Proyecto
```bash
# 1. Copiar template
# 2. Personalizar con links a _global/
# 3. Listo
```

### Cleanup
```bash
# Archivo viejo/descontinuado? → archivos-legacy/
# No borres, mueve aquí para referencia histórica
```

---

## 📞 Guía Rápida: Primeros Pasos

### HOY (10 minutos)
```
1. Abre: INDEX.md (este archivo)
2. Abre: SETUP.md (configuración)
3. Abre: proyectos/kpfm-etiquetas/README.md
```

### ESTA SEMANA
```
1. Lee: _global/copilot/INSTRUCCIONES_BASE.md
2. Lee: _global/standards/ARQUITECTURA_PATTERNS.md
3. Explora: graph view en Obsidian
```

### PRÓXIMO MES
```
1. Agrega nuevo proyecto
2. Copia template, personaliza
3. Implementa feature
4. Contribuye a _global/ si encuentras patrón reutilizable
```

---

## 🔗 Enlaces CRÍTICOS

### Empezar
- [[SETUP]] - Configuración inicial
- [[NAVIGATION]] - Cómo navegar

### Global (Copilot)
- [[_global/copilot/INSTRUCCIONES_BASE]]
- [[_global/copilot/PATRONES_GENERALES]]
- [[_global/standards/ARQUITECTURA_PATTERNS]]
- [[_global/copilot/PROMPT_AUDITORIA_REPOSITORIO]]

### Global (Repositorios)
- [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]

### Proyecto Actual
- [[kpfm/proyectos/kpfm-etiquetas/README]]
- [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]

### Templates
- [[_global/templates/DECISION_TEMPLATE]]
- [[_global/templates/PROJECT_TEMPLATE]]

---

## 💾 Versionado

```bash
# Este vault está en Git
cd ~/Documents/kpfm-obsidian-vault

# Commit cambios
git add .
git commit -m "feat: [descripción cambio]"
git push

# Ver histórico
git log --oneline
```

---

**Creada:** 29 Junio 2026
**Versión:** 2.0 - Multi-Proyecto Escalable
**Estado:** ✅ Listo para Usar + Expandir
**Próximo:** Lee [[SETUP]]




