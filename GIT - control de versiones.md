# GIT

Un Sistema de control de versiones es una herramienta que permite hacer seguimiento (rastrear) los cambios que se han realizado en conjunto de archivos de un proyecto de software.

# GitHub

Servicio de hosting que nos permite almacenar proyectos de desarrollo de software y control de versiones usando git.

# Instalación y Configuración

1. Ejecutar comando **sudo apt update**

2. Ejecutar comando **sudo apt install git**

3. Ejecutar **git --version**

   ![](https://i.ibb.co/TtN6Sss/image.png)

4. Configure credenciales de git localmente

   ```
   git config --global user.name "Usuario"
   git config --global user.email "Email"
   ```

   ![](https://i.ibb.co/z4t9T9z/image.png)

![](https://i.ibb.co/BVPct6S/image.png)

## Configurando rama Main

```
git config --global init.defaultBranch main
nano ~/.gitconfig
```

![](https://i.ibb.co/7Smwv4B/image.png)

![](https://i.ibb.co/6DxRDMF/image.png)

## Configuracion editor por defecto

```
git config --global core.editor "Editor"
```

**Visual Studio Code**

```
git config --global core.editor "code --wait"
```

**Vim**

```
git config --global core.editor "vim"
```

![](https://i.ibb.co/9ZPym20/image.png)

![](https://i.ibb.co/xmhbnkS/image.png)

## Resumen Comandos Conf. Inicial

```less
git --version
213225665+johlverpardo@users.noreply.github.com

git config --global user.name "xxxxxxx"
git config --global user.email xxxxxx@gmail.com

git config --global init.defaultBranch main
# muestra todas las configuraciones de Git activas en tu sistema. 
git config --list
# asignando visual studio code como editor de configuración de git
git config --global core.editor "code --wait"
#abre el archivo de configuración global de Git (~/.gitconfig en Linux/macOS o C:\Users\TuUsuario\.gitconfig en Windows) en el #editor de texto predeterminado de Git.
git config --global -e
# para estandarizar los saltos de línea en windows
git config --global core.autocrlf true
# para estandarizar los saltos de línea en linux/mac
git config --global core.autocrlf input
# ver todas las opciones de la configuración en la terminal
git config -h
# ver todas las opciones de la configuración en el navegador
git help config
```



# Conceptos Básicos

## Control de versiones

El control de versiones es un sistema que permite gestionar y realizar un seguimiento de los cambios en el código o en cualquier conjunto de archivos a lo largo del tiempo. Su función principal es guardar distintas versiones o estados de un proyecto, facilitando la colaboración entre desarrolladores y permitiendo volver a versiones anteriores si es necesario.

Algunas de las características clave del control de versiones son:

1. **Historial de cambios**: Almacena cada modificación realizada, lo que permite ver qué cambios se hicieron, quién los hizo y cuándo.
2. **Colaboración**: Facilita que varios desarrolladores trabajen en el mismo proyecto sin pisarse el trabajo, incluso si están modificando los mismos archivos.
3. **Deshacer cambios**: Permite revertir el proyecto a un estado anterior si se cometió un error o si un cambio no resulta como se esperaba.
4. **Ramas (branches)**: Ofrece la capacidad de crear versiones paralelas del proyecto, llamadas "ramas," para experimentar o desarrollar nuevas características sin afectar la versión principal.

Las herramientas de control de versiones más populares incluyen Git, Subversion (SVN), y Mercurial, aunque Git es la más común, especialmente en proyectos de software libre y desarrollo colaborativo.



## Repositorio

![](https://i.ibb.co/wYvvrch/image.png)

Un repositorio es un espacio o almacenamiento en el que se guarda y organiza el código de un proyecto, junto con su historial de cambios y configuraciones. Es como una "biblioteca" de archivos y datos del proyecto, que permite gestionar el control de versiones y la colaboración entre desarrolladores. Los repositorios pueden estar en tu máquina local (repositorio local) o en un servidor en la nube (repositorio remoto) al que pueden acceder varios usuarios.

**Características principales de un repositorio:**

1. **Almacenamiento de código**: Guarda los archivos del proyecto, desde el código fuente hasta los documentos relacionados.
2. **Historial de cambios**: Lleva un registro de todas las modificaciones realizadas, permitiendo a los desarrolladores revisar y restaurar versiones anteriores del proyecto.
3. **Colaboración**: Permite que varios desarrolladores contribuyan al mismo proyecto. Los cambios de cada colaborador se integran al repositorio, facilitando la cooperación.
4. **Ramas**: Permite crear ramas (branches) o versiones paralelas del proyecto para desarrollar nuevas características o probar cambios sin afectar la versión principal.
5. **Acceso remoto**: Un repositorio remoto, como los de plataformas como GitHub, GitLab o Bitbucket, facilita la colaboración al permitir que los desarrolladores trabajen en el mismo proyecto desde diferentes ubicaciones.

## Commit

Un **commit** en el contexto del control de versiones (como Git) es una acción que permite guardar un "punto de control" de los cambios realizados en el proyecto. Es como tomar una "foto" del estado actual del proyecto en un momento específico. Cada commit registra el conjunto de cambios que se han hecho desde el último commit, incluyendo un mensaje que describe esos cambios, lo cual ayuda a mantener un historial claro y detallado de la evolución del proyecto.

![](https://i.ibb.co/GF151V8/image.png)

![](https://i.ibb.co/ThPrmRw/image.png)



### Características de un commit:

1. **Historial de cambios**: Un commit guarda un registro permanente de los cambios en el proyecto, facilitando el seguimiento de lo que se hizo en cada etapa.
2. **Deshacer o revertir cambios**: Gracias al historial de commits, es posible volver a un estado anterior del proyecto si algún cambio resultó en un error o problema.
3. **Colaboración**: Cada commit indica quién hizo el cambio, cuándo, y con qué propósito, ayudando a que los miembros del equipo comprendan el flujo de trabajo y el avance de otros colaboradores.
4. **Mensajes descriptivos**: Al hacer un commit, se recomienda escribir un mensaje que describa brevemente los cambios realizados (como "Corrige error en el cálculo de impuestos" o "Agrega nueva función de búsqueda"). Esto facilita la revisión del historial.

# Áreas en Git

![](https://i.ibb.co/h7SqPT6/image.png)

En Git, las áreas representan diferentes etapas del ciclo de vida de los cambios en los archivos dentro de un repositorio. Estas áreas son esenciales para gestionar versiones y organizar cambios de manera eficiente. Aquí están las principales áreas en Git:

### **Working Directory (Directorio de trabajo)**

Es el área correspondiente al estado ***modified\*** y es la carpeta local de tu computadora donde almacenas los archivos de tu proyecto.

- **Estado:** Los archivos están **modificados** pero no rastreados o preparados para un commit.
- **Acción clave:** Si haces cambios en tus archivos, puedes moverlos al área de preparación con

```
git add archivo
```

### **Staging Area (Área de preparación o index)**

Es el área correspondiente al estado ***staged\*** también se le llama ***index\*** por que es el área donde *git* indexa y agrega los cambios realizados en los archivos previos a comprometerlos en su registro.

- **Estado:** Los archivos están **preparados**.

- **Acción clave:** Para añadir archivos o cambios al área de preparación, usa:

  ```
  git add archivo
  ```

- **Ver archivos preparados**

  ```
  git status
  ```

### **Repository (Repositorio local o commit area)**

Es el área correspondiente al estado ***remote\*** y es el directorio remoto donde almacenamos los archivos del proyecto en alguna plataforma *web* como *GitHub*, *GitLab*, *BitBucket*. *Git* denomina ***origin\*** al repositorio remoto.

- **Estado:** Los archivos están **confirmados**.
- **Acción clave:** Para confirmar cambios desde el área de preparación

```
git commit -m "Mensaje del commit"
```

```
git log — Muestra el historial de commits en el repositorio.
```

### Remote Repository (Repositorio remoto)

Es una copia del repositorio local almacenada en un servidor remoto, como GitHub, GitLab o Bitbucket. Permite colaborar con otros desarrolladores.

- **Estado:** Los cambios están **enviados** al remoto.
- **Acción clave:** Para subir cambios al repositorio remoto:

```
git push origin rama
```

- **Sincronización:** Puedes obtener cambios desde el remoto con

```
git pull origin rama
```

### Ciclo de flujo típico en Git:

1. **Editar archivos** en el directorio de trabajo.
2. **Preparar cambios** en el área de preparación con `git add`.
3. **Confirmar cambios** al repositorio local con `git commit`.
4. **Subir cambios** al repositorio remoto con `git push`.

![](https://i.ibb.co/kKs17QB/image.png)

```
# agregar los cambios de un archivo al staged
git add archivo/directorio
# agregar todos los cambios de todos los archivos al staged
git add .

# los cambios son comprometidos en el repositorio
# debes escribir el mensaje del cambio
# cuando se abra el archivo de configuración
# al terminar guarda y cierra el archivo
# para que los cambios tengan efecto
git commit
# es un shortcut del comando anterior
# escribes y confirmas el mensaje del cambio en un sólo paso
git commit -m "mensaje descriptivo del cambio"


# se agrega el origen remoto de tu repositorio de GitHub
git remote add origin https://github.com/usuario/repositorio.git
# la primera vez que vinculamos el repositorio remoto con el local
git push -u origin main
# para las subsecuentes actualizaciones, sino cambias de rama
git push


#para descargar los cambios del repositorio remoto al local
git pull
```

## De *master* a *main*

Con los desafortunados acontecimientos del 25 de mayo de 2020 en los Estados Unidos que culminaron con el asesinato del afroamericano [*George Floyd*](https://es.wikipedia.org/wiki/Muerte_de_George_Floyd) a manos de policias de la ciudad de *Mineápolis*, se intensificó de manera global el movimiento [*#BlackLivesMatter*](https://es.wikipedia.org/wiki/Black_Lives_Matter).

Con dicho movimiento muchas industrias y empresas comenzaron a tomar acciones para erradicar el racismo.

En la industria de la tecnología por años se han empleado palabras como *master*, *slave*, *whitelist*, *blacklist* entre otras que actualmente no son bien vistas por el contexto y la semántica que implican.

Al respecto *Microsoft* empresa propietaria de *GitHub* decidió comenzar una campaña para reemplazar el nombre de la rama principal de los repositorios de *master* a *main*

### Para repositorios nuevos

```
git init
git add .
git commit -m "Primer commit"
git branch -M main
git remote add origin https://github.com/usuario/repositorio.git
git push -u origin main
```

### Para repositorios existentes

```
git branch -M main
git remote add origin https://github.com/usuario/repositorio.git
git push -u origin main
```

### Para reemplazar la rama *master* por *main* en *GitHub*

```
# Paso 1
# Crea la rama local main y pásale el historial de la rama master
git branch -m master main


# Paso 2
# Haz un push de la nueva rama local main en el repositorio remoto de GitHub
git push -u origin main


# Paso 3
# Cambia el HEAD actual a la rama main
git symbolic-ref refs/remotes/origin/HEAD refs/remotes/origin/main

Paso 4
Cambia la rama default de master a main en tu repositorio de GitHub .

# Paso 5
# Elimina la rama master del repositorio remoto
git push origin --delete master



```

