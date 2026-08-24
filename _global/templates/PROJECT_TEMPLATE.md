# 🚀 [NOMBRE_PROYECTO] - README

**Copia este template a `proyectos/nuevo-proyecto/README.md` y personaliza**

---

## 📋 Información Básica

**Nombre Proyecto:** [Tu nombre proyecto]
**Stack:** [Python 3.11, PySpark, FastAPI, etc.]
**Descripción:** Una línea: qué problema resuelve
**Ubicación Repo:** [link a GitHub/GitLab]
**Owner:** [Tu nombre]
**Vault:** Este archivo en `proyectos/[nombre]/`

---

## 🎯 Visión General

### Qué Es
[Párrafo 1: Qué es el proyecto, para quién]

### Problema que Resuelve
[Párrafo 2: Antes vs después, impacto]

### Tecnología
- **Lenguaje Principal:** [Python 3.11]
- **Framework/Engine:** [PySpark, FastAPI, etc.]
- **Base Datos:** [PostgreSQL, Spark tables, etc.]
- **Deploy:** [Docker, Kubernetes, Lambda, etc.]

---

## 📂 Estructura Este Proyecto

```
proyectos/[proyecto-name]/
├─ README.md                      ← Tú estás aquí
├─ ESTADO.md                      ← Estado actual (update cada sesión)
├─ contexto/
│  ├─ COPILOT_INSTRUCTIONS.md    ← Reglas proyecto (linkeadas a _global/)
│  ├─ ARQUITECTURA.md             ← Cómo está diseñado
│  ├─ CAMBIOS_RECIENTES.md        ← Sesión anterior
│  └─ ROADMAP.md                  ← Qué viene
├─ referencias/
│  ├─ NOMENCLATURA.md            ← Variables específicas
│  ├─ [TECHNOLOGY]_FUNCTIONS.md  ← Funciones usadas frecuente
│  ├─ CONFIGURACION.md            ← Config keys
│  └─ SNIPPETS.md                 ← Code snippets útiles
├─ decisiones/
│  ├─ [DECISION_1].md            ← Decisión arquitectónica
│  └─ [DECISION_N].md
└─ docs/
   └─ README.md                   ← Links a documentación real
```

**Documentación en Obsidian:**
- Reglas Copilot: `contexto/COPILOT_INSTRUCTIONS.md`
- Estado actual: `ESTADO.md` (update cada sesión)
- Decisiones: `decisiones/[decision-name].md`

**Documentación Proyecto Real:**
- Code repo: `[link]`
- Docs folder: `/docs` en proyecto real
- Wiki (si existe): `[link]`

---

## 🚀 Quick Start

### 1. Requisitos
```bash
# Versiones requeridas
Python 3.11+
PySpark 3.x (si aplica)
PostgreSQL 13+ (si aplica)
[Otras herramientas...]
```

### 2. Setup Local
```bash
# Clonar repo
git clone [repo-url]
cd [proyecto]

# Crear virtualenv
python -m venv venv
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt
pip install -r requirements_dev.txt   # Para desarrollo

# Setup base de datos (si aplica)
[comando específico proyecto]
```

### 3. Ejecutar
```bash
# Prueba rápida
python -m pytest

# Ejecutar aplicación
python main.py

# Ver logs
tail -f logs/app.log
```

### 4. Documentación
- Arquitectura: `contexto/ARQUITECTURA.md`
- Configuración: `referencias/CONFIGURACION.md`
- API: ver `/docs` en repo real

---

## 🏗️ Arquitectura (30 segundos)

**Flujo General:**
```
[Entrada] 
  ↓
[Validación]
  ↓
[Lógica Core]
  ↓
[Salida]
```

**Componentes Principales:**
- **A:** [Descripción corta]
- **B:** [Descripción corta]
- **C:** [Descripción corta]

**Para más detalle:** Ver `contexto/ARQUITECTURA.md`

---

## 🧪 Tests

### Ejecutar
```bash
pytest -q                          # Quick run
pytest -v                          # Verbose
pytest tests/test_io/ -k keyword   # Filter specific
```

### Coverage
```bash
pytest --cov=src --cov-report=html
open htmlcov/index.html
```

### TDD (Test-Driven Development)
1. Write test first (RED)
2. Implement minimal code (GREEN)
3. Refactor (REFACTOR)

---

## 📝 Reglas Este Proyecto

**Antes de empezar, leer:**

### Instrucciones Copilot
- Global: [[_global/copilot/INSTRUCCIONES_BASE]]
- Específicas proyecto: `contexto/COPILOT_INSTRUCTIONS.md`

### Estándares
- Global: [[_global/standards/ARQUITECTURA_PATTERNS]]
- Específicos proyecto: `referencias/NOMENCLATURA.md`

### Anti-Patrones
- [[_global/copilot/ANTI_PATRONES]]

### Operacion Obsidian (obligatoria)
- Mantener `contexto/COPILOT_INSTRUCTIONS.md` dentro del proyecto y enriquecerlo con reglas locales.
- Antes de implementar: revisar `ESTADO.md` y `contexto/CAMBIOS_RECIENTES.md`.
- Si falta documentacion del proyecto: crearla en este namespace antes de continuar.
- Al finalizar: actualizar `ESTADO.md` y `contexto/CAMBIOS_RECIENTES.md`.

---

## 📊 Estado Actual

**Para saber dónde estamos:**
→ Abre `ESTADO.md` (update cada sesión)

---

## 🔄 Workflow Típico: "¿Qué hago ahora?"

### Inicio Sesión (5 minutos)
```
1. Abre: ESTADO.md
2. Lee: contexto/COPILOT_INSTRUCTIONS.md
3. Consulta: referencias/* si necesitas
4. ¡Empieza!
```

### Durante Sesión
```
1. Crea branch: feature/[nombre]
2. Escribe test FIRST
3. Implementa código
4. Verifica tests pasan
5. Hace commit claro
```

### Fin Sesión
```
1. Abre: ESTADO.md
2. Agrega: qué hiciste
3. Commit: git commit -m "Session: [qué]"
4. Push: git push origin [branch]
```

---

## 📚 Decisiones Importantes

Documentadas en `decisiones/`:

### Decisión 1: [Nombre]
- Archivo: `decisiones/NOMBRE.md`
- Razón: [Por qué la tomamos]
- Impacto: [Qué cambió]

### Decisión 2...

---

## 🔗 Cross-Project References

**Si este proyecto usa código de otro:**
```
Dependencia: [[../../proyectos/otro-proyecto/]]
Versión requerida: X.Y.Z
API contract: [link a docs]
```

---

## 📞 Contacto / Owner

**Owner:** [Nombre/GitHub]
**Reportar bugs:** [Cómo]
**Preguntas:** [Slack, email, etc.]

---

## 🎓 Documentación Completa

| Qué Quiero | Dónde |
|-----------|-------|
| Entender arquitectura | `contexto/ARQUITECTURA.md` |
| Saber qué cambió | `contexto/CAMBIOS_RECIENTES.md` |
| Copilot rules | `contexto/COPILOT_INSTRUCTIONS.md` |
| Variables usadas | `referencias/NOMENCLATURA.md` |
| Funciones frecuentes | `referencias/[TECH]_FUNCTIONS.md` |
| Config keys | `referencias/CONFIGURACION.md` |
| Code snippets | `referencias/SNIPPETS.md` |
| Decisiones importantes | `decisiones/` (carpeta) |

---

## 🚀 Próximos Pasos (Para Nuevo Colaborador)

1. Clone repo
2. Setup local (ver arriba)
3. Read `ESTADO.md` (dónde estamos)
4. Read `contexto/COPILOT_INSTRUCTIONS.md` (reglas)
5. Lee `contexto/ARQUITECTURA.md` (cómo funciona)
6. ¡Empieza con feature pequeña!

---

## 💡 Tips Obsidian

```markdown
# Navegar
Cmd+O              → Buscar archivo
Cmd+Shift+F        → Buscar texto
Cmd+Click          → Abrir link en nueva tab

# Links
[[ESTADO]]      → Link a archivo
[[archivo#sección]]  → Link a sección específica
```

---

## 🔗 Vault Obsidian

**Ubicación:** `~/Documents/kpfm-obsidian-vault/`

**Navegación:**
- Start: [[INDEX]]
- Global copilot rules: [[_global/copilot/INSTRUCCIONES_BASE]]
- Este proyecto: Estás viendo

---

## ⚙️ Configuración

### ENV Variables
```bash
# .env.local (no versionar)
DATABASE_URL=...
API_KEY=...
[Otros...]
```

### Config Files
```
resources/application.conf
resources/config/[micro-service].conf
[Otros...]
```

Para detalle: Ver `referencias/CONFIGURACION.md`

---

## 🎯  Contribuyendo

### Pasos
1. Fork o crear branch
2. Escribe test
3. Implementa
4. Verifica tests pasan
5. Create PR
6. Review + Merge

### Pull Request Checklist
- [ ] Tests pass: `pytest -q`
- [ ] No linting errors: `[project-specific]`
- [ ] Docstrings added
- [ ] Updated ESTADO.md
- [ ] Commits are clean

---

## 📖 Recursos Externos

- [Link a docs proyecto](/)
- [Link a repo GitHub](/)
- [Link a boards/issues](/)
- [[_global/shared/HERRAMIENTAS | Global Tools]]

---

**Creado:** [Fecha]
**Maintained by:** [Tu nombre]
**Vault:** [[INDEX | Vault Obsidian]]
**Siguiente:** Lee [[ESTADO]] para saber dónde estamos




