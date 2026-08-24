# Estado - categ-gl-git

## Estado actual (21 Agosto 2026)

### ✅ Completado
- Lectura de `t_kpfm_movements` simplificada: solo desde catalogo de entidades en `TaggingJob.movements` (sin fallback parquet).
- Integración de `gf_pfm_mov_tag_arc` en `TaggingJob` usando lectura parquet multi-ruta.
- Fallback de lectura para `t_kpfm_movements` en `TaggingJob` (`tagging_jobs.py`): primero catálogo de entidades, después parquet.
- Implementación de lecturas del fallback usando `adautils` (`AdviceDataFrameReader`).
- Tests focalizados para prioridad de fuente y fallback de movimientos.
- Alineación del contrato de configuración para entity catalog con dos variables: `MOVEMENTS_ENTITY_DATABASE` y `MOVEMENTS_ENTITY_TABLE`.

### 🔄 En progreso
- Revisión funcional de la rama `feature/AIFORRCP-1715-movement-tagger-table-reading-update`.
- Validación de alcance entre lectura de movimientos y enriquecimiento de `mov_tags_arc` (solo parquet).
- Pendiente en repo consumidor: alta de `MOVEMENTS_ENTITY_DATABASE` y `MOVEMENTS_ENTITY_TABLE` en su configuracion.

## Enlaces clave
- [[gl/proyectos/categ-gl-git/README]]
- [[gl/proyectos/categ-gl-git/contexto/COPILOT_INSTRUCTIONS]]
- [[gl/proyectos/categ-gl-git/contexto/CAMBIOS_RECIENTES]]
- [[_global/standards/WIKILINKS_CONVENCION]]

---

**Última actualización:** 20 Agosto 2026  
**Status:** Activo






