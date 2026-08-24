# Spark Functions - Referencia Rápida

**Funciones PySpark usadas frecuentemente en este proyecto**

---

## 🔄 Transformación de Arrays

### `f.transform(column, function)`
Aplica una función a cada elemento de un array.

**Sintaxis:**
```python
f.transform(f.col("array_column"), lambda element: expression)
```

**Ejemplo en proyecto:**
```python
# Convertir array de tags a map indexado por código
df_prior_map = (
    df_input.select(mov_id, f.col(tags_col).alias("_prior_array"))
    .withColumn(prior_col,
        f.map_from_entries(
            f.transform(f.coalesce(f.col("_prior_array"), f.array()),
                lambda t: f.struct(
                    t[GF_PFM_TAG_CODE.name].alias("key"), 
                    t.alias("value")
                )
            )
        )
    )
)
```

**Lo que hace:**
- `lambda t: ...` - Para cada tag `t` en el array
- `f.struct(t[...].alias("key"), ...)` - Crea tuple (code, tag_struct)
- `f.map_from_entries()` - Convierte array de tuples → map

---

### `f.array(element1, element2, ...)`
Crea un array literal.

**Sintaxis:**
```python
f.array(f.lit("ELEC"), f.lit("GAS"))  # Retorna ["ELEC", "GAS"]
```

**En proyecto:**
```python
# Retornar array vacío si no hay etiquetas previas
f.coalesce(f.col("_prior_array"), f.array())
```

---

### `f.concat(array1, array2, ...)`
Concatena dos o más arrays.

**Sintaxis:**
```python
new_tags_array = f.concat(new_tags_col, non_reevaluated_tags_col)
```

**En proyecto:**
```python
# Mezclar etiquetas nuevas con no reevaluadas
result = (
    df_new
    .join(df_input.select(mov_id, non_reeval_col), on=mov_id, how="left")
    .withColumn(
        result_tag_col,
        f.concat(f.coalesce(f.col(tag_col), f.array()),
                 f.coalesce(f.col(non_reeval_col), f.array()))
    )
)
```

---

### `f.array_contains(array_column, value)`
Verifica si un array contiene un elemento.

**Sintaxis:**
```python
f.array_contains(f.col("codes"), "ELEC")  # Retorna true/false
```

**En proyecto:**
```python
# Verificar si etiqueta ya fue evaluada antes
contains_code = f.array_contains(
    f.transform(f.col(tags_col), lambda t: t[GF_PFM_TAG_CODE.name]),
    new_tag[GF_PFM_TAG_CODE.name]
)
```

---

### `f.filter(array_column, function)`
Filtra elementos de un array.

**Sintaxis:**
```python
f.filter(f.col("array"), lambda x: expression)
```

**En proyecto:**
```python
# Obtener solo tags no-reevaluados (no aparecen en nuevas)
non_reevaluated = f.filter(
    f.col(tags_col),
    lambda t: ~f.array_contains(
        f.col(new_codes_map_col),
        t[GF_PFM_TAG_CODE.name]
    )
)
```

---

## 🗺️ Manejo de Maps

### `f.map_from_entries(array_of_tuples)`
Convierte array de `[key, value]` pares → Map.

**Sintaxis:**
```python
f.map_from_entries(f.array(
    f.struct(f.lit("k1").alias("key"), f.lit("v1").alias("value")),
    f.struct(f.lit("k2").alias("key"), f.lit("v2").alias("value"))
))
# Resultado: {"k1": "v1", "k2": "v2"}
```

**En proyecto:**
```python
# Indexar tags por código para búsqueda O(1)
prior_map = f.map_from_entries(
    f.transform(f.col("_prior_array"),
        lambda t: f.struct(
            t[GF_PFM_TAG_CODE.name].alias("key"),  # Clave: código
            t.alias("value")                         # Valor: struct tag
        )
    )
)
# Resultado: {"ELEC": {struct}, "GAS": {struct}}
```

---

## 🏗️ Estructuras (Structs)

### `f.struct(col1, col2, ...)`
Crea una columna struct desde columnas existentes.

**Sintaxis:**
```python
f.struct(
    f.col("code").alias("tag_code"),
    f.col("date").alias("tag_date"),
    f.lit(True).alias("visible")
)
```

**En proyecto:**
```python
# Crear nuevo tag struct con todos los campos
new_tag_struct = f.struct(
    new_tag[GF_PFM_TAG_CODE.name].alias(GF_PFM_TAG_CODE.name),
    new_tag[GF_PFM_TAG_CREATED_DATE.name].alias(GF_PFM_TAG_CREATED_DATE.name),
    f.current_timestamp().alias(GF_PFM_TAG_UPDATED_DATE.name),  # ← Cambió
    # ... resto de campos
)
```

---

## ❓ Condicionales

### `f.when(condition, value).otherwise(alt_value)`
Expresión IF/ELIF/ELSE distribuida.

**Sintaxis:**
```python
f.when(condition, value)
    .when(condition2, value2)
    .otherwise(default_value)
```

**Importante:** Las condiciones son Spark Columns (no Python bools).

**En proyecto:**
```python
# Resolver cada tag nuevo: nuevo, sin cambio, o cambio de visibilidad
resolved_tag = (
    f.when(~prior_exists, new_tag)                  # NO existía: agregar
        .when(visibility_changed, updated_tag)      # SÍ cambió: actualizar
        .otherwise(prior_tag)                       # NO cambió: preservar
        .cast(TAG_ENTRY_SCHEMA)
)

# Nota: `visibility_changed = prior.visible != new.visible`
# NO ES un bool de Python, es un Column expression
```

**Error común:**
```python
# ❌ INCORRECTO
visible_changed = prior_tag[VISIBLE.name] != new_tag[VISIBLE.name]
if visible_changed:  # TypeError: Expected Column, got bool
    ...

# ✅ CORRECTO
visible_changed = prior_tag[VISIBLE.name] != new_tag[VISIBLE.name]
result = f.when(visible_changed, ...)
```

---

## 💾 Operaciones de Datos

### `f.col(column_name)`
Referencia a columna por nombre.

**Sintaxis:**
```python
f.col("gf_pfm_tag_code")
```

---

### `f.lit(value)`
Crea columna literal (constante).

**Sintaxis:**
```python
f.lit(True)           # Constante boolean
f.lit("ELEC")         # Constante string
f.lit(42)             # Constante número
```

---

### `f.current_timestamp()`
Retorna timestamp actual de Spark (momento ejecución).

**Sintaxis:**
```python
f.current_timestamp()  # timestamp type
```

**En proyecto:**
```python
# Fijar updated_date cuando visibilidad cambió
RuleEngine._execution_tag_date()  # Wrapper centralizado
```

---

### `f.coalesce(col1, col2, ...)`
Retorna primer valor no-null.

**Sintaxis:**
```python
f.coalesce(f.col("option1"), f.col("option2"), f.lit("default"))
```

**En proyecto:**
```python
# Si array previo es null, usar array vacío
f.coalesce(f.col("_prior_array"), f.array())
```

---

## 🔗 Casting y Conversiones

### `.cast(target_type)`
Convierte columna a otro tipo.

**Sintaxis:**
```python
f.col("tag_struct").cast(TAG_ENTRY_SCHEMA)
```

**En proyecto:**
```python
# Asegurar struct tiene todos los campos en orden correcto
resolved_tag = (
    f.when(...)
        .otherwise(...)
        .cast(TAG_ENTRY_SCHEMA)  # Validar schema
)
```

---

## 📋 Funciones de Columna

### `withColumn(name, expression)`
Agrega/modifica columna en DataFrame.

**Sintaxis:**
```python
df.withColumn("new_col", f.expr(...))
```

**En proyecto:**
```python
df_prior_map = (
    df_input.select(...)
    .withColumn(prior_col, f.map_from_entries(...))
    .select(mov_id, prior_col)
)
```

---

## 🎯 Patrones Comunes en el Proyecto

### Patrón 1: Convertir Array → Map
```python
# Razón: Búsqueda O(1) por código en lugar de O(n)
tag_map = f.map_from_entries(
    f.transform(f.col(tags_col),
        lambda t: f.struct(
            t["code"].alias("key"),
            t.alias("value")
        )
    )
)
```

### Patrón 2: Condicional Distribuida Compleja
```python
# Razón: Spark ejecuta en Catalyst optimizer (no Python loop)
result = (
    f.when(condition1, value1)
        .when(condition2, value2)
        .when(condition3, value3)
        .otherwise(default)
        .cast(TargetSchema)
)
```

### Patrón 3: Array Concatenación Segura
```python
# Razón: Manejar nulls sin NullPointerException
df.withColumn(
    "result",
    f.concat(
        f.coalesce(f.col("arr1"), f.array()),
        f.coalesce(f.col("arr2"), f.array())
    )
)
```

---

## ⚡ Performance Tips

### ✅ Hacer
- Usar `f.map_from_entries()` + `[]` indexing → O(1)
- Usar `f.when()` en lugar de Pandas UDF → distribuido
- Usar `.cast()` explícitamente → esquema seguro
- Centralizar timestamps → consistencia

### ❌ No hacer
- Pandas UDF para lógica simple → overhead serialización
- Python loops sobre particiones → no distribuido
- Acceso lineal en arrays grandes → O(n)
- Timestamps diferentes por fila → inconsistencia

---

## 📚 Referencias Oficiales

- [PySpark Documentation - array functions](https://spark.apache.org/docs/latest/api/python/)
- [PySpark SQL Functions](https://spark.apache.org/docs/latest/sql-ref-functions.html)
- [Catalyst Optimizer](https://databricks.com/blog/2015/04/13/deep-dive-into-sparks-catalyst-optimizer.html)

---

**Versión:** 1.0
**Stack:** PySpark 3.x
**Última Actualización:** 29 Junio 2026

