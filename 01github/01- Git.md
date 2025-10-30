
# 📘 Guía Completa de Git y Control de Versiones

Este documento combina conceptos clave y comandos prácticos de Git, ideal como referencia rápida y para entender el flujo del control de versiones.

## 🔍 ¿Qué es Git y el Control de Versiones?

- **Control de versiones:** Sistema que permite gestionar y rastrear cambios en archivos a lo largo del tiempo. Facilita:
  - Historial de cambios
  - Colaboración entre varios desarrolladores
  - Revertir cambios
  - Crear ramas para nuevas características

- **Git:** Es el sistema de control de versiones más popular. Permite tener un repositorio local con todo el historial del proyecto.

- **GitHub:** Servicio de hosting para repositorios Git en la nube, ideal para colaborar.

## ⚙️ Instalación y Configuración

### Instalar Git
```bash
sudo apt update
sudo apt install git
git --version
```

### Configuración inicial
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tucorreo@ejemplo.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"   # VS Code
# o para vim
git config --global core.editor "vim"
```

### Comandos útiles de configuración
```bash
git config --list
git config --global -e
git config -h
git help config
```

### Normalizar saltos de línea
```bash
git config --global core.autocrlf true    # Windows
git config --global core.autocrlf input   # Linux/mac
```

## 📁 Crear y Clonar Repositorios
```bash
git init
git clone <URL>
```

## 📌 Estado de Archivos y Preparación
```bash
git status
git add <archivo>
git add .
git restore --staged <archivo>
git reset HEAD <archivo>
```

## ✅ Commits
```bash
git commit -m "Mensaje"
git commit -am "Mensaje"
```

## 🔍 Historial y Diferencias
```bash
git log
git log --oneline
git log --graph --oneline --all
git show <commit>
git diff
git diff --cached
git reflog
```

## 🌿 Ramas
```bash
git branch
git branch <nombre>
git checkout <rama>
git checkout -b <rama>
git switch <rama>
git branch -d <rama>
```

### Fusionar ramas
```bash
git merge <rama>
```

## 🌐 Conexión con Repositorio Remoto
```bash
git remote -v
git remote add origin <URL>
git push -u origin main
git push
git pull
git fetch
```

## 🧹 Limpieza y Archivos Eliminados
```bash
git rm <archivo>
git rm --cached <archivo>
git clean -f
```

## 📦 Stash
```bash
git stash
git stash pop
```

## 🔐 Manejo de Credenciales
```bash
git config --global --unset user.name
git config --global --unset user.email
git credential-cache exit
git config --global --unset credential.helper
```

## 🧠 Otros Comandos Útiles
```bash
git blame <archivo>
git tag <nombre>
git checkout <SHA>
```

## 📊 Visualización en VS Code
1. Instala la extensión **Git Graph**.
2. Ctrl + Shift + P → `Git Graph: View Git Graph`
3. Visualiza ramas, commits y fusiones.

## ⚡ Tips finales
- Usa `git help <comando>` para consultar la documentación oficial.
- Flujo básico:
  1. Editar archivos
  2. `git add`
  3. `git commit`
  4. `git push`
