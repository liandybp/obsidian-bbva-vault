# 🔄 Migración de Estructura - Por UUAA (20 Julio 2026)

## 📍 Cambio Realizado

Se reorganizó el vault para mantener proyectos **organizados por UUAA** en lugar de tenerlos todos en una carpeta `proyectos/` global.

### Estructura Anterior (Plana)
```
BBVA_Vault/
├── proyectos/
│   ├── kpfm-etiquetas/      ← Proyecto KPFM
│   └── ...
├── kpfm/                     ← UUAA KPFM
├── kagr/                     ← UUAA KAGR
└── ...
```

### Estructura Nueva (Por UUAA)
```
BBVA_Vault/
├── kpfm/                     ← UUAA KPFM
│   ├── proyectos/
│   │   ├── kpfm-etiquetas/  ← Proyecto 1
│   │   └── otro-proyecto/   ← Proyecto 2 (cuando exista)
│   └── README.md
├── kagr/                     ← UUAA KAGR
│   ├── proyectos/
│   │   ├── lib-agregador-github/
│   │   └── ...
│   └── README.md
├── categ_es/                 ← UUAA (cuando exista)
│   ├── proyectos/
│   │   └── ...
│   └── README.md
└── _global/                  ← Standards compartidas (sin UUAA)
```

**Ventajas:**
- ✅ Escalable: Agregar proyecto = nueva carpeta en `UUAA/proyectos/`
- ✅ Organizado: Cada UUAA con su propio namespace
- ✅ Coherente: Refleja estructura real del repositorio
- ✅ Cross-UUAA: `_global/` para reglas compartidas

---

## 📝 Cambios Realizados

### 1. ✅ Contenido Migrado
```
FROM: ~/BBVA_Vault/proyectos/kpfm-etiquetas/*
TO:   ~/BBVA_Vault/kpfm/proyectos/kpfm-etiquetas/*
```
- Todos los archivos de kpfm-etiquetas
- Contexto, decisiones, referencias, docs
- CHANGELOG, ESTADO, README, etc.

### 2. ✅ Wikilinks Actualizadas
**Pattern:** `[[proyectos/kpfm-etiquetas/...]]` → `[[kpfm/proyectos/kpfm-etiquetas/...]]`

**Archivos actualizados:**
- `_global/copilot/INSTRUCCIONES_BASE.md`
- `_global/copilot/PROMPT_CIERRE_SESION.md`
- `_global/copilot/README_COPILOT.md`
- `_global/standards/WIKILINKS_CONVENCION.md`
- `contexto/COPILOT_INSTRUCTIONS.md`
- `SETUP.md`, `NAVIGATION.md`, `INDEX.md`
- `CONSOLIDACION_20JUL2026.md`
- `VALIDACION_FINAL_20JUL2026.md`
- `CIERRE_SESION_20JUL2026.md`
- Y todos los archivos internos del proyecto

### 3. ✅ Limpieza
```bash
rm -rf ~/BBVA_Vault/proyectos/kpfm-etiquetas/
```
Eliminada carpeta antigua (contenido ya migrado)

---

## 🔗 Nuevos Wikilinks (Ejemplos)

### Para sesiones futuras:
- ✅ `[[kpfm/proyectos/kpfm-etiquetas/QUICK_REFERENCE]]`
- ✅ `[[kpfm/proyectos/kpfm-etiquetas/ESTADO]]`
- ✅ `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`
- ✅ `[[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]`

### Para otros proyectos KPFM (cuando existan):
- `[[kpfm/proyectos/otro-proyecto/README]]`
- `[[kpfm/proyectos/otro-proyecto/contexto/COPILOT_INSTRUCTIONS]]`

---

## 📊 Impacto

| Aspecto | Antes | Después |
|---------|-------|---------|
| Estructura | Plana (proyectos/) | Por UUAA (kpfm/, kagr/, etc.) |
| Escalabilidad | Limitada | Escalable |
| Wikilinks | `[[proyectos/...]]` | `[[UUAA/proyectos/...]]` |
| Organización | Centralizada | Descentralizada por UUAA |
| Graph View | Menos claro | Más estructurado |

---

## ✅ Validaciones

- [x] Contenido migrado 100%
- [x] Wikilinks actualizadas en todos los archivos
- [x] Carpeta antigua eliminada
- [x] No hay contenido duplicado
- [x] Estructura coherente

---

## 🔍 Verificación

Para confirmar que todo está en el lugar correcto:

```bash
# Ver estructura kpfm
ls -la ~/Documents/BBVA_Vault/kpfm/proyectos/

# Ver que la carpeta antigua fue eliminada
ls ~/Documents/BBVA_Vault/proyectos/kpfm-etiquetas/ 2>/dev/null
# → No debe existir

# Verificar Wikilinks actualizadas
grep -r "kpfm/proyectos/kpfm-etiquetas" ~/Documents/BBVA_Vault --include="*.md"
# → Debe haber muchas referencias
```

---

## 🚀 Próximos Pasos

### Para nuevos proyectos KPFM
```
1. crear carpeta: ~/BBVA_Vault/kpfm/proyectos/NUEVO_PROYECTO/
2. Copiar template: cp -r _global/templates/PROJECT_TEMPLATE/* .
3. Actualizar Wikilinks: [[kpfm/proyectos/NUEVO_PROYECTO/...]]
```

### Para nuevas UUAAs
```
1. Crear: ~/BBVA_Vault/NUEVA_UUAA/
2. Crear: ~/BBVA_Vault/NUEVA_UUAA/proyectos/
3. Actualizar INDEX.md y NAVIGATION.md
```

---

## 📌 Referencias de Migración

**Antes:**
- Instrucciones antiguas: `[[proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`

**Ahora:**
- Instrucciones nuevas: `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`

**Para próximas sesiones:**
Copilot debe usar SIEMPRE el formato `[[UUAA/proyectos/PROYECTO/...]]`

---

**Migración completada:** 20 Julio 2026, ~11:30 AM  
**Status:** ✅ COMPLETADO  
**Wikilinks actualizadas:** 15+ archivos  
**Contenido duplicado:** Eliminado

