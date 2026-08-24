# Decisión Arquitectónica: Separación Librería-Engines

**Decisor**: Arquitecto KAGR  
**Fecha**: 30 Junio 2026  
**Estado**: ✅ APROBADA Y DOCUMENTADA  
**Revisores**: KPFM, WUMC, Agregador  

---

## Problema

Los sistemas de tagging (Agregador, User Space, Proloan) duplicaban código y compartían lógica de forma frágil. Cambios en una parte rompían otras. No había punto único de verdad para regex.

---

## Decisión

**Centralizar toda la lógica de tagging en una librería agnóstica (`lib-agregador-github`) que no lleve configuración. Los engines (aplicaciones consumidoras) aportan la configuración.**

---

## Justificación

| Aspecto | Ventaja |
|--------|---------|
| **Reutilización** | Múltiples engines usan el mismo código |
| **Mantenimiento** | Un único lugar para cambiar lógica |
| **Agnóstica** | No depende de deployments específicos |
| **Flexibilidad** | Cada engine elige su configuración |
| **Testing** | Librería testeable independientemente |
| **Escalabilidad** | Agregar nuevos engines es trivial |

---

## Alternativas Consideradas

### ❌ Opción 1: Monolito único
- Problema: Un cambio afecta todo
- Problema: Difícil de testear

### ❌ Opción 2: Duplicar código
- Problema: Mantenimiento insostenible
- Problema: Sin "single source of truth"

### ✅ Opción 3: Librería + Engines (ELEGIDA)
- ✅ Separación clara de responsabilidades
- ✅ Reutilizable
- ✅ Mantenible

---

## Implementación

### Librería (`lib-agregador-github`)
```
Responsabilidad: LÓGICA
├─ DefaultTaggingLogic
├─ BrandLogic, CategoryLogic, etc.
├─ CatalogReaders
├─ Tablas/Schemas
└─ Utilidades
```

**NO Contiene**:
- ❌ Configuración por entorno (dev/prod)
- ❌ Paths hardcodeados
- ❌ Secrets
- ❌ Database specifics

### Engines (Agregador, User Space, Proloan)
```
Responsabilidad: CONFIGURACIÓN + ORQUESTACIÓN
├─ application.conf (claves base)
├─ catalogs.conf (paths por entorno)
├─ catalogs_proloan.conf (específico Proloan)
├─ Jobs que instancian la librería
└─ Datasources específicas
```

---

## Catálogos: Flujo de Datos

```
Engine (config)
├─ Define: BRANDS_REGEX_CATALOG_PATH = "s3://bucket/..."
└─ Instancia: DailyTaggingJob(spark, config)

DailyTaggingJob
├─ Lee: config.get_string("BRANDS_REGEX_CATALOG_PATH")
├─ Carga: CatalogReader.load_catalogs(config, CATALOGS_CONFIG)
└─ Pasa catálogos a: DefaultTaggingLogic(**catalogs)

DefaultTaggingLogic
└─ Usa catálogos recibidos (agnóstica a su origen)
```

**Ventaja**: Mismo código en dev (local) y prod (S3)

---

## Ejemplo: Agregar Regex Nueva

### Librería (NO CAMBIA)
```python
# tagging_jobs.py
CATALOGS_CONFIG = [
    CatalogConf('positive_regex_catalog', 'POSITIVE_REGEX_CATALOG_PATH', CategoryRegexCatalogReader),
    ...
]
```

### Engine (CAMBIA)
```hocon
# catalogs.conf
POSITIVE_REGEX_CATALOG_PATH = "s3://prod-catalogs/gastos_regex_v2.csv"

# Contiene nueva regex: "(?i).*bizum.*" → 77
```

**Resultado**: Sin cambiar librería, Proloan usa nuevas regex

---

## Riesgos Mitigados

| Riesgo | Mitigación |
|--------|-----------|
| Cambio en librería rompe engines | Tests + deprecation warnings |
| Engine cambia catálogo incorrectamente | Validación en reader |
| Configuración duplicada | Centralización en engine |
| Sin compatibilidad backwards | Versionado de librería |

---

## Métricas de Éxito

✅ Un único lugar para cambiar lógica de tagging  
✅ Múltiples engines reutilizan la librería  
✅ >80% cobertura de tests  
✅ Zero downtime en cambios de regex  
✅ Onboarding nuevo engine < 1 día  

---

## Contramedidas de Cambios

Si necesitas cambiar esta arquitectura:

1. **Documentar** por qué no funciona la actual
2. **Proponer** alternativa con ventajas/desventajas
3. **Validar** impacto en KAGR, KPFM, WUMC
4. **Actualizar** documentación completa
5. **Migrar** código de todos los engines

---

## Referencias

- [[kagr/proyectos/lib-agregador-github/contexto/ARQUITECTURA]]
- [[kagr/proyectos/lib-agregador-github/referencias/ENGINES]]
- [[kagr/proyectos/lib-agregador-github/contexto/COPILOT_INSTRUCTIONS]]

---

**Última actualización**: 30 Junio 2026

