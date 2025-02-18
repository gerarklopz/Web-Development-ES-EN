# Github_desde_Docker
# Configuración y Uso de Docker para Trabajar con GitHub

Este documento detalla los pasos necesarios para configurar un contenedor Docker, trabajar con GitHub y realizar cambios en un repositorio desde el contenedor.

---

## 1. Preparar Docker en el Sistema Anfitrión

### Verificar e Instalar Docker
1. Comprobamos si Docker está instalado:
   ```bash
   docker --version
   ```

2. Si Docker no está instalado, lo instalamos:
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   ```

3. Añadimos nuestro usuario al grupo `docker` para evitar usar `sudo`:
   ```bash
   sudo usermod -aG docker $(whoami)
   newgrp docker
   ```

4. Verificamos que Docker funciona correctamente:
   ```bash
   docker run hello-world
   ```

---

## 2. Crear una Imagen Docker Personalizada

### Crear un Dockerfile
1. Creamos un directorio para el proyecto y el archivo `Dockerfile`:
   ```bash
   mkdir ~/docker_projects && cd ~/docker_projects
   vi Dockerfile
   ```

2. Añadimos el siguiente contenido al `Dockerfile`:
   ```Dockerfile
   # Usar una imagen base de Ubuntu
   FROM ubuntu:20.04

   # Instalar dependencias necesarias
   RUN apt update && apt install -y \
       git \
       ssh \
       vim \
       curl && \
       apt clean

   # Crear un directorio de trabajo
   WORKDIR /workspace

   # Configurar el contenedor para iniciar con bash
   CMD ["bash"]
   ```

### Construir la Imagen
1. Construimos la imagen personalizada:
   ```bash
   docker build -t pc_test .
   ```

2. Verificamos que la imagen se haya creado:
   ```bash
   docker images
   ```

---

## 3. Crear y Ejecutar el Contenedor

1. Creamos y ejecutamos un contenedor basado en la imagen:
   ```bash
   docker run -it --name pc_test-instance -v ~/workspace:/workspace pc_test /bin/bash
   ```
   - **`--name`**: Asigna un nombre al contenedor.
   - **`-v ~/workspace:/workspace`**: Monta un directorio local en el contenedor.

2. Verificamos los contenedores activos:
   ```bash
   docker ps
   ```

3. Para detener el contenedor:
   ```bash
   docker stop pc_test-instance
   ```

4. Para reiniciar y acceder nuevamente al contenedor:
   ```bash
   docker start -ai pc_test-instance
   ```

---

## 4. Configurar Git en el Contenedor

1. Configuramos nuestro nombre y correo en Git:
   ```bash
   git config --global user.name "Gerardo Arca López"
   git config --global user.email "gerardoarcalopez@gmail.com"
   ```

2. Verificamos la configuración:
   ```bash
   git config --list
   ```

3. Aseguramos que las claves SSH estén disponibles o generamos nuevas:
   - Para copiar las claves del anfitrión:
     ```bash
     docker run -it -v ~/.ssh:/root/.ssh:ro pc_test /bin/bash
     ```
   - Para generar una nueva clave:
     ```bash
     ssh-keygen -t ed25519 -C "gerardoarcalopez@gmail.com"
     cat /root/.ssh/id_ed25519.pub
     ```
     Añadimos la clave pública a GitHub desde [Settings → SSH Keys](https://github.com/settings/keys).

4. Verificamos la conexión SSH con GitHub:
   ```bash
   ssh -T git@github.com
   ```

---

## 5. Trabajar con GitHub desde el Contenedor

### Clonar el Repositorio
1. Navegamos al directorio `/workspace`:
   ```bash
   cd /workspace
   ```

2. Clonamos el repositorio de GitHub:
   ```bash
   git clone git@github.com:gerarklopz/Practica_de_workflow_en_GitHub.git
   ```

3. Accedemos al directorio del repositorio:
   ```bash
   cd Practica_de_workflow_en_GitHub
   ```

### Realizar Cambios y Subirlos
1. Editamos o creamos un archivo:
   ```bash
   vi README.md
   ```
   Ejemplo de contenido:
   ```markdown
   ### Configuración en Docker
   Este archivo documenta los pasos para configurar y trabajar con GitHub desde un contenedor Docker.

   #### Pasos realizados:
   - Configuración de Git.
   - Autenticación SSH con GitHub.
   - Clonación del repositorio.
   - Realización de cambios documentados en este archivo.
   ```

2. Añadimos los cambios al área de staging:
   ```bash
   git add README.md
   ```

3. Realizamos el commit:
   ```bash
   git commit -m "Documentar configuración en Docker"
   ```

4. Subimos los cambios al remoto:
   ```bash
   git push origin main
   ```

---

## 6. Resumen de Comandos Clave

```bash
# Construir la imagen
docker build -t pc_test .

# Crear y ejecutar el contenedor
docker run -it --name pc_test-instance -v ~/workspace:/workspace pc_test /bin/bash

# Detener el contenedor
docker stop pc_test-instance

# Reiniciar y acceder al contenedor
docker start -ai pc_test-instance

# Configurar Git
git config --global user.name "Gerardo Arca López"
git config --global user.email "gerardoarcalopez@gmail.com"

# Verificar conexión SSH
ssh -T git@github.com

# Trabajar con el repositorio
git clone git@github.com:gerarklopz/Practica_de_workflow_en_GitHub.git
cd Practica_de_workflow_en_GitHub
vi README.md
git add README.md
git commit -m "Documentar configuración en Docker"
git push origin main
```

---

¡Listos para mostrar nuestras habilidades trabajando con GitHub desde Docker! 😊



