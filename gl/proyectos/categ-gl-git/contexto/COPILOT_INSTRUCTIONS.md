# Instrucciones Copilot - categ-gl-git

## Base obligatoria
- [[_global/copilot/INSTRUCCIONES_BASE]]
- [[_global/standards/WIKILINKS_CONVENCION]]

## Contexto del proyecto
- Repositorio: `/Users/t022458/PycharmProjects/gl/categ_gl_git`
- Dominio: categorización global de movimientos
- Runtime principal: `glglcsadvpyglobaltagger/gl/runtime/batch/jobs/`
- Jobs relevantes:
  - `tagging_jobs.py`
  - `tagging_jobs_v2.py`

## Reglas locales
- Mantener cambios pequeños y acotados por job/helper.
- No mezclar la lectura de `t_kpfm_movements` con la de `mov_tags_arc`.
- `mov_tags_arc` se consume desde parquet.
- Si se modifica comportamiento de lectura, cubrir explícitamente:
  - fuente prioritaria
  - fallback
  - fallo cuando ambas fuentes no están disponibles

## Flujo por sesión
### Antes de implementar
1. Leer [[_global/copilot/INSTRUCCIONES_BASE]]
2. Revisar [[gl/proyectos/categ-gl-git/ESTADO]]
3. Revisar [[gl/proyectos/categ-gl-git/contexto/CAMBIOS_RECIENTES]]

### Al finalizar
- Actualizar [[gl/proyectos/categ-gl-git/contexto/CAMBIOS_RECIENTES]]
- Actualizar [[gl/proyectos/categ-gl-git/ESTADO]] si cambia el estado real
- Actualizar `[[daily/2026-08-20]]` o la nota diaria correspondiente

