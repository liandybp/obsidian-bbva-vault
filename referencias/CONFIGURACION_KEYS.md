# Claves de Configuración - Referencia Rápida

**Todas las claves HOCON utilizadas en el proyecto**

---

## 🔑 Configuración Global (application.conf)

### Identificación
```hocon
UNIVERSE_COUNTRY_ID = "MX"
  Descripción: País único para esta ejecución
  Valores: "MX", "ES", etc.
  Ubicación: Environment variable o application.conf
  Criticidad: 🔴 Crítica (determina geografía)
```

### Etiquetado
```hocon
LABELS_PARAM {
  LABELS_TO_APPLY = ["ELEC", "GAS", "SOLAR"]
    Descripción: Etiquetas a evaluar
    Tipo: List[string]
    Si vacío: NO se etiqueta nada
    Criticidad: 🔴 Crítica (filtro principal)
  
  LABELS_TO_SKIP = []
    Descripción: Etiquetas a ignorar (opcional)
    Tipo: List[string]
    Criticidad: 🟡 Baja
}
```

### Spark
```hocon
SPARK_CONFIG {
  app.name = "kpfm_ho_cs_fou_gdh_pys_movementstagger"
    Descripción: Nombre del app Spark
    
  sql.adaptive.enabled = true
    Descripción: Adaptive Query Execution (optimización)
    
  spark.sql.adaptive.skewJoin.enabled = true
    Descripción: Manejo automático de skew
    
  spark.default.parallelism = 200
    Descripción: Particiones paralelas (tunable)
}
```

### Datos de Entrada
```hocon
INPUT {
  TABLE = "t_kpfm_movements"
    Descripción: Tabla origen de movimientos
    
  FILTER_DATE = "2026-06-01"
    Descripción: Fecha de corte (opcional)
}
```

### Datos de Salida
```hocon
OUTPUT {
  TABLE = "t_kpfm_movements_tagged"
    Descripción: Tabla destino (etiquetada)
    
  MODE = "overwrite"
    Valores: "overwrite", "append", "ignore"
    
  PARTITION_BY = ["g_inf_universe_country_id"]
    Descripción: Columnas de partición
}
```

---

## 🗺️ Configuración por Geografía

### Ubicación
```
resources/config/geography_config/{COUNTRY}_config.conf
```

### Estructura Típica
```hocon
# Para MX
labels {
  # Sobrescribe global si aplica
  ELEC {
    condition = "amount > 500"  # Diferente a global
  }
  
  # Agregados específicos MX
  MX_SPECIAL {
    label_id = "MX_SPECIAL"
    condition = "product_type == 'MX_ONLY'"
    scope = "MOVEMENT"
  }
}
```

### Precedencia
```
1. {country}_config.conf (mayor precedencia)
2. labels.conf (global)
```

---

## 🏷️ Definición de Etiquetas (labels.conf)

### Estructura
```hocon
labels {
  LABEL_ID {
    label_id = "LABEL_ID"
    label_name = "Nombre Legible"
    condition = "logical_expression"
    module = "custom_module"
    scope = "MOVEMENT"
    status = "active"
  }
}
```

### Campos
| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `label_id` | string | ✅ | Identificador único |
| `label_name` | string | ❌ | Nombre humano |
| `condition` | string | ⭕ | Expresión Spark (XOR con module) |
| `module` | string | ⭕ | Función custom (XOR con condition) |
| `scope` | string | ❌ | "MOVEMENT", "ACCOUNT", etc (default: MOVEMENT) |
| `status` | string | ❌ | "active", "inactive" (default: active) |

### Ejemplo Completo
```hocon
labels {
  ELEC {
    label_id = "ELEC"
    label_name = "Electricity Bill"
    condition = "amount > 1000 AND product_type == 'ELECTRICITY'"
    scope = "MOVEMENT"
    status = "active"
  }
  
  GAS {
    label_id = "GAS"
    label_name = "Gas Bill"
    condition = "product_type == 'GAS' OR service_type == 'GAS'"
    scope = "MOVEMENT"
  }
  
  CUSTOM_RULE {
    label_id = "CUSTOM"
    module = "kpfm_ho_cs_fou_gdh_pys_movementstagger.business.labels.custom_labeler"
    scope = "ACCOUNT"
    status = "inactive"  # No se evalúa
  }
}
```

---

## ⚙️ Placeholders HOCON

### Sintaxis
```hocon
${clave}                # Valor requerido (falla si no existe)
${?clave}               # Valor opcional (null si no existe)
${clave.sub.clave}      # Anidado
${?clave:-default}      # Opcional con fallback (PyHOCON extendido)
```

### Ejemplo
```hocon
LABELS_PARAM {
  LABELS_TO_APPLY = ${?LABELS_TO_APPLY_ENV}  # De env variable
  LABELS_TO_SKIP = []
}

OUTPUT {
  TABLE = ${OUTPUT_TABLE}           # Requerido
  MODE = ${?OUTPUT_MODE:-overwrite}  # Opcional, default "overwrite"
}
```

---

## 🔗 Cómo se Cargan (PySpark + PyHOCON)

### Orden de Carga
```
1. application.conf (base)
   ↓
2. {country}_config.conf (sobrescribe claves país)
   ↓
3. Environment variables (sobrescribe todo)
   ↓
4. Runtime parameters (CLI args - máxima precedencia)
```

### Código Típico
```python
from pyhocon import ConfigFactory
from io import StringIO

# Cargar base
config_base = ConfigFactory.parse_file("resources/application.conf")

# Cargar geografía
country = os.getenv("UNIVERSE_COUNTRY_ID", "MX")
config_geo = ConfigFactory.parse_file(
    f"resources/config/geography_config/{country}_config.conf"
)

# Merge (geografía sobrescribe base)
config_merged = ConfigFactory.merge([config_base, config_geo])

# Acceder
country_id = config_merged.get("UNIVERSE_COUNTRY_ID")
labels_to_apply = config_merged.get("LABELS_PARAM.LABELS_TO_APPLY", [])
```

---

## ❌ Anti-Patrones Configuración

| ❌ Incorrecto | ✅ Correcto |
|---------------|-----------|
| `rules` clave | `labels` clave |
| `rule_id` en etiqueta | `label_id` en etiqueta |
| Hardcoded país | `UNIVERSE_COUNTRY_ID` env var |
| `enabled` flag como control | `LABELS_TO_APPLY` list |
| Múltiples fuentes de verdad | Single HOCON config |
| Sobrescribir sin merge | `ConfigFactory.merge()` |

---

## 🔍 Validación de Configuración

### Lo que el Project Valida

✅ **ConfigReader validaciones:**
- `UNIVERSE_COUNTRY_ID` no es null
- `LABELS_PARAM.LABELS_TO_APPLY` es list
- `labels.conf` tiene `label_id` para cada etiqueta
- No hay duplicados de `label_id`

❌ **Lo que NO valida (responsabilidad de user):**
- Si `condition` es sintaxis Spark válida
- Si `module` existe y es importable
- Si `scope` value es esperado

### Cómo Validar Manualmente
```python
from kpfm_ho_cs_fou_gdh_pys_movementstagger.io.config import ConfigReader

reader = ConfigReader()
config = reader.load_config()  # Lanza exception si inválido

# Acceso seguro
labels_to_apply = config.labels_param.labels_to_apply
active_rules = reader.get_active_rules()
```

---

## 🚀 Ejemplos Completos

### Escenario 1: México con 3 etiquetas
```hocon
# application.conf
UNIVERSE_COUNTRY_ID = "MX"
LABELS_PARAM.LABELS_TO_APPLY = ["ELEC", "GAS", "MX_SPECIFIC"]

# MX_config.conf
labels {
  MX_SPECIFIC {
    label_id = "MX_SPECIFIC"
    condition = "g_inf_universe_country_id == 'MX'"
  }
}
```

### Escenario 2: España sin etiquetas (debug)
```hocon
# application.conf
UNIVERSE_COUNTRY_ID = "ES"
LABELS_PARAM.LABELS_TO_APPLY = []  # Vacío = NO etiqueta
```

### Escenario 3: Configuración por Environment
```bash
# CLI
export UNIVERSE_COUNTRY_ID="MX"
export LABELS_TO_APPLY_ENV="ELEC,GAS"
spark-submit worker.py

# En config:
LABELS_PARAM.LABELS_TO_APPLY = ${?LABELS_TO_APPLY_ENV}
```

---

## 📚 Referencias Rápidas

| Clave | Tipo | Criticidad | Ubicación |
|-------|------|-----------|-----------|
| `UNIVERSE_COUNTRY_ID` | string | 🔴 | app.conf |
| `LABELS_PARAM.LABELS_TO_APPLY` | list | 🔴 | app.conf |
| `condition` | string | 🔴 | labels.conf |
| `scope` | string | 🟡 | labels.conf |
| `status` | string | 🟢 | labels.conf |

**🔴 Crítica:** Rompe job si mal configurado
**🟡 Importante:** Afecta comportamiento
**🟢 Baja:** Información o metadata

---

## 🔧 Debug: Cómo Verificar Configuración

```python
# En worker.py o app.py
from kpfm_ho_cs_fou_gdh_pys_movementstagger.io.config import ConfigReader

reader = ConfigReader()
config = reader.load_config()

print(f"Country: {config.universe_country_id}")
print(f"Labels to apply: {config.labels_param.labels_to_apply}")
print(f"Active rules: {len(reader.get_active_rules())}")

# Output esperado:
# Country: MX
# Labels to apply: ['ELEC', 'GAS', 'SOLAR']
# Active rules: 3
```

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026
**Complemente con:** [[NOMENCLATURA]]

