# GitHub Copilot + Obsidian: Guía de Integración

**Cómo hacer que GitHub Copilot siempre tenga contexto actualizado**

---

## 🎯 La Idea

Normalmente, GitHub Copilot no sabe qué proyecto es. 

Con este vault, TÚ le dices exactamente qué contexto debería tener.

---

## 🔗 Estrategia: 3 Formas de Integrar

### Opción 1: Manual (Recomendado para Empezar)

Cuando le pidas algo a Copilot, menciona el vault:

```
"Implementa [feature] siguiendo las reglas en:
/Users/t022458/Documents/kpfm-obsidian-vault/contexto/COPILOT_INSTRUCTIONS.md

Y consulta la nomenclatura en:
/Users/t022458/Documents/kpfm-obsidian-vault/referencias/NOMENCLATURA.md"
```

**Ventaja:** Total control, Copilot ve contexto fresco
**Desventaja:** Repetir ruta cada vez

### Opción 2: Comentario en Código (Recomendado)

En la parte superior de `worker.py` o `app.py`:

```python
"""
Consultar contexto en: ~/Documents/kpfm-obsidian-vault/contexto/
Reglas Copilot: ~/Documents/kpfm-obsidian-vault/contexto/COPILOT_INSTRUCTIONS.md
"""
```

Luego pide a Copilot:
```
"Lee el docstring del archivo y aplica todas las reglas"
```

**Ventaja:** Copilot lo ve automáticamente
**Desventaja:** Requiere actualizar si cambian reglas

### Opción 3: GitHub Workspaces (Futuro - Premium)

Si usas Codespaces o GitHub Cloud Shell:
```bash
# Clonar vault en workspace
git clone [repo-vault] ~/workspace-context
```

Copilot accede a contexto auto.

---

## 🎓 Ejemplo Práctico

### Situation
Necesitas que Copilot implemente una feature nueva siguiendo el contexto.

### Pasos

#### 1. Abre PyCharm + Obsidian

```bash
# Terminal
cd ~/PycharmProjects/kpfm/lib/etiquetas
code .                      # PyCharm

# Otra ventana
open ~/Documents/kpfm-obsidian-vault
# (se abre en Obsidian)
```

#### 2. Lee contexto en Obsidian (2 min)

En Obsidian:
- Abre `contexto/CONTEXT_ACTUAL.md`
- Abre `contexto/COPILOT_INSTRUCTIONS.md`
- Scanning rápido

#### 3. Abre Copilot en PyCharm

```
Cmd+I (o Cmd+/)  → Abre Copilot chat
```

#### 4. Pásale el contexto

**Chat a Copilot:**
```
"Implementa la siguiente feature:
[Descripción]

Consulta estas reglas:
https://archive copy COPILOT_INSTRUCTIONS.md aquí

Y usa estos patrones Spark:
https://archive copy SPARK_FUNCTIONS.md

Nomenclatura (usa estos nombres):
https://archive copy NOMENCLATURA.md"
```

#### 5. Copilot Sugiere

Copilot genera código respetando:
- ✅ Reglas de nomenclatura
- ✅ Patrones Spark
- ✅ Estructura del proyecto
- ✅ Tests incluidos

---

## 📋 Checklist: Qué Pasar a Copilot

**Para TODA feature nueva, pasa:**

```
[ ] Archivo: COPILOT_INSTRUCTIONS.md
[ ] Sección: "Reglas funcionales CRÍTICAS"
[ ] Archivo: NOMENCLATURA.md
[ ] Sección: "Variables Principales"
[ ] Archivo: SPARK_FUNCTIONS.md (si usa Spark)
[ ] Archivo: TAG_MERGING_LOGIC.md (si related)
```

**Máximo:** Copy-paste 3-4 archivos.

---

## 💾 Workflow Recomendado

### Sesión Inicio (5 min)

```
1. Abrir Obsidian
   └─ Leer contexto/CONTEXTO_ACTUAL.md

2. Abrir PyCharm
   └─ Leer código existente

3. Abrir Copilot Chat
   └─ Pega COPILOT_INSTRUCTIONS.md
   └─ Pega NOMENCLATURA.md
   └─ "Implementa [feature]"
```

### Sesión Trabajo (30 min)

```
1. Copilot sugiere código
2. Modifica en PyCharm
3. Ejecuta tests
4. Iteración si aplica
```

### Sesión Fin (2 min)

```
1. Abre Obsidian
2. Actualiza contexto/CAMBIOS_RECIENTES.md
3. Commit + push
```

---

## 🔧 Copy-Paste Rápido: 3 Comandos

### Para Copilot (Copy cuando necesites contexto)

#### Comando 1: Reglas del Proyecto
```
---
CONTEXTO: Proyecto PySpark de Etiquetado
STACK: Python 3.11, PySpark, Pydantic, PyHOCON

REGLAS CRÍTICAS:
- País se determina por UNIVERSE_COUNTRY_ID (NUNCA fallback)
- Etiquetas selectas: LABELS_PARAM.LABELS_TO_APPLY (si vacío = sin etiquetado)
- Schema: TAG_ENTRY_SCHEMA con campos gf_pfm_tag_*
- Merge: merge_with_existing_tags() reconcilia etiquetas nuevas + previas
- Timestamps: usar RuleEngine._execution_tag_date() centralizado
- Test: incluir tests toda lógica crítica

NOMENCLATURA:
- label_id (identificador único)
- LABELS_PARAM (parámetro config)
- gf_mov_tags_arc (columna de etiquetas)
- gf_pfm_tag_* (prefijo campos)
- RuleEngine (motor evaluación)

NO hacer:
- Reintroducir `enabled` como control
- "Lista vacía evalúa todo" para LABELS_TO_APPLY
- Fallbacks a otros países
- Cambios arquitectura sin solicitud
---
```

Copilot lo respeta incluso sin archivo real.

#### Comando 2: Request Pattern
```
"Siguiendo estas reglas [pega reglas arriba]:

Implementa [FEATURE]

Detalles:
- Entrada: [qué recibe]
- Salida: [qué produce]
- Restricciones: [qué evitar]

Incluye:
- Código con docstrings
- Tests (mínimo 3 casos)
- Actualización docs si needed"
```

#### Comando 3: Review Pattern
```
"Lee este código contra COPILOT_INSTRUCTIONS.md:

[Pega código]

¿Respeta todas las reglas? Si no, sugiere cambios."
```

---

## 🎯 Casos de Uso Específicos

### Caso 1: Implementar Nueva Etiqueta

**Pasa a Copilot:**
1. `COPILOT_INSTRUCTIONS.md` (sección "Schema de Salida")
2. `NOMENCLATURA.md` (tabla "Variables de Etiqueta")
3. `CONFIGURACION_KEYS.md` (sección "Definición de Etiquetas")

**Resultado:** Copilot crea etiqueta con estructura correcta

### Caso 2: Implementar Nueva Función Spark

**Pasa a Copilot:**
1. `SPARK_FUNCTIONS.md` (función similar como ejemplo)
2. `TAG_MERGING_LOGIC.md` (cómo lo usamos nosotros)
3. `NOMENCLATURA.md` (nomenclatura)

**Resultado:** Copilot código Spark compatible

### Caso 3: Crear Nueva Decisión

**Pasa a Copilot:**
1. `decisiones/TAG_MERGING_LOGIC.md` (template)
2. Tu problema + propuesta solución

**Resultado:** Copilot estructura decisión profesional

---

## 🔄 Mantener Contexto Actualizado

### Trigger: Qué eventos actualizar vault?

```
✅ Cada feature completada
✅ Cada bug fix importante
✅ Cada decision arquitectónica
✅ Cambio en nomenclatura
✅ Nuevo patrón Spark descubierto
```

### Cómo actualizar:

```
1. Sesión completa
2. Abre Obsidian
3. Actualiza:
   - contexto/CAMBIOS_RECIENTES.md (+5 líneas)
   - referencias/* (si aplica)
   - decisiones/* (si fue decision importante)
4. Commit en git
5. Listo
```

---

## 🚀 Nivel Experto: Automatización

### Crear Telegram Bot que Sincronice

```python
# (Futuro: código boilerplate)
# Bot recibe: "Feature X completada"
# Lee: CAMBIOS_RECIENTES.md
# Actualiza: Obsidian vault
# Pushea: Git automático
```

(Opcional, solo si equipo grande)

---

## 💡 Pro Tips: Copilot + Obsidian

### ✅ Hacer
- ✅ Pasar contexto COMPLETO primero (5 segundos de lectura es OK)
- ✅ Pedir a Copilot que resuma reglas: "¿Cuáles son las 3 reglas críticas?"
- ✅ Usar Obsidian links en Copilot: "Lee [[COPILOT_INSTRUCTIONS]]"
- ✅ Guardar respuestas buenas: Crear `/contexto/COPILOT_PROMPTS.md`

### ❌ No hacer
- ❌ Pasar vault al 100% (demasiado ruido)
- ❌ Preguntarle sobre contexto que no pasaste
- ❌ Olvidar actualizar vault después de terminar
- ❌ Usar Copilot sin mencionar reglas (genera best practices globales, no proyecto-specific)

---

## 📊 Benchmarking: Con vs Sin Contexto

### Sin Contexto (Tradicional)
```
Copilot: "Creo que debería usar X pattern"
Tú: "No, en este proyecto usamos Y"
Copilot: "OK, aquí la versión Y..."
Iteraciones: 3-4 ❌
Tiempo: 15 min
```

### Con Contexto (Este Vault)
```
Tú: "Pasa [{reglas}/{nombeclatura}/{ejemplos}]"
Copilot: "Entiendo, usaré Y pattern"
[Genera código correcto primero intento]
Iteraciones: 0-1 ✅
Tiempo: 3 min
```

**Ahorro: ~70% tiempo** 🚀

---

## 🔗 Links Importantes

| Asset | Ubicación |
|-------|-----------|
| Reglas Copilot | `~/Documents/kpfm-obsidian-vault/contexto/COPILOT_INSTRUCTIONS.md` |
| Nomenclatura | `~/Documents/kpfm-obsidian-vault/referencias/NOMENCLATURA.md` |
| Spark Patterns | `~/Documents/kpfm-obsidian-vault/referencias/SPARK_FUNCTIONS.md` |
| Decisiones | `~/Documents/kpfm-obsidian-vault/decisiones/TAG_MERGING_LOGIC.md` |

---

## 🎓 Próximo Paso

1. Instala Obsidian
2. Abre vault
3. Lee QUICK_START.md (5 min)
4. Empieza a trabajar con Copilot + contexto

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026
**Integración:** GitHub Copilot + Obsidian Vault ✅

