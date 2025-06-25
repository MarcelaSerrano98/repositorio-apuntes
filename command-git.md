# 📘 Guía Completa de Comandos Git

Este archivo contiene los comandos más usados de Git para iniciar, gestionar versiones, ramas, sincronizar con repositorios remotos y más. Ideal como referencia rápida.

---

## 🔧 Configuración Inicial

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tucorreo@ejemplo.com"
git config --global init.defaultBranch main
git config --list
 
git init                      Inicializa un repositorio local
git clone <URL>                Clona un repositorio remoto


📌 Estado de Archivos
git status                    # Ver el estado de los archivos
git add <archivo>             # Agrega un archivo al staging area
git add .                     # Agrega todos los archivos modificados
git restore --staged <archivo>   # Quita archivo del staging
git reset HEAD <archivo>      # Alternativa clásica para quitar del staging

✅ Commits
git commit -m "Mensaje"       # Crea un commit con mensaje
git log --oneline             # Ves los commits realizados
git reset --hard HEAD~1       # Elimina el ultimo commit



🔍 Historial de Cambios

git log                       # Historial completo de commits
git log --oneline             # Historial resumido
git show <commit>             # Detalles de un commit específico


🌿 Ramas

git branch                    # Lista ramas locales
git branch <nombre>           # Crea una nueva rama
git checkout <rama>           # Cambia a una rama
git checkout -b <rama>        # Crea y cambia a la nueva rama
git switch <rama>             # Alternativa moderna a checkout
git branch -d <rama>          # Elimina una rama local

🔀 Fusionar Ramas
git merge <rama>              # Une rama indicada a la actual

🌐 Conexión con Repositorio Remoto
git remote -v                 # Ver repositorios remotos
git remote add origin <URL>  # Agregar un repositorio remoto
git push -u origin main      # Subir el proyecto por primera vez
git push                     # Enviar cambios al remoto
git pull                     # Traer y fusionar cambios del remoto
git fetch                    # Traer cambios sin fusionar

🧹 Limpieza y Archivos Eliminados
git rm <archivo>             # Elimina archivo del disco y del staging
git rm --cached <archivo>    # Elimina archivo del staging, lo deja en disco
git clean -f                 # Elimina archivos no rastreados (pendientes sin seguimiento)

📦 Stash (guardar cambios sin confirmar)
git stash                    # Guarda temporalmente los cambios
git stash pop                # Restaura los cambios guardados

🔐 Manejo de Credenciales
git config --global --unset user.name
git config --global --unset user.email
git credential-cache exit
git config --global --unset credential.helper

🧠 Otros Comandos Útiles
git blame <archivo>          # Ver autor línea por línea
git tag <nombre>             # Crear etiqueta en commit actual
git checkout <SHA>           # Cambiar a un commit específico (modo detached)

📊 Visualizar ramas con Git Graph (en VS Code)
Instala la extensión Git Graph desde el Marketplace.

Abre la paleta de comandos: Ctrl + Shift + P.

Escribe Git Graph: View Git Graph y selecciónalo.

Podrás ver ramas, commits y fusiones de forma visual.

💡 Tips Finales
Usa git help <comando> para ver la documentación oficial.

Comando útil para ver ramas y fusiones gráficamente:
git log --graph --oneline --all
