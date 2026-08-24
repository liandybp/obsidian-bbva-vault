# Directrices BBVA — Ficheros de Control Repositorio (Aplicadas a Copilot)

> **Extraído de:** AI_FOUNDATION - Structuring & Enhancement - Movements - Directrices Qs Transformación.xlsx (BLOQUE_1 a BLOQUE_5, Q4-2025 → Q4-2026).
> **Adaptación Obsidian:** Julio 2026
> **Alcance:** Artefactos y ficheros de **documentación, gobierno y testing** que Copilot debe verificar y generar en cada repositorio nuevo que toque.
> **Excluda a propósito:** Buenas prácticas de código/SOLID/Clean Code (cobertas ya por equipo), pair programming, estrategias de despliegue.

---

## 0. Leyenda

| Campo | Significado |
|---|---|
| **Obligatoriedad** | `Debe` = obligatorio · `Debería` = recomendado fuertemente · `Puede` = opcional |
| **Bloque (Q)** | Trimestre en que la directriz entra en vigor según plan AIF |
| **Estado AIF** | Estado de implantación en Q4-2025 (⚠️ prioridad inversamente proporcional: "No iniciada" = candidata prioritaria) |
| **Código** | Identificador único de directriz para trazabilidad (ej. D65, D83) |

---

## 1. Cómo debe usarse este documento (flujo Copilot + Vault)

### 1.1 Lectura previa (cada sesión)
- Leer este documento desde Obsidian: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
- Verificar contexto actual del repo en sesión.
- Identificar stack, tipo (batch/online/IaC/librería), plataforma (EMR/Dataproc/SageMaker/GitLab).

### 1.2 Escaneo del repositorio
Para cada repositorio, comprobar la **existencia de cada fichero/artefacto** listado en secciones 2-6, en este orden de prioridad:
1. `Debe` con bloque **vencido** (Q4-2025, Q1-2026, Q2-2026) · **Máxima prioridad.**
2. `Debe` con bloque futuro.
3. `Debería` (recomendado fuertemente).
4. `Puede` (opcional).

### 1.3 Generación/enmienda
Si falta un fichero:
1. **Generar** contenido mínimo descrito en la sección correspondiente.
2. **Adaptar** al contexto real: lenguaje, tipo servicio, plataforma.
3. **Evitar** contenido de pruebas/código fuente per se; limitarse a **estructura, config y documentación**.
4. Si no aplica por naturaleza repo (ej. legacy_conf sin BBDD) → **anotar excepción** en README.

### 1.4 Documentación en Vault
Para cada repo:
- Crear entrada en `proyectos/<nombre-repo>/` si se activa sesión formal.
- Actualizar `ESTADO.md` con cumplimiento de directrices.
- Registrar excepções/pendientes en `contexto/CAMBIOS_RECIENTES.md`.

---

## 2. Ficheros de documentación y gobierno obligatorios

### 2.1 `README.md`
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D65 | Debe | Q4-2025 | Implementada (1.0) |
| D67 | Debería | Q4-2025 | En progreso (0.2) |
| D66 | Puede | Q4-2025 | No iniciada (0.0) |

**Contenido mínimo exigido:**
- ✅ Descripción clara **objetivo técnico** del código.
- ✅ **Propietario/equipo** responsable (contacto/Slack/DL).
- ✅ Estructura de directorios principales.
- ✅ Scripts útiles o comandos de arranque (`make help`, `./scripts/start.sh`).
- ✅ **Modelo de branching** explícito: `branching_model: "gitflow|trunk-based|scaled-tbd"`.
- ✅ Link a documentacion extendida (wiki, Confluence, docs/ folder).

### 2.2 `.gitignore` + `.gitattributes`
**NUEVO (Complementa D12-D14).**

| Obligatoriedad | Sugerencia |
|---|---|
| Debe | Generar según stack: Python (`.pyc`, `__pycache__/`, `venv/`), PySpark/Scala (`.jar`, `target/`), Terraform (`.tfstate`), etc. |

**Contenido:**
- ✅ Patrones sensibles: `*.key`, `*.pem`, `secrets/`, `.env*`.
- ✅ Artefactos build: `build/`, `dist/`, `*.egg-info`, `.cache`.
- ✅ Ficheros > 30 MB (versionado es cargo, usar Git-LFS si necesario).
- ✅ OS cruft: `.DS_Store`, `Thumbs.db`.

### 2.3 `CHANGELOG.md`
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D68 | Debería | Q4-2025 | En progreso (0.6) |

**Contenido:**
- ✅ Formato Markdown, idealmente **generado automáticamente** (CI/CD hook desde commits/tags).
- ✅ "Changelog note" (consumo técnico) separado de *Release Note* (usuario final).
- ✅ Historial versionado: v1.0.0, v0.9.1, etc.

### 2.4 `CONTRIBUTING.md` (InnerSource)
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D83, D86, D87, D92 | Debe | Q1-2026 | No iniciada (0.0) ⚠️ |

**Contenido:**
- ✅ Procedimiento para **otros equipos abran issues**.
- ✅ Modelo de Pull Request (referencia issue, estructura commit, checklist).
- ✅ Setup local mínimo para contribuidores externos.
- ✅ Contacto/owner equipo para cuestiones de diseño.

### 2.5 Metadatos del Repositorio
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D3 | Debe | Q4-2025 (VENCIDO) | No iniciada (0.0) ⚠️⚠️ |

**Contenido (uno de estos formatos):**
- `metadata.yml` en raíz:
  ```yaml
  repo_name: kpfm-etiquetas
  owner_team: DataEngineering
  owner_email: data-eng@bbva.com
  description: PySpark tagging job for movements
  type: batch  # batch|online|library|iac
  stack: [Python, PySpark, Pydantic, PyHOCON]
  branching_model: gitflow
  platform: EMR  # EMR|Dataproc|SageMaker|Athena|GitLab
  ```
- O sección dedicada en README.
- O Kaafile/equivalente si plataforma KAA define metadatos.

> 🔴 **ALTA PRIORIDAD:** Esta es de las con menor implantación (No iniciada). Candidata prioritaria escaneo.

### 2.6 Release Notes (si libera funcionalidad a usuarios finales)
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D144, D145, D146 | Debe | Q4-2026 | No iniciada (0.0) |

**Estructura obligatoria:**
- ✅ Título, Fecha, Versión.
- ✅ Resumen ejecutivo (30 palabras max).
- ✅ Mejoras (user-facing, sin jerga técnica).
- ✅ Correcciones de bugs.
- ✅ ⚠️ **Cambios no retrocompatibles** (con ejemplos = CRÍTICO).
- ✅ Instrucciones instalación/upgrade.
- ✅ Contacto soporte.

---

## 3. Ficheros de configuración, IaC y dependencias

### 3.1 Configuración versionada
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D59-D64 | Debe | Q1-2026 | No iniciada (0.0) ⚠️ |

**Verificar:**
- ✅ Ficheros config en repositorio (desacoplados del código aplicativo).
- ✅ **Secretos NO versionados:** residir en Vault (gestor centralizado), referenciado solo.
- ✅ Cambios manuales infrecuentes documentados/justificados (comentario inline + CONTRIBUTING.md).
- ✅ Carpeta `resources/config/` (o `config/`) con estructura clara por geografía/entorno.

### 3.2 Infraestructura como Código (IaC)
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D11 | Debe | Q4-2025 | Implementada (1.0) |

**Aplica si repo despliega infra:**
- ✅ Scripts construcción, data seed, IaC (Terraform/CloudFormation/CDK).
- ✅ Versionados junto código aplicativo.
- ✅ Documentación deployment (README o docs/DEPLOYMENT.md).

### 3.3 Gestión de dependencias
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D53, D54, D55 | Debe | Q4-2025 | En progreso (0.5) |

**Generar/verificar:**
- ✅ **Lockfile** con versiones fijadas:
  - Python: `requirements.txt` (con `==`), `poetry.lock`, o `pyproject.toml` con versiones exactas.
  - Scala/Maven: `pom.xml` con versiones pineadas, `dependency-check` análisis.
- ✅ Evitar "abiertas" (rangos `^`, `~`) en releases productivas.
- ✅ Artefactos publicados: **inmutables**, no sobrescribir releases.

### 3.4 Scripts y control de esquema de base de datos
| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D75-D78, D84, D86 | Debe | Q1-2026 | No iniciada (0.0) ⚠️ |

**Si repo gestiona BBDD:**
- ✅ Scripts DDL/schema versionados en repo (`sql/`, `migrations/`).
- ✅ Versionado de schema asociado a versión código (CHANGELOG.md lo documenta).
- ✅ Plan update si cambio no retrocompatible (backup/rollback scripts).
- ✅ Tracking migración en pipeline CI/CD.

---

## 4. Exclusiones obligatorias (Anti-patrones a detectar)

| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D12, D13, D14 | Debe | Q4-2025 | En progreso (0.9) |

**Copilot debe reportar (ficheros que NO deberían estar):**
- 🚫 Secretos/contraseñas en bruto (`*.key`, `*.pem`, hardcoded tokens en config).
- 🚫 Binarios/artefactos generados: `.jar`, `.exe`, `.egg`, carpetas `build/`, `dist/`, `target/`, `__pycache__/`.
- 🚫 Ficheros gigante (> 30 MB): usar Git-LFS o excluir.

**Acción recomendada:**
- ✅ Revisar/generar `.gitignore` adecuado al stack.
- ✅ Ejecutar `git log --diff-filter=D` para detectar ficheros eliminados que podrían reaparecer.

---

## 5. Pipeline CI/CD como artefacto de control

| Código | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|
| D29, D95 | Debe | Q4-2025 | No iniciada (0.0) ⚠️ |

**Fichero de definición:** `Jenkinsfile`, `.gitlab-ci.yml`, `.github/workflows/*`, etc.

**Stages obligatorios antes merge o release:**
- ✅ **Lint + formateo** (black, flake8, eslint, etc.).
- ✅ **Tests unitarios** (mínimo 80% coverage critical paths).
- ✅ **Tests integrados** (mockeados/virtualizados o env test).
- ✅ **SAST** (SonarQube, Semgrep, Checkmarx).
- ✅ **TIA** (Test Impact Analysis) o smoke tests post-deploy.
- ✅ Intégración manual + automática en servidor de CI/CD.

**Output:** Reporte artefactos (cobertura, vulnerabilidades, scan resultados) accesible desde repo/wiki.

---

## 6. Testing — estructura y artefactos

> **Excluye deliberadamente:** Cómo escribir un buen test (aislamiento, mocks, datos). Solo: **estructura/artefacto verificable**.

| Código | Directriz | Obligatoriedad | Bloque | Estado AIF |
|---|---|---|---|---|
| D26 | Tests automatizados residen en repo de código (mismo repo code) | Debe | Q4-2025 | Implementada (1.0) |
| D8 | Requisitos funcionales con ID único, referenciados en tests (naming/tagging) | Debe | Q2-2026 | No iniciada (0.0) ⚠️ |
| D14 | Documentos Gherkin (BDD) para comportamientos críticos | Puede | Q2-2026 | No iniciada (0.0) |
| D46 | Tests integración verifican interacción con dependencias (API, BBDD) | Debe | Q2-2026 | No iniciada (0.0) ⚠️ |

**Verificar/generar:**
- ✅ Carpeta `tests/` o `test/` **dentro del repo** (no fuera, salvo E2E/monitoreo).
- ✅ Pipeline (sección 5) incluye stage **tests explícito**.
- ✅ Si requisitos con ID (Jira, Azure DevOps) → tests referencian ID en nombre/tags:
  ```python
  # Ejemplo
  @pytest.mark.requirement("KPFM-2345")
  def test_merge_preserves_created_date_when_no_visibility_change():
      ...
  ```
- ✅ Si equipo usa BDD → ficheros `.feature` (Gherkin) para escenarios críticos.

---

## 7. Tabla resumen priorizada (escaneo rápido por repo)

| Prioridad | Fichero/Artefacto | Obligatoriedad | Bloque | Estado AIF | Acción |
|---|---|---|---|---|---|
| 🔴 CRÍTICA | `metadata.yml` o sección README | Debe | Q4-2025 (vencido) | No iniciada | Generar inmediatamente |
| 🔴 CRÍTICA | `.gitignore` + `.gitattributes` | Debe | Q4-2025 | En progreso (0.9) | Completar |
| 🔴 CRÍTICA | `CONTRIBUTING.md` (InnerSource) | Debe | Q1-2026 (vencido) | No iniciada | Generar |
| 🔴 CRÍTICA | Ficheros config versionados (desacoplados de secretos) | Debe | Q1-2026 | No iniciada | Auditar + generar |
| 🔴 CRÍTICA | Pipeline CI/CD con stages lint + unit + SAST | Debe | Q4-2025 | No iniciada | Auditar Jenkinsfile/.gitlab-ci.yml |
| 🔴 CRÍTICA | Lockfile dependencias (versiones fijadas) | Debe | Q4-2025 | No iniciada | Generar requirements.txt|poetry.lock |
| 🔴 CRÍTICA | Scripts DDL/schema BBDD versionados (si aplica) | Debe | Q1-2026 | No iniciada | Generar migrations/ |
| 🟡 MEDIA | README.md con owner + branching_model | Debería | Q4-2025 | En progreso (0.2) | Enriquecer |
| 🟡 MEDIA | CHANGELOG.md automatizado | Debería | Q4-2025 | En progreso (0.6) | Implementar CI hook |
| 🟡 MEDIA | Tests con tagging requisito (tracking) | Debe | Q2-2026 | No iniciada | Implementar decoradores pytest |
| 🟢 PRÓXIMA | Release Notes (estructura fija) | Debe | Q4-2026 | No iniciada | Template preparado |
| 🟢 PRÓXIMA | Vault para secretos referenciado en config | Debe | Q4-2026 | No iniciada | Template preparado |

---

## 8. Checklist de inicio por sesión (Copilot)

Antes de cualquier trabajo en repo nuevo o existente:

- [ ] Leer esta directriz desde vault: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
- [ ] Leer metadatos repo si existen (README/.kaafile/metadata.yml).
- [ ] Ejecutar: `ls -la .gitignore CONTRIBUTING.md CHANGELOG.md Jenkinsfile` (verificar presencia).
- [ ] Si falta algún "🔴 CRÍTICA", agregar a backlog sesión con prioridad.
- [ ] Documentar findings en `proyectos/<repo>/ESTADO.md`.
- [ ] Al cierre: actualizar `contexto/CAMBIOS_RECIENTES.md` con gaps cerrados.

---

## 9. Fuera de alcance (deliberado)

- Buenas prácticas estilo código, SOLID/Clean Code, refactor, code smells.
- Estrategias pair/mob programming, dinámicas code review.
- Cómo escribir test concreto (mocks, fixtures, datos hardcoded).
- Despliegue, monitorización, logging aplicativo.
- Configuración específica de tooling (ESLint, Prettier settings, etc.).

---

## 🔗 Referencias Vault

- Default: [[INDEX]]
- Navegación: [[NAVIGATION]]
- Instrucciones Copilot global: [[_global/copilot/INSTRUCCIONES_BASE]]
- Wikilinks convención: [[_global/standards/WIKILINKS_CONVENCION]]

**Para cada proyecto activo:**
- Estado repositorio: `proyectos/<nombre>/ESTADO.md`
- Cambios sesión: `proyectos/<nombre>/contexto/CAMBIOS_RECIENTES.md`

---

**Versión:** 1.0 (Adaptación Obsidian)
**Versión origen:** AI_FOUNDATION Directrices Qs Transformación (Q4-2025 → Q4-2026)
**Creada:** 29 Junio 2026
**Última actualización:** 29 Junio 2026
**Estado:** ✅ Listo para implantación en Copilot

