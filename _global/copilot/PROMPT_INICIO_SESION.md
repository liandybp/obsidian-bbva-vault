# Prompt Inicio de Sesion (Copilot)

Usa este prompt al arrancar una sesion para que Copilot cargue contexto desde el vault.

```text
Antes de implementar cualquier cambio, usa el vault de Obsidian como fuente de contexto.

Ruta del vault:
~/Documents/kpfm-obsidian-vault/

Pasos obligatorios:
1) Lee `INDEX` y `NAVIGATION`.
2) Lee reglas globales en `_global/copilot/INSTRUCCIONES_BASE`.
3) Lee convencion de links en `_global/standards/WIKILINKS_CONVENCION`.
4) Identifica el proyecto activo y lee:
   - `proyectos/<proyecto>/README`
   - `proyectos/<proyecto>/ESTADO`
   - `proyectos/<proyecto>/contexto/COPILOT_INSTRUCTIONS`
5) Si falta documentacion del proyecto, crea estructura minima:
   - `README`, `ESTADO`, `contexto/COPILOT_INSTRUCTIONS`, `contexto/CAMBIOS_RECIENTES`, `referencias/`, `decisiones/`.
6) Implementa cambios respetando reglas globales + locales.
7) Al finalizar, actualiza:
   - `proyectos/<proyecto>/contexto/CAMBIOS_RECIENTES`
   - `proyectos/<proyecto>/ESTADO`
   - decisiones/referencias si aplica.

Regla de eficiencia:
- Usa contexto curado (links + resumen corto), evita pegar documentos completos.
```

## Referencias
- [[INDEX]]
- [[NAVIGATION]]
- [[_global/copilot/INSTRUCCIONES_BASE]]
- [[_global/standards/WIKILINKS_CONVENCION]]

