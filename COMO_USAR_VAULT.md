# Guía: Cómo Usar este Vault Obsidian con GitHub Copilot

**Instrucciones paso a paso para máximo provecho del contexto centralizado**

---

## 🎯 Objetivo

Este vault está diseñado para que **GitHub Copilot siempre tenga el contexto actualizado del proyecto**, sin perder información entre sesiones.

---

## 📱 Instalación y Configuración

### Pasos 1-3: Descargar Obsidian
1. Ve a [obsidian.md](https://obsidian.md/)
2. Descarga para macOS
3. Abre la aplicación

### Paso 4: Abrir Vault Existente
1. En Obsidian, click en **"Open folder as vault"**
2. Navega a: `/Users/t022458/Documents/kpfm-obsidian-vault/`
3. Click en **"Open"**

### Paso 5: Verificar Vault
Deberías ver en la barra izquierda:
```
📁 kpfm-obsidian-vault
  ├── 📁 proyecto/
  ├── 📁 contexto/
  ├── 📁 tareas/
  ├── 📁 decisiones/
  ├── 📁 referencias/
  └── 📄 README.md
```

---

## 🔄 Workflow Recomendado

### 1️⃣ ANTES de Iniciar una Sessión (Importantísimo)

```bash
# En PyCharm o terminal:
# 1. Abre el vault Obsidian
# 2. Lee estos archivos EN ORDEN:
```

**Checklist de lectura previa (5 min):**
- [ ] `/contexto/CONTEXTO_ACTUAL.md` - Dónde estamos
- [ ] `/contexto/COPILOT_INSTRUCTIONS.md` - Qué respetar
- [ ] `/contexto/CAMBIOS_RECIENTES.md` - Qué se hizo último

**Máximo:** Invierte 5 minutos en esto. Es importante.

### 2️⃣ DURANTE la Sesión (Mientras Codeas)

**Si necesitas:**
- **Recordar nomenclatura** → `/referencias/NOMENCLATURA.md`
- **Sintaxis Spark** → `/referencias/SPARK_FUNCTIONS.md`
- **Entender una decisión** → `/decisiones/*/`
- **Ver arquitectura** → `/proyecto/ARQUITECTURA.md`

**Copilot:**
Cuando le pidas a Copilot que haga algo, menciónale (opcional pero útil):
```
"Implementa [feature] siguiendo el contexto de /contexto/COPILOT_INSTRUCTIONS.md"
```

### 3️⃣ DESPUÉS de Terminar (Actualizar Vault)

**Checklist actualización (10 min):**
1. [ ] ¿Implementé feature nueva? 
   - Agregar línea a `/contexto/CAMBIOS_RECIENTES.md`
   - Crear archivo en `/decisiones/` si fue decisión importante

2. [ ] ¿Cambié nomenclatura o stack?
   - Actualizar `/referencias/NOMENCLATURA.md`
   
3. [ ] ¿Cambié configuración?
   - Actualizar `/referencias/CONFIGURACION_KEYS.md`

4. [ ] ¿Aprendí patrón nuevo?
   - Agregar a `/referencias/SPARK_FUNCTIONS.md`

---

## 📂 Estructura Explicada

### `/proyecto/`
Documentación ESTÁTICA del proyecto (casi no cambia)
```
ARQUITECTURA.md         → Diagrama: qué componentes hablan con quién
STACK_TECNOLOGICO.md    → Python 3.11, PySpark, Pydantic, etc.
ENTRY_POINTS.md         → `worker.py`, `app.py` explicados
```

**Cuándo actualizar:** Casi nunca. Solo si cambias tech stack.

### `/contexto/`
Estado ACTUAL y dinámico del proyecto (se actualiza cada sesión)
```
CONTEXTO_ACTUAL.md              → Features completados, estado general
COPILOT_INSTRUCTIONS.md         → Reglas que Copilot DEBE respetar
CAMBIOS_RECIENTES.md            → Histórico de sesiones
```

**Cuándo actualizar:** Después de cada sesión productiva.

### `/decisiones/`
Explicación del "por qué" detrás de decisiones importantes
```
TAG_MERGING_LOGIC.md    → Por qué implementamos merge de etiquetas
SCHEMA_EVOLUTION.md     → Por qué agregamos gf_pfm_tag_updated_date
```

**Cuándo actualizar:** Cuando hagas decisión arquitectónica importante.

### `/referencias/`
Búsquedas rápidas mientras codeas (copy-paste friendly)
```
NOMENCLATURA.md         → Variables del proyecto (label_id, etc)
SPARK_FUNCTIONS.md      → Funciones Spark con ejemplos
CONFIGURACION_KEYS.md   → Claves HOCON y qué hacen
```

**Cuándo actualizar:** Cuando descubras patrón nuevo o función Spark.

### `/tareas/`
Tracking de work items (opcional, para equipos)
```
BACKLOG.md              → Features pendientes
EN_PROGRESO.md          → Tareas activas
COMPLETADAS.md          → Features finalizadas
```

**Cuándo actualizar:** Si trabajas en equipo.

---

## 🔗 Cómo Vincular Archivos (Links Internos)

Obsidian permite crear vínculos internos. Ejemplo:

```markdown
# En CAMBIOS_RECIENTES.md
Ver la decisión completa en [[TAG_MERGING_LOGIC]]

# O:
Consulta [[COPILOT_INSTRUCTIONS#Regla engine y etiquetas | RuleEngine rules]]
```

**Para crear link:**
1. Escribe: `[[nombre_archivo]]`
2. Obsidian lo auto-completa
3. Click para navegar

---

## 🎨 Obsidian: Features Útiles

### 1. Graph View (Visualizar conexiones)
- Click en **"Open graph view"** (ícono de puntos)
- Verás conexiones entre archivos
- Click en nodo para saltar a archivo

### 2. Search (Buscar texto)
- Keyboard: `Cmd + P` (open file)
- Keyboard: `Cmd + Shift + F` (search text)
- Busca: "updated_date" → encuentra todas referencias

### 3. Quick Switcher
- `Cmd + O` → buscar archivo
- Empieza a escribir nombre
- Click o Enter para abrir

### 4. Backlinks
- Abre cualquier archivo
- Panel derecho mostrará archivos que linkan a éste

---

## 💾 Backup y Sincronización

### Opción 1: Manual (Recomendado Ahora)
```bash
# Cada vez que cambies vault:
cd ~/Documents/kpfm-obsidian-vault
git add .
git commit -m "Update context: [qué cambió]"
git push
```

### Opción 2: Obsidian Sync (Premium)
- $8/mes
- Sincroniza automáticamente entre dispositivos
- Versionado automático

### Opción 3: iCloud Drive / Dropbox
- Carpeta `/Documents/kpfm-obsidian-vault/` en sync
- ⚠️ Cuidado con conflictos

---

## 🔐 Seguridad y Privacidad

**Nota:** Obsidian es local, tus archivos no se suben a servidores (a menos que uses Sync).

**Lo que está en el vault:**
- ✅ Información técnica pública (arquitectura, patrones)
- ✅ Instrucciones para Copilot
- ✅ Decisiones de diseño
- ❌ NO secrets, passwords, tokens

**Si necesitas guardar secrets:**
- Usa `.env.local` (no versionar)
- Usa KeyChain de macOS
- NO incluyas en Obsidian

---

## 📝 Template: Cómo Agregar Archivo Nuevo

### Si es decisión arquitectónica:
**Crear:** `/decisiones/NOMBRE_DECISION.md`

```markdown
# Decisión: [Nombre]

**Fecha:** [Hoy]
**Estado:** 🔶 En progreso / ✅ Implementado
**Autor:** [Tu nombre]

## 🎯 Problema
[Qué problema resuelve]

## 💡 Solución
[Cómo lo resolviste]

## ✅ Evidencia
[Tests, commits, files changed]

## 📊 Impacto
[Antes vs después]

---

**Ref:** [[CONTEXTO_ACTUAL#Cambios Recientes]]
```

### Si es feature nuevo:
**Actualizar:** `/contexto/CAMBIOS_RECIENTES.md`

Agregar sección:
```markdown
## Sesión [FECHA] - [NOMBRE FEATURE]

### ✅ Completado
- [ ] Implementación
- [ ] Tests
- [ ] Documentación

### 📝 Archivos Cambiados
- `file1.py` (líneas X-Y)
- `file2.py` (líneas X-Y)

### 🔗 Vinculado a
[[TAG_MERGING_LOGIC]]
```

---

## 🚀 Pro Tips

### ✅ Hacer
1. ✅ Leer contexto ANTES de trabajar (5 min)
2. ✅ Links internos: `[[archivo]]` para navegar
3. ✅ Búsqueda global: `Cmd+Shift+F` para encontrar cosas
4. ✅ Actualizar después de cada sesión (10 min)
5. ✅ Template de decisión para decisiones importantes

### ❌ No hacer
1. ❌ Dejar mucho tiempo sin actualizar
2. ❌ Guardar secrets o passwords
3. ❌ Crear archivos sin estructura (hazlos en `/proyecto/` o `/contexto/`)
4. ❌ Dejar links rotos: revisar antes de commitear

---

## 🔄 Ciclo Recomendado (Semanal)

### Lunes (Inicio de semana)
```
1. Abrir Obsidian
2. Leer /contexto/CAMBIOS_RECIENTES.md (qué pasó)
3. Leer /contexto/COPILOT_INSTRUCTIONS.md (reglas vigentes)
4. Revisar /decisiones/ si hay pendientes
```

### Viernes (Fin de semana)
```
1. Actualizar /contexto/CAMBIOS_RECIENTES.md
2. Crear /decisiones/ALGO_IMPORTANTE.md si aplica
3. Revisar /referencias/ (¿aprendí algo nuevo?)
4. Commit y push a git
```

---

## 📞 Troubleshooting

### Problema: "¿Dónde pongo archivo X?"

**Regla:** 
- ¿Es decisión importante? → `/decisiones/`
- ¿Es referencia técnica? → `/referencias/`
- ¿Es estado/contexto? → `/contexto/`
- ¿Es arquitectura? → `/proyecto/`
- ¿Es tarea? → `/tareas/`

### Problema: "Links rotos"
```
Solución:
1. Search: Cmd+Shift+F → "!!" (links rotos)
2. O: Panel derecho → "Unlinked mentions"
3. Fix o borra
```

### Problema: "Obsidian no encuentra archivo"
```
Solución:
1. Verifica en archivo manager: ~/Documents/kpfm-obsidian-vault/
2. Si no está, crea manualmente
3. Cierra y reabre Obsidian
```

---

## 🎓 Próxima Sesión: Checklist Rápido

```markdown
- [ ] Abrir Obsidian
- [ ] Leer CONTEXTO_ACTUAL.md (2 min)
- [ ] Leer COPILOT_INSTRUCTIONS.md (3 min)
- [ ] Empezar a trabajar
- [ ] Actualizar al terminar
- [ ] Commit y push
```

---

**Versión:** 1.0
**Creada:** 29 Junio 2026
**Mantén este archivo como referencia siempre**

