# Prompt Auditoría Repositorio (Template para Copilot)

**Usar este prompt cuando necesites auditar un repositorio contra directrices BBVA.**

---

## Flujo (copiar y pegar a Copilot)

```text
Voy a auditar un repositorio contra las directrices BBVA de control.

Vault de contexto:
~/Documents/kpfm-obsidian-vault/

Pasos obligatorios:

1) Lee primero:
   - [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
   - Priorización: tabla sección 7 (Crítica > Media > Próxima)

2) Información del repositorio a auditar:
   - Ubicación local: [INGRESAR_RUTA]
   - Nombre: [INGRESAR_NOMBRE]
   - Stack: [INGRESAR_STACK: Python/PySpark/Scala/Terraform/etc]
   - Tipo: [INGRESAR_TIPO: batch/online/library/iac/legacy_conf]
   - Plataforma: [INGRESAR_PLATAFORMA: EMR/Dataproc/SageMaker/Athena/GitLab/etc]

3) Escanea y reporta (en este orden):

   A. CRÍTICA - Ficheros obligatorios (Q4-2025 vencido)
      - [ ] metadata.yml o sección README con owner + branching_model
      - [ ] .gitignore completo para stack
      - [ ] CONTRIBUTING.md con procedimiento InnerSource
      - [ ] Ficheros config versionados (desacoplados de secretos)
      - [ ] Pipeline CI/CD (Jenkinsfile/.gitlab-ci.yml) con stages
      - [ ] Lockfile dependencias (requirements.txt/poetry.lock con versiones fijadas)
      - [ ] Scripts DDL/schema BBDD (si aplica repo)

   B. MEDIA - Artefactos recomendados (Q4-2025)
      - [ ] README.md enriquecido (owner, branching_model, estructura)
      - [ ] CHANGELOG.md automatizado o manual
      - [ ] Tests con tagging requisito (decorator @pytest.mark.requirement("ID"))

   C. CRÍTICA - Anti-patrones (DEBE detectar)
      - [ ] Secrets hardcoded (*.key, *.pem, tokens)
      - [ ] Binarios/artefactos: *.jar, *.egg, build/, dist/, __pycache__/
      - [ ] Ficheros gigante > 30 MB versionados

   D. Testing + Pipeline
      - [ ] Carpeta tests/ dentro repo
      - [ ] Pipeline incluye: lint + unit + SAST + TIA|smoke
      - [ ] Cobertura reportada en logs/artefacto

4) Reporte de hallazgos:

   Formato:
   ```
   REPOSITORIO: [nombre]
   STACK: [stack]
   TIPO: [tipo]
   FECHA AUDITORÍA: [hoy]

   CRÍTICA - GAPS (Máxima prioridad)
   - [ ] Fichero 1: FALTA (acciones: generar con template X)
   - [ ] Fichero 2: EXISTE pero incompleto (mejorar: Y)
   - [ ] Anti-patrón 1: ENCONTRADO (acción: remover)

   MEDIA - RECOMENDACIONES
   - [ ] Fichero 3: FALTA (generar si tiempo)
   - [ ] Mejora 1: enriquecer sección X

   COMPLIANT
   - ✅ Fichero A: OK
   - ✅ Fichero B: OK

   RESUMEN PRIORIDAD
   - Acciones CRÍTICA: [número]
   - Acciones MEDIA: [número]
   - Estimado esfuerzo: [2h|4h|1d|2d]
   ```

5) Si lo solicito, genera:
   - ficheros_faltantes/ con templates para cada gap
   - AUDIT_REPORT.md en raíz repo
   - Actualiza proyectos/<repo-name>/ESTADO.md en vault

6) Documentación en Vault:
   - Registra en: proyectos/<repo-name>/contexto/CAMBIOS_RECIENTES.md
   - Enlaza a: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS#7-tabla-resumen-priorizada]]
```

---

## Cómo usar este template

### Opción A: Copilot en JetBrains
1. Abre Chat de Copilot (Cmd+Shift+Enter en macOS).
2. Pega el bloque `## Flujo` arriba.
3. Completa [INGRESAR_*] con datos reales del repo.
4. Presiona Enter.

### Opción B: Copilot en Web
1. Copia el bloque `## Flujo`.
2. Abre GitHub Copilot web (si tienes acceso).
3. Pega y completa placeholders.
4. Ejecuta.

### Opción C: Terminal + Prompt Copy
```bash
# Desde terminal, copia el prompt a portapapeles
cat > audit_prompt.txt << 'EOF'
[pega contenido Flujo con placeholders rellenos]
EOF

# Ábrelo en tu editor Copilot favorito
cat audit_prompt.txt | pbcopy
```

---

## Placeholders a rellenar

| Placeholder | Ejemplo |
|---|---|
| [INGRESAR_RUTA] | `/Users/t022458/PycharmProjects/kpfm/lib/etiquetas` |
| [INGRESAR_NOMBRE] | `kpfm-etiquetas` |
| [INGRESAR_STACK] | `Python 3.11, PySpark, Pydantic, PyHOCON` |
| [INGRESAR_TIPO] | `batch` |
| [INGRESAR_PLATAFORMA] | `EMR` |
| [hoy] | `29 Junio 2026` |

---

## Checklist antes de auditar

- [ ] He leído `DIRECTRICES_CONTROL_REPOSITORIOS.md`.
- [ ] Sé ubicación exacta del repo local.
- [ ] Conozco stack/tipo/plataforma del repo.
- [ ] Tengo permisos lectura repo (git clone / local access).
- [ ] He abierto Chat de Copilot o estoy en sesión Copilot.

---

## Salida esperada de Copilot

Copilot devuelve:

```
✅ AUDITORÍA COMPLETADA

Repositorio: kpfm-etiquetas
Stack: Python 3.11, PySpark, Pydantic, PyHOCON
Tipo: batch
Fecha: 29 Junio 2026

CRÍTICA - FALTANTE (3 gaps)
1. metadata.yml - GENERAR
2. CONTRIBUTING.md - GENERAR
3. Lockfile - COMPLETAR (requirements.txt existe pero sin versiones fijadas)

MEDIA - RECOMENDACIONES (2)
1. README.md - enriquecer sección owner
2. CHANGELOG.md - implementar auto-generación

ANTI-PATRONES - OK
✅ No secrets encontrados
✅ No binarios > 30 MB
✅ .gitignore adecuado

COMPLIANT (5/10)
✅ Pipeline CI/CD OK (Jenkinsfile)
✅ Tests estructura OK
✅ Configuración versionada OK
✅ Scripts DDL OK
✅ SAST en pipeline OK

PRÓXIMOS PASOS:
→ Generar 2 ficheros (metadata.yml, CONTRIBUTING.md) = 1h
→ Enriquecer README = 30min
→ Auto-CHANGELOG setup = 1h
Estimado total: 2.5h

Archivos generados: /ficheros_faltantes/
Informe completo: AUDIT_REPORT.md (raíz repo)
```

---

## Referencias

- Directrices BBVA: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]
- Tabla priorizada: [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS#7-tabla-resumen-priorizada]]
- Instrucciones Copilot global: [[_global/copilot/INSTRUCCIONES_BASE]]
- Proyectos activos: [[INDEX]]

---

**Versión:** 1.0
**Creada:** 29 Junio 2026
**Estado:** ✅ Listo para usar
**Última actualización:** 29 Junio 2026

---

## Ejemplo de sesión completa

### Input (lo que pasas a Copilot):
```
Voy a auditar un repositorio contra las directrices BBVA de control.

Vault de contexto:
~/Documents/kpfm-obsidian-vault/

Pasos obligatorios:

1) Lee primero:
   [[_global/standards/DIRECTRICES_CONTROL_REPOSITORIOS]]

2) Información del repositorio:
   - Ubicación local: /Users/t022458/PycharmProjects/kpfm/lib/etiquetas
   - Nombre: kpfm-etiquetas
   - Stack: Python 3.11, PySpark, Pydantic, PyHOCON
   - Tipo: batch
   - Plataforma: EMR

[... resto del flujo ...]
```

### Output (lo que Copilot devuelve):
```
✅ AUDITORÍA COMPLETADA

Repositorio: kpfm-etiquetas
...

CRÍTICA - FALTANTE (3 gaps)
1. metadata.yml - GENERAR
2. CONTRIBUTING.md - GENERAR
3. Lockfile - COMPLETAR

...

PRÓXIMOS PASOS:
→ Generar 2 ficheros = 1h
→ Enriquecer README = 30min
→ Auto-CHANGELOG setup = 1h
Estimado total: 2.5h
```

---

## Tip Pro: Automatizar auditorías periódicas

**Crea alias en `.zshrc` para auditar repos frecuentes:**

```bash
alias audit-etiquetas='pbpaste | grep -q "PROMPT_AUDITORIA" && echo "Iniciando auditoria..." && open -a "JetBrains Toolbox" && echo "Pega el prompt en Copilot Chat"'
```

O más simple, guarda este prompt como fichero y úsalo como template reutilizable.

---

**¡Listo para auditar cualquier repositorio! 🔍**

