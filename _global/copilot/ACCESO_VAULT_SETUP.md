# Acceso Copilot al Vault - Guía de Setup

**Fecha:** 22 Julio 2026  
**Propósito:** Documentar cómo Copilot accede y mantiene actualizado el vault Obsidian across all projects.  
**Aplicable a:** Todos los proyectos que usen Copilot en el vault BBVA.

---

## 📍 Punto Clave

GitHub Copilot tienes acceso de **lectura/escritura** a TODOS los archivos `.md` en:
```
/Users/t022458/Documents/BBVA_vault/
```

Esto significa que Copilot puede:
- ✅ Leer documentación del proyecto
- ✅ Actualizar CAMBIOS_RECIENTES.md
- ✅ Crear/actualizar daily notes
- ✅ Mantener contexto sincronizado entre sesiones

---

## 🚀 Setup Necesario por Proyecto

Cuando agregues un proyecto nuevo que use Copilot:

### 1. En el Repositorio
**Archivo:** `.github/copilot-instructions.md`

```markdown
## Operacion de contexto en Obsidian (obligatorio)

Copilot **TIENE ACCESO DE LECTURA/ESCRITURA** al vault de Obsidian en `/Users/t022458/Documents/BBVA_vault/`.
Debe mantener actualizado el vault en cada sesion de trabajo.

- Vault base: `/Users/t022458/Documents/BBVA_vault/`
- Instrucciones globales: `/Users/t022458/Documents/BBVA_vault/_global/copilot/INSTRUCCIONES_BASE.md`
- Proyecto actual: `/Users/t022458/Documents/BBVA_vault/[UUAA]/proyectos/[PROYECTO]/`
- Daily notes: `/Users/t022458/Documents/BBVA_vault/daily/YYYY-MM-DD.md`

### Capacidades de Copilot en el Vault
- ✅ **Lectura:** Todos los archivos `.md` del vault
- ✅ **Escritura:** Editar/crear archivos en cualquier carpeta del vault
- ✅ **Actualización:** CAMBIOS_RECIENTES.md, ESTADO.md, daily notes
- ✅ **Creación:** Nueva documentación según estructura de proyecto

[Resto de instrucciones específicas del proyecto...]
```

### 2. En el Vault
**Estructura carpeta proyecto:**
```
~/Documents/BBVA_vault/[UUAA]/proyectos/[PROYECTO]/
├── README.md
├── ESTADO.md                          # ← Copilot actualiza aquí
├── QUICK_REFERENCE.md
├── contexto/
│   ├── COPILOT_INSTRUCTIONS.md        # ← Copilot lee instrucciones
│   ├── CAMBIOS_RECIENTES.md           # ← Copilot actualiza cada sesión
│   ├── ARQUITECTURA.md
│   └── AUDITORIA_VAULT_*.md
├── referencias/
├── decisiones/
└── docs/
```

**Archivo clave:** `contexto/COPILOT_INSTRUCTIONS.md`

```markdown
# Instrucciones Copilot - [PROYECTO]

## 📌 Base Obligatoria (Global)
- [[_global/copilot/INSTRUCCIONES_BASE]]
- [[_global/copilot/ANTI_PATRONES]]

## 🔗 Acceso Vault Obsidian

Copilot actualiza documentación en `/Users/t022458/Documents/BBVA_vault/` durante cada sesión:

- **Lectura/Escritura:** Todos los archivos `.md` del vault
- **Patrón:** Actualizar CAMBIOS_RECIENTES, ESTADO y daily notes
- **Estructura proyecto:**
  ```
  ~/Documents/BBVA_vault/[UUAA]/proyectos/[PROYECTO]/
  ├── contexto/
  │   ├── COPILOT_INSTRUCTIONS.md  ← Este archivo
  │   ├── CAMBIOS_RECIENTES.md     ← Se actualiza cada sesión
  │   └── ...
  ```

[Resto de instrucciones específicas...]
```

---

## 🔄 Flujo de Actualización Esperado

### Inicial de Sesión (Lectura)
```
1. Copilot lee: /BBVA_vault/_global/copilot/INSTRUCCIONES_BASE.md
2. Copilot lee: /BBVA_vault/[UUAA]/proyectos/[PROYECTO]/ESTADO.md
3. Copilot lee: /BBVA_vault/[UUAA]/proyectos/[PROYECTO]/contexto/CAMBIOS_RECIENTES.md
4. Copilot entiende contexto y qué trabajar
```

### Final de Sesión (Escritura)
```
1. Copilot edita: /BBVA_vault/[UUAA]/proyectos/[PROYECTO]/contexto/CAMBIOS_RECIENTES.md
   - Qué se cambió
   - Archivos tocados
   - Tests ejecutados/pendientes
   - Decisiones arquitectónicas

2. Copilot actualiza: /BBVA_vault/[UUAA]/proyectos/[PROYECTO]/ESTADO.md
   - Estado real de avance

3. Copilot crea/actualiza: /BBVA_vault/daily/YYYY-MM-DD.md
   - Todos los proyectos trabajados en el día
   - Resumen por proyecto
   - Bloqueos y próximos pasos
```

---

## 📋 Checklist: Nuevo Proyecto

- [ ] Carpeta creada en vault: `~/Documents/BBVA_vault/[UUAA]/proyectos/[PROYECTO]/`
- [ ] README.md con descripción general
- [ ] ESTADO.md con estado actual
- [ ] contexto/COPILOT_INSTRUCTIONS.md con instrucciones específicas
- [ ] contexto/CAMBIOS_RECIENTES.md (vacío o con sesión inicial si corresponde)
- [ ] .github/copilot-instructions.md en repositorio con sección "Operacion de contexto en Obsidian"
- [ ] Referencia en INSTRUCCIONES_BASE.md a nuevo proyecto (sección "Instrucciones Específicas por Proyecto")

---

## 💡 Ejemplos de Proyectos Usando Este Sistema

### 1. kpfm-etiquetas
- **Repo:** `/Users/t022458/PycharmProjects/kpfm/lib/etiquetas/`
- **Vault:** `[[kpfm/proyectos/kpfm-etiquetas]]`
- **Instrucciones repo:** `.github/copilot-instructions.md` (ACTUALIZADO Con acceso vault)
- **Instrucciones vault:** `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]` (ACTUALIZADO)
- ✅ Modelo a seguir

### 2. kagr - lib-agregador-github (próximo a setup)
- **Repo:** `/Users/t022458/PycharmProjects/kagr/lib/lib_agregador_github/`
- **Vault:** `[[kagr/proyectos/lib-agregador-github]]` (crear si no existe)
- **Instrucciones repo:** `.github/copilot-instructions.md` (ADD sección acceso vault)
- **Instrucciones vault:** `contexto/COPILOT_INSTRUCTIONS.md` (crear)

---

## 🔗 Referencias

- **Guía Global:** [[_global/copilot/INSTRUCCIONES_BASE#🔗-Acceso-Vault-Obsidian]]
- **Plantilla Instrucciones Proyecto:** Ver ejemplo en [[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]
- **Convención Wikilinks:** [[_global/standards/WIKILINKS_CONVENCION]]

---

## ⚠️ Importante: NO Automático

- **Copilot NO sincroniza automáticamente** cambios en vault
- **Requiero instrucción explícita** o estén en mis instrucciones del proyecto
- **Cada proyecto debe tener instrucciones claras** de cuándo y qué actualizar
- **Revisar checklist** al final de cada sesión

---

**Última actualización:** 22 Julio 2026  
**Versión:** 1.0

