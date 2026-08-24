# KPFM Etiquetas - Vault Obsidian

## 📌 Propósito
Este vault Obsidian centraliza el contexto del proyecto **kpfm/lib/etiquetas** para mantener GitHub Copilot siempre actualizado con:
- Arquitectura del proyecto
- Reglas funcionales críticas
- Decisiones de diseño
- Patrones de código
- Estado de tareas y features

## 📁 Estructura

### `/proyecto`
Documentación técnica del proyecto:
- `ARQUITECTURA.md` - Diagrama de componentes y flujos
- `STACK_TECNOLOGICO.md` - Stack: Python 3.11, PySpark, Pydantic, PyHOCON
- `ENTRY_POINTS.md` - Puntos de entrada: worker.py, app.py

### `/contexto`
Contexto actual del proyecto:
- `COPILOT_INSTRUCTIONS.md` - Instrucciones vigentes para Copilot
- `CONTEXTO_ACTUAL.md` - Estado actual del proyecto (última sesión)
- `CAMBIOS_RECIENTES.md` - Features/fixes implementadas recientemente

### `/tareas`
Tracking de work items:
- `BACKLOG.md` - Features pendientes
- `EN_PROGRESO.md` - Tareas activas
- `COMPLETADAS.md` - Features finalizadas

### `/decisiones`
Decisiones arquitectónicas y de diseño:
- `TAG_MERGING_LOGIC.md` - Decisión: Merge de etiquetas con historia
- `SCHEMA_EVOLUTION.md` - Campo `gf_pfm_tag_updated_date`

### `/referencias`
Referencias rápidas:
- `NOMENCLATURA.md` - Términos del proyecto
- `CONFIGURACION_KEYS.md` - Claves de configuración críticas
- `SPARK_FUNCTIONS.md` - Funciones Spark usadas frecuentemente

## 🎯 Cómo usar este vault

### Para Copilot:
1. **Antes de trabajar en una tarea:** 
   - Leer `/contexto/COPILOT_INSTRUCTIONS.md`
   - Revisar `/contexto/CONTEXTO_ACTUAL.md`

2. **Al escribir código:**
   - Consultar `/referencias/NOMENCLATURA.md`
   - Seguir patrones en `/proyecto/ARQUITECTURA.md`

3. **Después de completar una tarea:**
   - Actualizar `/contexto/CAMBIOS_RECIENTES.md`
   - Documentar decisión en `/decisiones/` si aplica

### Para el usuario:
- Revisar `CONTEXTO_ACTUAL.md` antes de iniciar nueva sesión
- Agregar tareas a `/tareas/EN_PROGRESO.md`
- Documentar decisiones importantes en `/decisiones/`

## 🔄 Workflow de actualización

Después de cada sesión productiva:
```bash
# 1. Actualizar estado actual
# 2. Documentar cambios recientes
# 3. Actualizar referencias si aplica
# 4. Limpiar y comprimir vault
```

**Última actualización:** 29 de junio, 2026

