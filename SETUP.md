# 🔧 SETUP - Configuración Inicial Vault Multi-Proyecto

**Cómo configurar Obsidian para máximo provecho**

---

## 📱 Paso 1: Instalar Obsidian

### macOS
```bash
# Opción A: Descargar
https://obsidian.md/download

# Opción B: Homebrew
brew install obsidian
```

### Windows/Linux
```bash
https://obsidian.md/download
```

---

## 📂 Paso 2: Abrir el Vault

1. **Abre Obsidian**
2. Click en **"Open folder as vault"** (esquina inferior izquierda)
3. Navega a: `~/Documents/kpfm-obsidian-vault/`
4. Click **"Open"**

✅ Listo. Verás la estructura en panel izquierdo.

---

## ⚙️ Paso 3: Configurar Obsidian

### Plugins Recomendados (gratuitos)

#### A) Graph View (INCLUIDO)
- Visualiza cómo se conectan todos los documentos
- Click ícono de red en panel izquierdo
- Ayuda a entender relaciones entre proyectos

#### B) Quick Switcher (INCLUIDO)
- `Cmd+O` busca archivo por nombre
- Esencial para navegar rápido 14+ archivos

#### C) Search (INCLUIDO)
- `Cmd+Shift+F` busca texto en TODO vault
- Encuentra "label_id" en cualquier proyecto

#### D) Backlinks (INCLUIDO)
- Click panel derecho → "Backlinks"
- Ver qué archivos linkan a éste

#### Plugins Opcionales:
- **Obsidian Git** ($0) - Auto-commit a GitHub
- **Dataview** ($0) - Crear vistas dinámicas
- **Calendar** ($0) - Timeline de cambios
- **Excalidraw** ($0) - Diagramas visuales

### Cómo Instalar Plugin Comunitario
1. Settings → Community Plugins
2. Desactiva "Restrict mode"
3. Click "Browse" y busca plugin
4. Click "Install"
5. Click "Enable"

---

## 🎨 Paso 4: Personalizar Tema

### Tema Recomendado
```
Settings → Appearance → Theme
→ Selecciona "Minimal" o "Obsidian"
```

### Editor Settings
```
Settings → Editor
→ Line numbers: ON
→ Fold heading: ON
→ Reading view: ON
```

---

## 📍 Paso 5: Crear Bookmarks (Favoritos)

Esto acelera MUCHO la navegación:

### Bookmark Críticos
```
1. Cmd+Shift+B → click "Add bookmark"
   [Bookmark INSTRUCCIONES_BASE]

2. Cmd+Shift+B → click "Add bookmark"
   [Bookmark proyectos/kpfm-etiquetas/README.md]

3. Cmd+Shift+B → click "Add bookmark"
   [Bookmark _global/standards/ARQUITECTURA_PATTERNS.md]
```

Resultado: Acceso 1-click a archivos críticos.

---

## 🔍 Paso 6: Personalizar Búsqueda

### Quick Filter
```
Settings → Core Plugins → Quick Switcher
→ Enable "Show existing only"
```

### Search Settings
```
Settings → Core Plugins → Search
→ Enable "Collapse results"
```

---

## 🖥️ Paso 7: Configurar Git (Backup Automático)

### Inicializar Git
```bash
cd ~/Documents/kpfm-obsidian-vault

# Primero: crear .gitignore
cat > .gitignore << 'EOF'
# Obsidian
.obsidian/workspace.json
.obsidian/cache
.obsidian/recent-files-cache.json

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/

# Legacy
archivos-legacy/*.bak
EOF

# Inicializar repo
git init
git add .
git commit -m "Initial: Multi-project Obsidian vault"
```

### Conectar a GitHub (Opcional)
```bash
# 1. Crear repo vacío en GitHub

# 2. Conectar remoto
git remote add origin https://github.com/[tu-usuario]/kpfm-obsidian-vault.git

# 3. Push
git branch -M main
git push -u origin main
```

### Auto-Commit con Plugin Git (Recomendado)
```
Settings → Community Plugins → Obsidian Git
→ Enable "Auto commit"
→ Auto commit interval: 10 (minutos)
→ Auto backup: ON
```

Resultado: Cambios se guardan automáticamente cada 10 min.

---

## 📋 Paso 8: Estructura Carpetas (Verificar)

Verifica que tu vault tenga esto:

```
~/Documents/kpfm-obsidian-vault/
├─ INDEX.md ✅
├─ SETUP.md ✅
├─ NAVIGATION.md (crearemos)
├─ _global/
│  ├─ copilot/
│  ├─ standards/
│  ├─ templates/
│  └─ shared/
└─ proyectos/
   └─ kpfm-etiquetas/
      ├─ README.md ✅
      ├─ contexto/ ✅
      ├─ referencias/ ✅
      └─ decisiones/ ✅
```

Si algo falta, créalo ahora.

---

## 🚀 Paso 9: Primeros Pasos en Obsidian

### Sesión Inicio (5 min)
```
1. Abre: INDEX.md
2. Click link: [[kpfm/proyectos/kpfm-etiquetas/README]]
3. Lee 2 minutos
4. ¡Ya estás dentro!
```

### Navega Como Pro
```
Ctrl+Click (Mac: Cmd+Click)  → Abre link en nueva pestaña
Backspace o Cmd+[            → Vuelve atrás
Cmd+P / Cmd+O               → Search file by name
Cmd+Shift+F                 → Search text globalmente
```

### Ver Graph View
```
Click ícono de red (panel izquierdo)
→ Visualiza cómo se conectan todos archivos
→ Zooma, muévete, haz click para explorar
```

---

## 💾 Paso 10: Rutina de Backup

### Diario (Automático si Git está activo)
```
Plugin Obsidian Git hace auto-commit cada 10 min
```

### Manual (Si necesitas)
```bash
cd ~/Documents/kpfm-obsidian-vault
git add .
git commit -m "Session update: [qué cambió]"
git push
```

### Verificar Status
```bash
git status        # Ver archivos modificados
git log --oneline # Ver histórico commits
```

---

## ✅ Checklist: Estás Listo Si...

- [ ] Obsidian instalado y vault abierto
- [ ] Ves estructura `_global/` y `proyectos/` en panel izquierdo
- [ ] Puedes hacer Cmd+O para buscar archivos
- [ ] Puedes hacer Cmd+Shift+F para buscar texto
- [ ] Hiciste click a un link interno (`[[...]]`) y funcionó
- [ ] Graph view muestra conexiones
- [ ] Git inicializado (opcional pero recomendado)
- [ ] Bookmarks creados para archivos críticos

---

## 🔧 Troubleshooting

| Problema | Solución |
|----------|----------|
| "Vault no abre" | Verifica ruta: `~/Documents/kpfm-obsidian-vault/` |
| "Links aparecen en rojo" | Archivo no existe → revisa nombre exacto |
| "Search no funciona" | Cierra y reabre Obsidian |
| "Graph view vacío" | Click "Force refresh" o reinicia Obsidian |
| "Git deja de hacer auto-commit" | Verifica plugin activo: Settings → Community Plugins |

---

## 📚 Documentación

### Obsidian Oficial
- https://help.obsidian.md/

### Markdown en Obsidian
- https://help.obsidian.md/Editing+and+formatting/Markdown+syntax

### Links Internos
- https://help.obsidian.md/Linking+notes+and+files/Internal+links

---

## 🎯 Próximo Paso

1. Completa este setup
2. Abre: [[NAVIGATION]] para aprender a navegar
3. Ve a: [[kpfm/proyectos/kpfm-etiquetas/README]]
4. ¡Empieza a usar!

---

**Versión:** 1.0
**Última Actualización:** 29 Junio 2026
**Estado:** Setup completo - listo para usar


