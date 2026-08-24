# Quick Start — Auditoría Repositorio (Versión Corta)

**Copia y pega ESTO en Copilot Chat cuando quieras auditar un repo.**

---

## Prompt Corto (30 segundos)

```text
Audita este repositorio contra directrices BBVA:

Datos repositorio:
- Ruta: [COMPLETAR: ej. /Users/tu-username/path/to/repo]
- Nombre: [COMPLETAR: ej. kpfm-etiquetas]
- Stack: [COMPLETAR: ej. Python 3.11, PySpark, Pydantic]
- Tipo: [COMPLETAR: batch|online|library|iac]
- Plataforma: [COMPLETAR: EMR|Dataproc|SageMaker|Athena|GitLab]

Instrucciones:
1. Lee: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
2. Escanea en prioridad (tabla sección 7):
   - CRÍTICA: ficheros vencidos Q4-2025
   - MEDIA: recomendaciones Q4-2025
   - Anti-patrones: secrets, binarios, ficheros gigante
   - Testing + Pipeline
3. Devuelve: reporte con gaps + estimado esfuerzo

Template completo (si necesitas todos detalles): [[_global/copilot/PROMPT_AUDITORIA_REPOSITORIO]]
```

---

## Cómo usar

### 1️⃣ Abre Copilot Chat
- **JetBrains:** `Cmd+Shift+Enter` → Chat tab
- **Web:** https://github.com/copilot

### 2️⃣ Rellena `[COMPLETAR]` con tu repo
Ejemplo:
```
- Ruta: /Users/t022458/PycharmProjects/kpfm/lib/etiquetas
- Nombre: kpfm-etiquetas
- Stack: Python 3.11, PySpark, Pydantic, PyHOCON
- Tipo: batch
- Plataforma: EMR
```

### 3️⃣ Pega en Copilot
```
Audita este repositorio contra directrices BBVA:

Datos repositorio:
- Ruta: /Users/t022458/PycharmProjects/kpfm/lib/etiquetas
- Nombre: kpfm-etiquetas
- Stack: Python 3.11, PySpark, Pydantic, PyHOCON
- Tipo: batch
- Plataforma: EMR

[... resto del prompt ...]
```

### 4️⃣ Espera reporte
Copilot devuelve:
```
✅ AUDITORÍA COMPLETADA

CRÍTICA - FALTANTE (X gaps)
- Fichero 1: FALTA
- Fichero 2: INCOMPLETO

...

PRÓXIMOS PASOS:
→ Generar ficheros = Xh
Estimado total: Xh
```

---

## Referencias en Vault

- **Directrices completas:** [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
- **Template largo:** [[_global/copilot/PROMPT_AUDITORIA_REPOSITORIO]]
- **Prompts otros:** [[_global/copilot/README_COPILOT]]

---

**Versión:** 1.0 (Quick Start)
**Creada:** 29 Junio 2026

