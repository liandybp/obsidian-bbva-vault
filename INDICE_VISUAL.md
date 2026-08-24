# 📊 Índice Visual - Tu Vault Obsidian

**Mapa rápido de todos los archivos y dónde usarlos**

---

## 🗺️ Estructura Completa

```
📁 kpfm-obsidian-vault/
│
├─ 🚀 START_HERE.md                          ← LEER PRIMERO (5 min)
├─ QUICK_START.md                            ← Setup rápido (5 min)
├─ COMO_USAR_VAULT.md                        ← Guía completa (20 min)
├─ COPILOT_OBSIDIAN_INTEGRATION.md          ← Integración Copilot
├─ README.md                                 ← Visión general
│
├─ 📋 contexto/                              ← ESTADO ACTUAL (actualizar cada sesión)
│  ├─ CONTEXTO_ACTUAL.md                    ← Dónde estamos HOY
│  ├─ COPILOT_INSTRUCTIONS.md               ← Reglas que respetar (crítico)
│  └─ CAMBIOS_RECIENTES.md                  ← Qué se hizo (histórico)
│
├─ 📚 referencias/                           ← BÚSQUEDAS RÁPIDAS (mientras codeas)
│  ├─ NOMENCLATURA.md                       ← label_id, gf_pfm_tag_*, etc.
│  ├─ SPARK_FUNCTIONS.md                    ← f.transform(), f.when(), etc.
│  └─ CONFIGURACION_KEYS.md                 ← UNIVERSE_COUNTRY_ID, LABELS_PARAM, etc.
│
├─ 🏗️ decisiones/                            ← DECISIONES IMPORTANTES (por qué)
│  ├─ TAG_MERGING_LOGIC.md                  ← La decisión principal
│  └─ TEMPLATE_DECISION.md                  ← Template para nuevas decisiones
│
├─ 📋 tareas/                                ← TRACKING (opcional)
│  ├─ BACKLOG.md                            ← Features pendientes
│  ├─ EN_PROGRESO.md                        ← Tareas activas
│  └─ COMPLETADAS.md                        ← Features finalizadas
│
└─ 🏛️ proyecto/                              ← DOCUMENTACIÓN ESTÁTICA
   ├─ ARQUITECTURA.md                       ← Diagrama componentes
   ├─ STACK_TECNOLOGICO.md                  ← Python, PySpark, Pydantic, PyHOCON
   └─ ENTRY_POINTS.md                       ← worker.py, app.py, etc.
```

---

## 🎯 Guía Rápida: Qué Leer Cuándo

### Escenario 1: "Acabo de sentarme a trabajar"
```
1. Abre: START_HERE.md (2 min)
2. Abre: contexto/CONTEXTO_ACTUAL.md (2 min)
3. Abre: contexto/COPILOT_INSTRUCTIONS.md (1 min)
⏱️ Total: 5 minutos max
```

### Escenario 2: "No recuerdo qué variable usar"
```
1. Cmd+Shift+F en Obsidian
2. Busca: "label_id" o "gf_pfm_tag"
3. Consulta: referencias/NOMENCLATURA.md
⏱️ 30 segundos
```

### Escenario 3: "¿Cómo hago X en Spark?"
```
1. Consulta: referencias/SPARK_FUNCTIONS.md
2. Busca: "f.transform()" u otra función
3. Copia ejemplo
⏱️ 1 minuto
```

### Escenario 4: "Terminé feature, debo actualizar"
```
1. Abre: contexto/CAMBIOS_RECIENTES.md
2. Agrega: sección con lo que hiciste
3. Commit + push
⏱️ 2 minutos
```

### Escenario 5: "Hice decisión importante"
```
1. Crea: decisiones/NOMBRE.md
2. Copia: decisiones/TEMPLATE_DECISION.md
3. Rellena template
4. Commit + push
⏱️ 15 minutos
```

---

## 📍 Localización de Respuestas

| Pregunta | Respuesta | Ubicación |
|----------|-----------|-----------|
| ¿Cuál es el estado actual? | CONTEXTO_ACTUAL.md | `/contexto/` |
| ¿Cuáles son las reglas? | COPILOT_INSTRUCTIONS.md | `/contexto/` |
| ¿Qué variable usar? | NOMENCLATURA.md | `/referencias/` |
| ¿Cómo hacer X en Spark? | SPARK_FUNCTIONS.md | `/referencias/` |
| ¿Qué config existe? | CONFIGURACION_KEYS.md | `/referencias/` |
| ¿Por qué hicimos X? | TAG_MERGING_LOGIC.md | `/decisiones/` |
| ¿Cómo documentar decisión? | TEMPLATE_DECISION.md | `/decisiones/` |
| ¿Qué se cambió? | CAMBIOS_RECIENTES.md | `/contexto/` |

---

## 📊 Estadísticas del Vault

| Métrica | Cantidad |
|---------|----------|
| Total archivos | 12 |
| Total palabras | ~18,000 |
| Total líneas código | ~2,000 |
| Templates | 1 (decision) |
| Tamaño | ~85 KB |
| Ubicación | `~/Documents/` |
| Versionado | Git ✅ |

---

## 🎓 Archivos por Propósito

### 🔴 CRÍTICOS (Leer ANTES de trabajar)
- ✅ contexto/COPILOT_INSTRUCTIONS.md
- ✅ contexto/CONTEXTO_ACTUAL.md

### 🟡 IMPORTANTES (Consultar mientras codeas)
- ✅ referencias/NOMENCLATURA.md
- ✅ referencias/SPARK_FUNCTIONS.md

### 🟢 REFERENCIAS (Cuando aplica)
- ✅ referencias/CONFIGURACION_KEYS.md
- ✅ decisiones/TAG_MERGING_LOGIC.md

### ⚫ ADMINISTRATIVA (Mantenimiento)
- ✅ contexto/CAMBIOS_RECIENTES.md
- ✅ decisiones/TEMPLATE_DECISION.md

### ◻️ OPCIONAL (Para equipos grandes)
- ⭕ tareas/BACKLOG.md
- ⭕ proyecto/ARQUITECTURA.md

---

## 🔗 Enlaces Internos (Copia en Obsidian)

```
[[INDEX]]
[[SETUP]]
[[NAVIGATION]]
[[_global/standards/WIKILINKS_CONVENCION]]
[[_global/copilot/INSTRUCCIONES_BASE]]
[[kpfm/proyectos/kpfm-etiquetas/README]]
[[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]
[[kpfm/proyectos/kpfm-etiquetas/referencias/NOMENCLATURA]]
[[kpfm/proyectos/kpfm-etiquetas/decisiones/TAG_MERGING_LOGIC]]
```

## 🧩 Convención para Graph View

- Usa `[[Nota]]` sin `.md`.
- Si hay ambigüedad, usa `[[carpeta/Nota]]`.
- Para secciones, usa `[[Nota#Seccion]]`.
- Referencia oficial: [[_global/standards/WIKILINKS_CONVENCION]].

---

## 🎯 Recomendaciones Finales

### ✅ Hacer
- ✅ Leer START_HERE.md hoy
- ✅ Abrir Obsidian y explorar
- ✅ Marcar favoritos (Cmd+Shift+B) COPILOT_INSTRUCTIONS.md
- ✅ Bookmark buscador (Cmd+Shift+F) para referencias
- ✅ Sync automático (Git o Obsidian Sync)

### ❌ No hacer
- ❌ Leer TODO el vault de una vez (abrumador)
- ❌ Dejar pasar más de 1 semana sin actualizar
- ❌ Guardar secrets en vault
- ❌ Editar archivos sin saber qué hacen

---

## 💾 Backup y Sincronización

### Opción 1: Git (Recomendado ahora)
```bash
cd ~/Documents/kpfm-obsidian-vault
git init
git add .
git commit -m "Initial vault"
git remote add origin [repo]
git push
```

### Opción 2: Obsidian Sync (Premium, $8/mes)
- Automático entre dispositivos
- Versionado + rollback
- Encriptado end-to-end

### Opción 3: iCloud Drive (Integrado)
- Carpeta en `/Documents/kpfm-obsidian-vault`
- Auto-sync en iCloud
- ⚠️ Cuidado con conflictos locales

---

## 🚀 Checklist Final

### Antes de Usar (Hoy)
- [ ] Descargar Obsidian
- [ ] Abrir vault
- [ ] Leer START_HERE.md
- [ ] Leer COPILOT_INSTRUCTIONS.md

### Primeros 7 Días
- [ ] Usar vault 5+ veces
- [ ] Actualizar CAMBIOS_RECIENTES.md 2+ veces
- [ ] Consultar referencias/NOMENCLATURA.md
- [ ] Pedir contexto a Copilot usando referencias

### Primeras 2 Semanas
- [ ] Configurar backup (Git)
- [ ] Crear 1-2 archivos en /decisiones/
- [ ] Explorar graph view
- [ ] Notar reducción en iteraciones Copilot

---

## 📞 Troubleshooting Rápido

| Problema | Solución |
|----------|----------|
| "Obsidian no abre vault" | Verifica ruta: `~/Documents/kpfm-obsidian-vault/` |
| "No encuentro archivo" | `Cmd+O` → search, o `Cmd+Shift+F` → text search |
| "Links rotos" | Check: panel derecho → "Unlinked mentions" |
| "Cambios no se sincronizaban" | Git push/pull o Obsidian Sync |
| "¿Dónde pongo X archivo?" | Consulta estructura arriba (🗺️) |

---

## 🎓 Próximo Paso

**Derecho:** Abre `START_HERE.md` ahora mismo.

```
1. Ya estás leyendo este archivo
2. Haz click en: [[START_HERE]]
3. O: Cmd+O → "START_HERE"
```

---

**Versión:** 1.0
**Creada:** 29 Junio 2026
**Estado:** ✅ Listo para Usar
**Next:** START_HERE.md → QUICK_START.md → CONTEXTO_ACTUAL.md


