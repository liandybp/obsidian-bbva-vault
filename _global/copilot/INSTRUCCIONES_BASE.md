# 🌍 Instrucciones Globales - GitHub Copilot

**Reglas y standards que TODOS los proyectos respetan**

**Versión:** 1.1
**Fecha:** 29 Junio 2026
**Actualizado:** 22 Julio 2026
**Aplicable a:** Todos los proyectos en este vault

---

## 🔗 Acceso al Vault de Obsidian (Obligatorio)

Copilot tiene acceso de lectura/escritura a todos los archivos `.md` del vault Obsidian:

- **Ruta base:** `/Users/t022458/Documents/BBVA_vault/`
- **Capacidad:** Leer, editar, crear archivos `.md` usando rutas absolutas
- **Patrón recomendado:** Mantener documentación actualizada en cada sesión
- **NO automático:** No sincronizo automáticamente, requiero instrucción explícita o checkpoints
- **Documentación:** Ver [[_global/copilot/ACCESO_VAULT_SETUP]] para setup en nuevos proyectos
- **Verificación:** Ver [[_global/copilot/VERIFICACION_ACCESO_VAULT]] (checklist)

### Cómo Copilot actualiza el vault:
```
1. Leo documento actual (ej. CAMBIOS_RECIENTES.md)
2. Edito/creo sección nueva con cambios de sesión
3. Actualizo [[ Wikilinks ]] internas del vault
4. Valido que no hay links rotos
```

### Para cada proyecto (replicar estructura):
```
~/Documents/BBVA_vault/UUAA/proyectos/PROYECTO/
├── README.md
├── ESTADO.md
├── QUICK_REFERENCE.md
├── contexto/
│   ├── COPILOT_INSTRUCTIONS.md  ← Instrucciones del proyecto
│   ├── CAMBIOS_RECIENTES.md     ← Sesiones y decisiones
│   ├── ARQUITECTURA.md
│   └── AUDITORIA_VAULT_*.md
├── referencias/
├── decisiones/
└── docs/
```

---

## 📅 Regla Global de Daily Note (obligatoria)

Al final de cada sesion, se debe actualizar una daily note por fecha en:
- `[[daily/YYYY-MM-DD]]`

Reglas:
- Una sola note por dia (no crear una por proyecto).
- Si en el mismo dia se trabajaron varios proyectos, registrar todos en la misma daily note.
- La daily note no reemplaza los updates del proyecto (`ESTADO`, `CAMBIOS_RECIENTES`); los complementa.

Minimo contenido por cierre de sesion:
- proyectos trabajados en el dia
- resumen breve por proyecto
- archivos o modulos tocados
- tests ejecutados/pendientes
- bloqueos y proximos pasos

---

## 📋 Nivel 1: Principios Fundamentales

### 1. Coherencia Código
```
✅ Sigue convenciones del proyecto (no global)
✅ Mantén cambios pequeños y enfocados
✅ Funciones puras, inputs claros, outputs predecibles
✅ Evita side-effects globales
✅ Docstrings en funciones públicas
```

### 2. Validación
```
✅ Validar inputs en capas de config/schemas
✅ Lanzar excepciones claras con contexto
✅ NO silenciar errores
✅ Loguear información útil para debug
```

### 3. Testing
```
✅ Test para lógica crítica (100% coverage core)
✅ Tests nombrados descriptivos
✅ Fixtures reutilizables
✅ Setup/teardown limpio
✅ Mock solo lo necesario (evita over-mocking)
```

### 4. Documentación
```
✅ Docstrings sobre cómo y por qué
✅ Ejemplos en documentación
✅ Links a decisiones arquitectónicas
✅ Mantener actualizado con cambios
✅ No documentar obvio (código limpio es mejor que comentarios vagos)
```

### 5. Dependencias
```
✅ Justificar cada dependencia nueva
✅ Prefer libraries establecidas y mantenidas
✅ Versionado exacto (no ^, no ~)
✅ Documentar por qué se eligió cada dependencia
✅ Revisar regularidad cambios en dependencies
```

---

## 📝 Nivel 2: Convenciones Nombres

### Archivos
```python
# Python
✅ lowercase_with_underscores.py
❌ CamelCase.py
❌ UPPERCASE.py

# Markdown
✅ DESCRIPTIVE_NAME.md
❌ file.md
❌ temp.md
```

### Variables
```python
✅ user_count = 42
✅ is_active = True
✅ config_data = {...}

❌ uc = 42
❌ _x = True
❌ d = {...}
```

### Constantes
```python
✅ MAX_RETRIES = 3
✅ DEFAULT_TIMEOUT = 30
✅ API_BASE_URL = "..."

❌ max_retries = 3
❌ TIMEOUT = 30
```

### Funciones
```python
✅ def calculate_total_price(items):
✅ def is_valid_email(email):
✅ def fetch_user_data(user_id):

❌ def calc():
❌ def validate(x):
❌ def get():
```

### Clases
```python
✅ class UserRepository:
✅ class ConfigValidator:
✅ class APIClient:

❌ class user:
❌ class config_validator:
❌ class API:
```

---

## 🚫 Nivel 3: Anti-Patrones Globales

### ❌ NUNCA Hacer

#### 1. Global State Descontrolado
```python
# ❌ MALO
CONFIG = None  # Global cambios en runtime

# ✅ BUENO
# Pasar config como argumento o inyectar
def process(config: Config) -> Result:
    pass
```

#### 2. Magic Numbers/Strings
```python
# ❌ MALO
if user.age > 18:  # ¿Por qué 18?
    grant_access()

# ✅ BUENO
MIN_AGE_FOR_ACCESS = 18
if user.age > MIN_AGE_FOR_ACCESS:
    grant_access()
```

#### 3. Exceptions Genéricas
```python
# ❌ MALO
except Exception:
    continue

# ✅ BUENO
except SpecificError as e:
    logger.error(f"Context specific: {e}")
    raise CustomError(f"What I was doing: {e}") from e
```

#### 4. Comments Engañosos
```python
# ❌ MALO
x = y + 1  # increment x

# ✅ BUENO
# increment counter to track attempts
attempt_counter = previous_attempts + 1
```

#### 5. Large Monolith Functions
```python
# ❌ MALO
def process_entire_flow():
    # 200 líneas
    # paso 1, paso 2, paso 3...
    pass

# ✅ BUENO
def process_entire_flow():
    step1_result = validate_input()
    step2_result = transform_data(step1_result)
    return persist_result(step2_result)
```

#### 6. Circular Imports
```python
# ❌ MALO
# module_a imports module_b
# module_b imports module_a

# ✅ BUENO
# Reorganizar dependency tree
# Usar interfaces/abstractions
```

#### 7. Hardcoded Values en Código
```python
# ❌ MALO
database_url = "postgres://localhost:5432/prod_db"

# ✅ BUENO
database_url = os.getenv("DATABASE_URL")
assert database_url, "DATABASE_URL env var required"
```

---

## 🧪 Nivel 4: Testing Standards

### Coverage Mínimo
```
✅ Core business logic: 100%
✅ Config/Schemas: 90%+
✅ Utils/Helpers: 80%+
✅ No test "happy path only", incluir edge cases

❌ Test solo para decir "tengo tests"
❌ Test que no validan nada (mocking incorrecto)
❌ Test que dependen de ejecución anterior
```

### Estructura Test
```python
def test_specific_behavior_produces_expected_result():
    # Arrange: Setup data
    input_data = create_test_data()
    
    # Act: Execute
    result = function_under_test(input_data)
    
    # Assert: Verificar
    assert result.property == expected_value
    assert result.another == other_value
```

### Nombres Descriptivos
```python
✅ test_merge_preserves_created_date_when_no_change()
✅ test_config_raises_error_if_required_key_missing()
✅ test_empty_input_returns_empty_array()

❌ test_merge()
❌ test_config()
❌ test_empty()
```

---

## 📚 Nivel 5: Documentación

### Docstrings Requeridos
```python
# ✅ COMPLETO
def calculate_tax(amount: float, tax_rate: float) -> float:
    """
    Calculate tax amount on given amount.
    
    Args:
        amount: Total amount to calculate tax on
        tax_rate: Tax percentage (0-100)
    
    Returns:
        Tax amount as float
    
    Raises:
        ValueError: If tax_rate is not 0-100
    
    Example:
        >>> calculate_tax(100, 10)
        10.0
    """
    if not 0 <= tax_rate <= 100:
        raise ValueError(f"tax_rate must be 0-100, got {tax_rate}")
    return amount * (tax_rate / 100)
```

### README Mínimo por Proyecto
```markdown
# [Proyecto]

One-liner descripción

## Quick Start
- Stack: [tecnologías]
- Setup: [cómo empezar]
- Test: [comando]

## Architecture
[Diagrama o link a decisiones]

## Contributing
[Guía]
```

### Markdown en Obsidian
```markdown
# Heading 1        ← Solo uno por archivo
## Heading 2       ← Usa cuando cambies tema
### Heading 3      ← Subtemas

**Bold** para énfasis
`code` para inline code
[[link]] para referencias internas
```

---

## 🔧 Nivel 6: Herramientas Recomendadas

### Linting + Formatting
```bash
# Python
black .                    # Auto-format
flake8 .                   # Lint
isort .                    # Organiza imports

# TypeScript
prettier --write .
eslint --fix .
```

### Pre-commit Hooks
```bash
# Auto-run linters antes de commit
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

### Type Hints
```python
# ✅ SIEMPRE en funciones públicas
def fetch_user(user_id: int) -> User:
    pass

def process_batch(items: List[Item]) -> Dict[str, Any]:
    pass
```

---

## 📊 Nivel 7: Git Workflow

### Commits Claros
```bash
# ✅ BUENO
git commit -m "feat: Add user authentication

- Implement JWT tokens
- Add login endpoint
- Include unit tests"

# ❌ MALO
git commit -m "updates"
git commit -m "WIP"
git commit -m "fix bug"
```

### Branches
```bash
✅ feature/user-auth
✅ fix/memory-leak
✅ docs/api-guide
✅ refactor/config-loading

❌ f/auth
❌ fix
❌ temp
```

### Pre-push Checklist
```bash
[ ] Tests pass locally: pytest -q
[ ] No linting errors: flake8 .
[ ] Commits are clear
[ ] Branch is up to date: git pull origin main
[ ] Push: git push origin [branch]
```

---

## 📈 Nivel 8: Code Review Checklist

### Como Autor (Before Asking Review)
```
[ ] Tests added/updated
[ ] Code linted and formatted
[ ] Docstrings complete
[ ] No debug prints left
[ ] Commits organized logically
[ ] PR description clear
```

### Como Reviewer
```
[ ] Tests cover behavior (not just lines)
[ ] No obvious bugs or security issues
[ ] Follows project standards
[ ] Docstrings are clear
[ ] Performance implications considered
[ ] Discuss, don't criticize person
```

---

## 🎯 Nivel 9: Project Lifecycle

### Start New Feature
```
1. Create branch: feature/[name]
2. Write tests FIRST (TDD)
3. Implement code
4. Verify all tests pass
5. Update docs
6. Create PR
```

### Fix Bug
```
1. Create branch: fix/[issue-number]
2. Write test that reproduces bug
3. Fix code
4. Verify test passes
5. Ensure no regressions
6. Create PR
```

### Refactor
```
1. Create branch: refactor/[what]
2. Keep tests passing (red/green/refactor)
3. Don't mix refactor + feature changes
4. Verify nothing broke
5. Create PR
```

### Documentation
```
1. Create branch: docs/[topic]
2. Update/create docs
3. Link from main README
4. Add to Obsidian vault
5. Create PR
```

---

## 🌐 Nivel 10: Colaboración Multi-Proyecto

### Cuando Proyecto A Usa Code de Proyecto B
```
✅ Extraer librería separada
✅ Documentar la dependencia
✅ Versionado semántico
✅ Actualizar ambos vaults cuando sea relevante

❌ Copy-paste código
❌ Dependencias ocultas
❌ Tight coupling
```

### Comunicación Entre Equipos
```
✅ Links Obsidian: [[proyectos/proyecto-a/decisiones/X]]
✅ Decisiones documentadas y accesibles
✅ API contracts claros
✅ Versionado de cambios incompatibles

❌ "Slack, ask me"
❌ Cambios sin aviso
❌ Acoplamiento tight
```

---

## 📚 Referencias

### Cuando leas estas reglas
```
- Guarda en bookmark: [[_global/copilot/INSTRUCCIONES_BASE]]
- Referencia regularidad: mis proyectos las siguen?
- Contribuye mejoras: encuentra mejor patrón? Agrega acá
```

### Por Proyecto
```
- Tu proyecto extiende: proyectos/[proyecto]/contexto/COPILOT_INSTRUCTIONS.md
- Linkea de vuelta: [[_global/copilot/INSTRUCCIONES_BASE]]
```

---

## 🔄 Evolución

### Cuando Agregas Regla Nueva Global
```
1. Edita: _global/copilot/INSTRUCCIONES_BASE.md
2. Commit: git commit -m "global: Add rule for X"
3. Push: git push
4. Todos proyectos refieren via links (auto-actualizados)
5. Sin cambios manuales necesarios
```

### Cuando Una Regla No Aplica a Un Proyecto
```
# En proyectos/[proyecto]/contexto/COPILOT_INSTRUCTIONS.md
## Excepciones a Globales
- EXCEPTO: [[_global/copilot/INSTRUCCIONES_BASE#heading]]
  Razón: [Por qué no aplica este proyecto]
```

---

## 📁 Instrucciones Específicas por Proyecto

Cada proyecto tiene su propio archivo `.github/copilot-instructions.md` en el repositorio y `contexto/COPILOT_INSTRUCTIONS.md` en el vault.

**Importante**: Estas instrucciones específicas EXTIENDEN las globales. Primero aplica globales, luego aplica las específicas del proyecto.

### KAGR - lib-agregador-github

**Ubicaciones:**
- Repositorio: `/Users/t022458/PycharmProjects/kagr/lib/lib_agregador_github/.github/copilot-instructions.md`
- Vault: `[[kagr/proyectos/lib-agregador-github/contexto/COPILOT_INSTRUCTIONS]]`

**Tipo:** Librería reutilizable (NO engine)

**Reglas clave:**
- ✅ Lógica centralizada, sin configuración
- ✅ Agnóstica a deployments
- ❌ NO hardcodear rutas
- ✅ Catálogos vienen del engine
- ✅ Mantener bajo acoplamiento
- ✅ Documentar breaking changes

**Cuando trabajas con este proyecto:**
1. Lee: `[[_global/copilot/INSTRUCCIONES_BASE]]` (globales)
2. Lee: `.github/copilot-instructions.md` (específicas)
3. Revisa: `[[kagr/proyectos/lib-agregador-github/contexto/ARQUITECTURA]]`
4. Consulta: `[[kagr/proyectos/lib-agregador-github/decisiones/ARQUITECTURA_LIBRERIA_ENGINES]]`

### KPFM - etiquetas

**Ubicaciones:**
- Repositorio: `/Users/t022458/PycharmProjects/kpfm/lib/etiquetas/.github/copilot-instructions.md`
- Vault: `[[kpfm/proyectos/kpfm-etiquetas/contexto/COPILOT_INSTRUCTIONS]]`

**Tipo:** Job consumidor de lib-agregador-github

**Reglas clave:**
- Sigue reglas de KAGR (es consumidor)
- Además: configuración específica de KPFM
- Además: geografía-específico
- Además: labels por país

### Patrón: Agregar Nuevo Proyecto

Cuando agregues un proyecto nuevo:

1. **En el repositorio:**
   - Crear `.github/copilot-instructions.md`
   - Base: copiar template y adaptar

2. **En el vault:**
   - Crear carpeta en UUAA: `UUAA/proyectos/[proyecto]/`
   - Crear: `README.md`, `ESTADO.md`
   - Crear: `contexto/COPILOT_INSTRUCTIONS.md`
   - Crear: `contexto/ARQUITECTURA.md` (si es relevante)

3. **En instrucciones:**
   - Registrar proyecto aquí en esta sección
   - Documentar qué hace diferente
   - Enlazar a vault

---

## ✅ Checklist: ¿Sigo las Reglas?

- [ ] Mi código tiene docstrings
- [ ] Las funciones son pequeñas y enfocadas
- [ ] Los nombres de variables son descriptivos
- [ ] Tengo tests para lógica crítica
- [ ] No hay valores hardcoded (use config)
- [ ] Los commits tienen mensajes claros
- [ ] No hay imports circulares
- [ ] Las excepciones son específicas
- [ ] La documentación está actualizada
- [ ] Linkeé a decisiones arquitectónicas si es relevante

---

## 🎓 Próximo Paso

- Leer en detalle la sección que te aplique
- Consultar cuando dudes
- Reportar mejoras sugeridas

---

**Versión:** 1.1
**Fecha:** 30 Junio 2026
**Última Actualización:** 30 Junio 2026 (agregados proyectos específicos)
**Aplicable a:** Todos los proyectos
**Linking desde:** Cada proyecto's COPILOT_INSTRUCTIONS.md
