# 🗺️ NAVIGATION - Cómo Navegar el Vault Multi-Proyecto

**Guía de navegación para entender la estructura y encontrar lo que necesitas**

---

## 🎯 Dónde Ir Por Situación

### Situación A: "Es mi primer día, debo entender el vault"

```
1. Reads: INDEX.md              (2 min)  ← Ahora estás aquí
2. Read:  SETUP.md              (5 min)  ← Configuración
3. Read:  NAVIGATION.md         (este)   (3 min)
4. Open:  _global/copilot/INSTRUCCIONES_BASE.md  (5 min)
5. Open:  proyectos/kpfm-etiquetas/README.md      (3 min)

⏱️ Total: 18 minutos de onboarding
```

### Situación B: "Voy a trabajar en kpfm-etiquetas"

```
1. Open:  proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS.md
2. Read:  (2 min - incluye links a _global/)
3. Consult: proyectos/kpfm-etiquetas/referencias/* (mientras codeas)
4. End of day: Update ESTADO.md en proyecto
```

### Situación C: "Voy a empezar un proyecto nuevo"

```
1. Copy:  _global/templates/PROJECT_TEMPLATE.md
2. Paste: proyectos/[nuevo]/README.md
3. Fill:  Template (incluye links a _global/)
4. Create: contexto/, referencias/, decisiones/
5. Add links to _global/ en COPILOT_INSTRUCTIONS.md
6. Start working
```

### Situación D: "Necesito una regla global que afecte TODOS años proyectos"

```
1. Open:  _global/copilot/INSTRUCCIONES_BASE.md
2. Edit:  Agrega la regla
3. Commit y push
4. Todos los proyectos auto-referencian vía links (sin cambios manuales)
```

### Situación E: "¿Dónde está [algo]?"

```
1. Cmd+O              → Busca por nombre archivo
2. Cmd+Shift+F        → Busca por texto (cualquier archivo)
3. Graph view         → Click ícono red, explora visualmente
4. Este documento     → Consulta tabla abajo
```

---

## 📍 Tabla de Contenidos - Rápida

| Quiero... | Voy a... |
|-----------|----------|
| Entender la estructura | [[INDEX]] |
| Configurar Obsidian | [[SETUP]] |
| Navegar el vault | [[NAVIGATION]] ← Aquí |
| Instrucciones Copilot GLOBALES | [[_global/copilot/INSTRUCCIONES_BASE]] |
| Patrones arquitectónicos | [[_global/standards/ARQUITECTURA_PATTERNS]] |
| Nomenclatura compartida | [[_global/standards/NOMENCLATURA_GLOBAL]] |
| Templates reutilizables | [[_global/templates/]] (carpeta) |
| Info proyecto kpfm-etiquetas | [[kpfm/proyectos/kpfm-etiquetas/README]] |
| Instrucciones Copilot (kpfm) | [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]] |
| Estado actual (kpfm) | [[kpfm/proyectos/kpfm-etiquetas/ESTADO]] |
| Cambios recientes (kpfm) | [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]] |
| Decisiones kpfm | [[kpfm/proyectos/kpfm-etiquetas/decisiones/]] (carpeta) |
| Crear nuevo proyecto | [[_global/templates/PROJECT_TEMPLATE]] |
| Git/Backup | [[SETUP#paso-7-configurar-git]] |

---

## 🧭 Navegación por Localización

### Nivel 1: Raíz (Root)
```
📁 ~/Documents/kpfm-obsidian-vault/
├─ INDEX.md                    ← START HERE  
├─ SETUP.md                    ← Configuración
├─ NAVIGATION.md               ← Este archivo
└─ [carpetas abajo]
```

**Qué hacer:**
- Todos comienzan aquí
- Léelos en orden: INDEX → SETUP → NAVIGATION

---

### Nivel 2: _global/ (Instrucciones y Standards Compartidos)

```
📁 _global/
│
├─ 📋 copilot/
│  ├─ INSTRUCCIONES_BASE.md         ← LEE PRIMERO (reglas globales)
│  ├─ PATRONES_GENERALES.md         ← Patrones reutilizables
│  ├─ ANTI_PATRONES.md              ← Lo que NUNCA hacer
│  └─ README_COPILOT.md             ← Guía integración Copilot
│
├─ 📐 standards/
│  ├─ NOMENCLATURA_GLOBAL.md        ← Términos comunes todos proyectos
│  ├─ ARQUITECTURA_PATTERNS.md       ← Patrones arquitectónicos
│  ├─ TESTING_GUIDELINES.md         ← Estándares testing
│  └─ DOCUMENTATION_TEMPLATES.md    ← Cómo documentar
│
├─ 🎨 templates/
│  ├─ PROJECT_TEMPLATE.md           ← Copiar para nuevo proyecto
│  ├─ DECISION_TEMPLATE.md          ← Copiar para decisión
│  ├─ FEATURE_TEMPLATE.md           ← Copiar para feature
│  └─ BUG_FIX_TEMPLATE.md           ← Copiar para bug fix
│
└─ 🔗 shared/
   ├─ STACK_GLOBAL.md              ← Tech stacks comunes
   ├─ HERRAMIENTAS.md              ← Tools que usamos
   └─ LINKS_EXTERNOS.md            ← Referencias externas
```

**Qué hacer acá:**
- **Leer:** INSTRUCCIONES_BASE.md (día 1)
- **Consultar:** PATRONES_GENERALES.md mientras codeas
- **Copiar:** Templates cuando agregues proyecto/decisión
- **Actualizar:** Si descubres patrón que TODOS proyectos usen

**Cuándo vuelvo aquí:**
- Cada nueva sesión (refresco reglas globales)
- Cuando agriego nuevo proyecto
- Cuando descubro patrón reutilizable

---

### Nivel 3: proyectos/ (Proyectos Específicos)

```
📁 proyectos/
│
├─ 📁 kpfm-etiquetas/              ← PROYECTO ACTUAL
│  ├─ 🚀 README.md                 ← Overview proyecto
│  ├─ 📊 ESTADO.md                 ← Estado actual (update cada sesión)
│  │
│  ├─ 📋 contexto/
│  │  ├─ COPILOT_INSTRUCTIONS.md   ← Reglas proyecto + global links
│  │  ├─ ARQUITECTURA.md           ← Arquitectura proyecto
│  │  ├─ CAMBIOS_RECIENTES.md      ← Histórico sesiones
│  │  └─ ROADMAP.md                ← Planes futuros
│  │
│  ├─ 📚 referencias/
│  │  ├─ NOMENCLATURA.md           ← Variables específicas
│  │  ├─ SPARK_FUNCTIONS.md        ← Funciones Spark
│  │  ├─ CONFIGURACION.md          ← Config específica
│  │  └─ SNIPPETS.md               ← Code snippets útiles
│  │
│  ├─ 🏗️ decisiones/
│  │  ├─ TAG_MERGING_LOGIC.md      ← Decisión 1
│  │  └─ [decisión-n].md           ← Decisión N
│  │
│  └─ 📖 docs/
│     └─ README.md                 ← Links a documentación proyecto
│
├─ 📁 otro-proyecto/               ← PROYECTO 2 (cuando lo agregues)
│  └─ [misma estructura]
│
└─ 📁 proyecto-n/                  ← PROYECTO N
   └─ [misma estructura]
```

**Qué hacer acá:**
- **Antes de sesión:** Leer `ESTADO.md` (dónde estamos)
- **Inicio sesión:** Leer `contexto/COPILOT_INSTRUCTIONS.md`
- **Mientras codeas:** Consultar `referencias/*`
- **Fin sesión:** Actualizar `ESTADO.md`

**Structure Pattern (reutilizable cada proyecto):**
```
proyecto/
├─ README.md                      ← Qué es
├─ ESTADO.md                      ← Dónde estamos (update cada sesión)
├─ contexto/
│  ├─ COPILOT_INSTRUCTIONS.md    ← Reglas (+ links a _global/)
│  ├─ ARQUITECTURA.md
│  ├─ CAMBIOS_RECIENTES.md
│  └─ ROADMAP.md
├─ referencias/                   ← Copy specific project
│  ├─ NOMENCLATURA.md
│  ├─ SPARK_FUNCTIONS.md
│  ├─ CONFIGURACION.md
│  └─ SNIPPETS.md
├─ decisiones/
│  └─ [decisiones].md
└─ docs/
   └─ README.md                   ← Link a /docs real del repo
```

---

## 🔗 Cross-Linking: Cómo Funcionan los Links

Norma oficial de este vault: [[_global/standards/WIKILINKS_CONVENCION]].

### Link Interno (Entre archivos Obsidian)
```markdown
Sintaxis: [[archivo]]
Ejemplo:  [[INSTRUCCIONES_BASE]]

En Obsidian: Click → abre archivo
En GitHub:  Muestra como código
```

### Link a Carpeta
```markdown
Sintaxis: [[carpeta/]]
Ejemplo:  [[_global/templates/]]

En Obsidian: Muestra contenidos carpeta
```

### Link con Alias
```markdown
Sintaxis: [[archivo|mostrar texto]]
Ejemplo:  [[INSTRUCCIONES_BASE|Instrucciones Copilot]]

En Obsidian: "Instrucciones Copilot" es clickeable → abre archivo
```

### Link a Sección
```markdown
Sintaxis: [[archivo#sección]]
Ejemplo:  [[SETUP#paso-7-configurar-git]]

En Obsidian: Jump directo a sección
```

---

## 📂 Anatomía: "¿A Dónde Va?"

### Nuevo Archivo Relacionado a Reglas Globales?
```
Va a: _global/copilot/ o _global/standards/
Razón: Reutilizable por TODO proyecto
```

### Nuevo Archivo Específico Proyecto kpfm?
```
Va a: proyectos/kpfm-etiquetas/contexto/ o referencias/
Razón: Solo aplica proyecto
```

### Nuevo Proyecto?
```
1. Copia: _global/templates/PROJECT_TEMPLATE.md
2. Crea carpeta: proyectos/nuevo-proyecto/
3. Pega template como README.md
4. Personaliza y agrega links a _global/
```

### Documentación Feature Completa?
```
Va a: proyectos/[proyecto]/decisiones/NOMBRE_FEATURE.md
Copia template: _global/templates/DECISION_TEMPLATE.md
Link desde: contexto/CAMBIOS_RECIENTES.md
```

---

## 🧠 Flujo Típico: "¿Qué Hago Ahora?"

### Escenario: Inicio Día - Empezar Sesión
```
1. Abre Obsidian
2. Bookmark click: INSTRUCCIONES_BASE.md (read 3 min)
3. Bookmark click: proyectos/kpfm-etiquetas/ESTADO.md (read 2 min)
4. Bookmark click: proyectos/kpfm-etiquetas/COPILOT_INSTRUCTIONS.md (read 2 min)
5. ¡Listo! Tienes contexto completo
6. Empieza a codear
```

### Escenario: Necesito Patrón Spark
```
1. Cmd+Shift+F
2. Busca "f.transform" o "f.when"
3. Consulta: referencias/SPARK_FUNCTIONS.md en proyecto
4. Copiar snippet
```

### Escenario: Terminé Feature
```
1. Abre: contexto/CAMBIOS_RECIENTES.md
2. Agrega: ## [Fecha] - [Feature]
3. Commit + push
4. Listo
```

### Escenario: Necesito Crear Decisión
```
1. Copia: _global/templates/DECISION_TEMPLATE.md
2. Crea: proyectos/[proyecto]/decisiones/NOMBRE.md
3. Rellena template
4. Link desde CAMBIOS_RECIENTES.md
5. Commit + push
```

---

## 🚀 Keyboard Shortcuts Esenciales

| Atajo | Qué Hace |
|-------|----------|
| `Cmd+O` | Buscar archivo por nombre |
| `Cmd+Shift+F` | Buscar texto EN TODO VAULT |
| `Cmd+Shift+B` | Crear/editar bookmarks |
| `Cmd+Click` | Abrir link en nueva pestaña |
| `Cmd+[` o Backspace | Volver atrás |
| `Cmd+]` | Ir adelante |
| `Tab` | Expandir/collapse secciones en outline |

---

## 📊 Graph View: Entender Conexiones

### Cómo Abrir
```
Click ícono red (panel izquierdo) → Graph View se abre
```

### Cómo Usar
```
1. Zoom: Mouse wheel o trackpad pinch
2. Mover: Click + drag
3. Click nodo: Abre archivo
4. Filters: Filtra por tipo (global, proyecto, etc)
5. Force-directed: Mueves nodos, se acomodan automáticamente
```

### Interpretar
```
Línea gruesa = muchos links
Línea fina = pocos links
Nodo grande = muchas referencias a este
Nodo aislado = sin links (posible candidato para linkar)
```

---

## 📈 Growth: Cuando Agregues Proyectos

### Proyecto Nuevo #1
```
1. Copy: _global/templates/PROJECT_TEMPLATE.md
2. Create folder: proyectos/proyecto-1/
3. Paste as README.md
4. Create subfolders: contexto/, referencias/, decisiones/
5. Llena COPILOT_INSTRUCTIONS.md con links a _global/
6. ¡Listo!

Result:
proyectos/
├─ kpfm-etiquetas/
└─ proyecto-1/          ← Nuevo
```

### Proyecto Nuevo #2
```
Same process
Reutiliza template de _global/templates/

Result:
proyectos/
├─ kpfm-etiquetas/
├─ proyecto-1/
└─ proyecto-2/          ← Nuevo
```

### Cuando Tengas 5+ Proyectos
```
1. _global/ habrá crecido (más standards, patrones)
2. Puedes crear _global/patterns-by-tech/ (FrontEnd, Backend, etc)
3. Cross-linking es más importante → graph view muestra "hub" de conocimiento
4. Búsqueda global (`Cmd+Shift+F`) es CRÍTICA
```

---

## 🎓 Reglas de Oro

### ✅ HACER
- ✅ Usar links internos `[[]]` para conectar ideas
- ✅ Citar secciones: `[[archivo#sección]]`
- ✅ Update `ESTADO.md` cada sesión (2 minutos)
- ✅ Request rules en `_global/` si afecta todos proyectos
- ✅ Commit regularmente: al fin cada sesión

### ❌ NO HACER
- ❌ Copiar contenido entre proyectos (link en su lugar)
- ❌ Editar `_global/` sin verificar que no rompa proyectos
- ❌ Guardar con nombres vagos ("temp", "nuevo", "test")
- ❌ Dejar links rotos
- ❌ Ignorar 1+ semana sin backup (git)

---

## 📞 Troubleshooting: "¿Dónde Va X?"

| Pregunta | Respuesta |
|----------|-----------|
| ¿Qué archivo leer primero? | INDEX.md |
| ¿Instrucciones Copilot globales? | _global/copilot/INSTRUCCIONES_BASE.md |
| ¿Instrucciones Copilot proyecto? | proyectos/[proyecto]/contexto/COPILOT_INSTRUCTIONS.md |
| ¿Estado actual proyecto? | proyectos/[proyecto]/ESTADO.md |
| ¿Decisiones proyecto? | proyectos/[proyecto]/decisiones/ (carpeta) |
| ¿Patrones reutilizables? | _global/standards/ARQUITECTURA_PATTERNS.md |
| ¿Crear nuevo proyecto? | _global/templates/PROJECT_TEMPLATE.md |
| ¿Documentar decisión? | _global/templates/DECISION_TEMPLATE.md |
| ¿Buscar en TODO vault? | Cmd+Shift+F |

---

## 🔗 Quick Links (Copy This)

```markdown
## Críticos
- [[INDEX]]
- [[SETUP]]
- [[_global/copilot/INSTRUCCIONES_BASE]]

## Actual Proyecto
- [[kpfm/proyectos/kpfm-etiquetas/README]]
- [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]

## Growth
- [[_global/templates/PROJECT_TEMPLATE]]
- [[_global/copilot/PATRONES_GENERALES]]
```

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026
**Estado:** ✅ Listo para Navegar
**Próximo:** Abre [[INDEX]] o bookmark tus archivos críticos



