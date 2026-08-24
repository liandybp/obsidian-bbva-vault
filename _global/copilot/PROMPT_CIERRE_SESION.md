# Prompt Cierre de Sesion (Copilot)

Usa este prompt al terminar para asegurar actualizacion del vault.

```text
Cierra la sesion actualizando documentacion del proyecto en el vault de Obsidian.

Ruta:
~/Documents/kpfm-obsidian-vault/

Checklist de cierre:
1) Actualiza `proyectos/<proyecto>/contexto/CAMBIOS_RECIENTES` con:
   - que se cambio
   - archivos tocados
   - tests ejecutados/pendientes
   - decision arquitectonica (si aplica)
2) Actualiza `proyectos/<proyecto>/ESTADO` con avance real y siguientes pasos.
3) Si surgio regla reutilizable, promoverla a `_global/`.
4) Verifica Wikilinks internos validos.

Responde con resumen corto de cierre.
```

## Referencias
- [[kpfm/proyectos/kpfm-etiquetas/ESTADO]]
- [[kpfm/proyectos/kpfm-etiquetas/contexto/CAMBIOS_RECIENTES]]
- [[_global/standards/WIKILINKS_CONVENCION]]

