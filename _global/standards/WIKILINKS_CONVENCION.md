# Convencion de Wikilinks en Obsidian

Objetivo: maximizar la conectividad del Graph View y evitar enlaces rotos.

## Regla base
- Usa `[[Nota]]` sin extension `.md`.
- Si puede haber duplicados, usa ruta corta: `[[carpeta/Nota]]`.
- Para secciones: `[[Nota#Seccion]]`.
- Para texto visible distinto: `[[Nota|Texto visible]]`.

## Estandar recomendado
- Nota unica en el vault: `[[INSTRUCCIONES_BASE]]`.
- Nota repetible por proyecto: `[[kpfm/proyectos/kpfm-etiquetas/README]]`.
- Secciones estables: evita renombrar headings usados en links.

## Anti-patrones
- Evitar `[[archivo.md]]` si no es necesario.
- Evitar rutas largas con muchos `../`.
- Evitar links a notas que no existen.

## Checklist rapido
- [ ] Link sin `.md`
- [ ] Si hay ambiguedad, ruta de carpeta
- [ ] Seccion existe cuando usas `#Seccion`
- [ ] El archivo destino existe

## Referencias
- [[INDEX]]
- [[NAVIGATION]]
- [[_global/copilot/INSTRUCCIONES_BASE]]

