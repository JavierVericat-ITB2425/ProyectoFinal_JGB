# Instalación de Docker y Docker Compose

Guía rápida de los pasos realizados para la instalación de Docker.

### 1. Actualización del sistema e Instalación
Primero actualizamos los repositorios e instalamos el motor de Docker y sus componentes:
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
<img width="709" height="232" alt="image" src="https://github.com/user-attachments/assets/a60b9c5b-076d-4158-a4aa-5c293f43cbb2" />


### 2. Gestión de Usuarios
Para poder ejecutar comandos de Docker sin usar `sudo`, añadimos nuestro usuario al grupo `docker`:
```bash
sudo usermod -aG docker $USER
```
*Nota: Es necesario cerrar sesión y volver a entrar para que se apliquen los cambios.*
<img width="372" height="24" alt="image" src="https://github.com/user-attachments/assets/0da90723-45d0-4fe7-a6e8-720a0d2ada3d" />


### 3. Verificación de la instalación
Comprobamos que Docker se ha instalado correctamente viendo su versión:
```bash
docker --version
```
<img width="305" height="39" alt="image" src="https://github.com/user-attachments/assets/ed0c3039-b930-4b6d-9513-19f34b28abd6" />


### 4. Docker Compose
Creamos o editamos el archivo de configuración para los contenedores:
```bash
sudo nano docker-compose.yml
```
El contenido de este archivo seria el siguiente

```bash
include:
  - keycloak/docker-compose.yml
  - monitoring/docker-compose.yml
  - nginx/docker-compose.yml
```
<img width="579" height="139" alt="image" src="https://github.com/user-attachments/assets/03e4864f-cc05-467f-87c9-e10dd2df12d0" />
