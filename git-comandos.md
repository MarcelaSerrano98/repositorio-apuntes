
# 📘 Guía de Comandos Git

Este archivo contiene los comandos más usados de Git para iniciar, gestionar versiones, ramas, sincronizar con repositorios remotos y más. Ideal como referencia rápida.

## 🔧 Configuración Inicial

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tucorreo@ejemplo.com"
git config --global init.defaultBranch main
git config --list
```

## 📁 Crear y Clonar Repositorios

```bash
git init                      # Inicializa un repositorio local
git clone <URL>               # Clona un repositorio remoto
```

## 📌 Estado de Archivos

```bash
git status                    # Ver qué ha cambiado
git add <archivo>             # Agrega archivo al staging area
git add .                     # Agrega todos los archivos modificados
git restore --staged <archivo>   # Quita archivo del staging
git reset HEAD <archivo>      # Quita del staging (forma clásica)
```

## ✅ Commits

```bash
git commit -m "Mensaje"       # Crea un commit con mensaje
git commit -am "Mensaje"      # Agrega y commitea archivos rastreados
```

## 🔍 Historial de Cambios

```bash
git log                       # Historial completo de commits
git log --oneline             # Historial resumido
git show <commit>             # Detalles de un commit específico
git diff                      # Diferencias no confirmadas
git diff --cached             # Diferencias en el staging area
git reflog                    # Ver historial de movimientos del HEAD
```

## 🌿 Ramas

```bash
git branch                    # Lista ramas locales
git branch <nombre>           # Crea una nueva rama
git checkout <rama>           # Cambia a una rama
git checkout -b <rama>        # Crea y cambia a nueva rama
git switch <rama>             # Alternativa moderna a checkout
git branch -d <rama>          # Elimina una rama local
```

## 🔀 Fusionar Ramas

```bash
git merge <rama>              # Une rama indicada a la actual
```

## 🌐 Conexión con Repositorio Remoto

```bash
git remote -v                 # Ver repositorios remotos
git remote add origin <URL>  # Agregar un repositorio remoto
git push -u origin main      # Subir el proyecto por primera vez
git push                     # Enviar cambios al remoto
git pull                     # Traer y fusionar cambios del remoto
git fetch                    # Traer cambios sin fusionarlos
```

## 🧹 Limpieza y Archivos Eliminados

```bash
git rm <archivo>             # Elimina archivo del disco y del staging
git rm --cached <archivo>    # Elimina archivo del staging, lo deja en disco
git clean -f                 # Elimina archivos no rastreados
```

## 📦 Stash (guardar cambios sin confirmar)

```bash
git stash                    # Guarda temporalmente los cambios
git stash pop                # Restaura los cambios guardados
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
git blame <archivo>          # Ver autor línea por línea
git tag <nombre>             # Crear etiqueta en commit actual
git checkout <SHA>           # Cambiar a un commit específico (modo detached)
```

## 📊 Visualizar ramas con Git Graph (en VS Code)

1. Instala la extensión **Git Graph** desde el Marketplace.
2. Abre la paleta de comandos: `Ctrl + Shift + P`.
3. Escribe `Git Graph: View Git Graph` y selecciónalo.
4. Podrás ver ramas, commits y fusiones de forma visual.

## 💡 Tips Finales

```bash
git log --graph --oneline --all   # Ver historial como un árbol
```
