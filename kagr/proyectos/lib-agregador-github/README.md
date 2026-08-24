# lib-agregador-github

Librería central que gestiona toda la lógica de tagging y catálogos para el procesamiento de movimientos bancarios.

## 🔑 Nota Clave

> **Todas las regex de Agregador, User Space y Proloan se gestionan desde esta librería.** Es el punto único de verdad (single source of truth) para la **lógica** de patrones de identificación.

⚠️ Esta librería **NO lleva configuración propia** — la configuración reside en los **engines** (aplicaciones consumidoras).

## Inicio Rápido

### Para Entender la Librería:
- [[kagr/proyectos/lib-agregador-github/referencias/DESCRIPCION]] — Descripción técnica completa
- [[kagr/proyectos/lib-agregador-github/referencias/ESTRUCTURA]] — Estructura de la librería
- [[kagr/proyectos/lib-agregador-github/referencias/CATÁLOGOS]] — Catálogos y regex gestionados

### Para Trabajar con la Librería:
- [[kagr/proyectos/lib-agregador-github/contexto/COPILOT_INSTRUCTIONS]] — Instrucciones para Copilot
- [[kagr/proyectos/lib-agregador-github/contexto/ARQUITECTURA]] — Vista de componentes
- [[kagr/proyectos/lib-agregador-github/decisiones/ARQUITECTURA_LIBRERIA_ENGINES]] — Por qué se separa librería y engines

### Para Integrar Engines:
- [[kagr/proyectos/lib-agregador-github/referencias/ENGINES]] — Cómo consumen la librería

## Localización

```
/Users/t022458/PycharmProjects/kagr/lib/lib_agregador_github/
```


