# Guía de Instalación de Docker en Ubuntu

Esta guía detalla los pasos necesarios para instalar Docker Engine y Docker Compose en un sistema Ubuntu.

## 1. Preparación del Sistema

Antes de instalar Docker, es necesario actualizar los paquetes existentes e instalar algunas dependencias.

### Actualizar el sistema
```bash
sudo apt update && sudo apt upgrade -y
```
<img width="692" height="197" alt="image" src="https://github.com/user-attachments/assets/43633740-f0c1-4bc6-ad29-b46481a0c165" />


### Instalar dependencias necesarias
```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
```

## 2. Configuración del Repositorio

Docker no se encuentra en los repositorios predeterminados de Ubuntu, por lo que debemos añadir el repositorio oficial.

### Añadir la clave GPG oficial de Docker
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### Añadir el repositorio de Docker
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 3. Instalación de Docker

Una vez configurado el repositorio, procedemos con la instalación de Docker y sus componentes principales.

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

**Paquetes incluidos:**
- `docker-ce`: Motor principal de Docker.
- `docker-ce-cli`: Interfaz de línea de comandos.
- `containerd.io`: Runtime de contenedores.
- `docker-compose-plugin`: Herramienta para gestionar múltiples contenedores.

## 4. Gestión de Usuarios (Opcional)

Por defecto, Docker requiere permisos de administrador. Para evitar usar `sudo` en cada comando, puedes añadir tu usuario al grupo `docker`.

```bash
sudo usermod -aG docker $USER
```

> **Nota:** Después de ejecutar este comando, es necesario cerrar sesión y volver a iniciarla (o reiniciar el sistema) para aplicar los cambios.

## 5. Verificación de la Instalación

Comprueba que Docker está correctamente instalado y funcionando.

### Verificar versión
```bash
docker --version
```

### Ejecutar contenedor de prueba
```bash
sudo docker run hello-world
```

Si todo funciona correctamente, Docker descargará y ejecutará una imagen de prueba mostrando un mensaje de éxito.

## 6. Configuración Básica del Servicio

### Comprobar estado del servicio
```bash
sudo systemctl status docker
```

### Habilitar Docker al inicio del sistema
```bash
sudo systemctl enable docker
```

### Ver información detallada del sistema Docker
```bash
docker info
```
