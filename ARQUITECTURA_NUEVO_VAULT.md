# Arquitectura Nuevo Vault

Vault multi-proyecto con capa global reutilizable.

## Estructura
- Global: `/_global`
- Proyectos: `/proyectos/<nombre>`
- Entradas raiz: [[INDEX]], [[SETUP]], [[NAVIGATION]]

## Principio
- Reglas comunes en [[_global/copilot/INSTRUCCIONES_BASE]].
- Reglas locales por proyecto en `proyectos/<nombre>/contexto/COPILOT_INSTRUCTIONS.md`.
- Convencion de enlaces en [[_global/standards/WIKILINKS_CONVENCION]].

## Flujo de trabajo
1. Leer global.
2. Leer contexto del proyecto activo.
3. Implementar.
4. Actualizar `ESTADO` y `CAMBIOS_RECIENTES` del proyecto.

## Proyecto actual
- [[kpfm/proyectos/kpfm-etiquetas/README]]
- [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]

