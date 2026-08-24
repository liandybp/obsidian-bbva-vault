# 🏛️ BBVA Vault Obsidian - Documentación Multi-UUAA

**Documentación centralizada para todas las UUAAs de BBVA**

Versión: 1.0 (BBVA Multi-UUAA)
Fecha: 30 Junio 2026

---

## 🎯 Estructura

```
📁 BBVA/
│
├─ 🌍 _global/               ← Estándares y directrices compartidas
│
├─ 📊 kpfm/                 ← UUAA KPFM
│  └─ proyectos/
│     └─ kpfm-etiquetas/    ← Proyectos de KPFM
│
├─ 📦 kagr/                 ← UUAA KAGR
│  └─ proyectos/
│     ├─ lib-agregador-github/           ← Librería central
│     ├─ engine-agregador/               ← Engine consumidor (ejemplo)
│     └─ [Más proyectos]
│
└─ 💼 wumc/                ← UUAA WUMC
   └─ proyectos/
      └─ [Proyectos de WUMC]
```

## 🔍 Por UUAA

### KPFM
- [[kpfm/README|KPFM]] — Directorio principal
  - [[kpfm/proyectos/kpfm-etiquetas/README|kpfm-etiquetas]] — Tagging y etiquetado

### KAGR
- [[kagr/README|KAGR]] — Directorio principal
  - [[kagr/proyectos/lib-agregador-github/README|lib-agregador-github]] — 🔑 **Librería central de tagging**
    - [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION|Descripción]]
    - [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA|Estructura]]
    - [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS|Catálogos y Regex]]
    - [[kagr/proyectos/lib-agregador-github/referencias/ENGINES|Engines Consumidores]]

### WUMC
- [[wumc/README|WUMC]] — Directorio principal

## 🔑 Nota Crítica

> **Todas las regex de Agregador, User Space y Proloan se gestionan desde `lib-agregador-github` (KAGR).**
>
> La librería es agnóstica — la configuración reside en los **engines** consumidores de cada UUAA.

## 🌍 Estándares Globales

- [[_global/copilot/INSTRUCCIONES_BASE|Instrucciones Base]] — Lo que TODOS los proyectos respetan
- [[_global/standards/NOMENCLATURA_GLOBAL|Nomenclatura Global]]
- [[_global/standards/TESTING_GUIDELINES|Testing Guidelines]]

## 🚀 Inicio Rápido

1. Localiza tu UUAA arriba
2. Consulta el proyecto específico que necesites
3. Lee la documentación técnica en cada proyecto
4. Si es KAGR → Consulta `lib-agregador-github` para entender la librería base


