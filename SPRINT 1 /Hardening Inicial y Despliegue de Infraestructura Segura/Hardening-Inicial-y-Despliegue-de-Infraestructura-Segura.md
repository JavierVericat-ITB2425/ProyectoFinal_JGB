
# Hardening Inicial y Despliegue de Infraestructura Segura

## Indice

- [Informacion general](#informacion-general)
- [S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard](#s1-01---acceso-ssh-seguro-hardening-inicial---isard)
    - [1. Gestion de claves](#1-gestion-de-claves)
    - [2. Hardening de SSH](#2-hardening-de-ssh)
    - [3. Firewall (UFW)](#3-firewall-ufw)
    - [4. Regla AWS](#4-regla-aws)
- [S1-02 - Docker + Keycloak - Isard](#s1-02---docker--keycloak---isard)
    - [1. Instalacion de Docker](#1-instalacion-de-docker)
    - [2. Despliegue de Keycloak](#2-despliegue-de-keycloak)
- [S1-03 - Configuracion de Seguridad IAM - Isard](#s1-03---configuracion-de-seguridad-iam---isard)
    - [1. Crear y configurar Realm](#1-crear-y-configurar-realm)
    - [2. Autenticacion Multifactor (MFA)](#2-autenticacion-multifactor-mfa)
    - [3. Politicas de Seguridad y Contrasenas](#3-politicas-de-seguridad-y-contrasenas)
    - [4. Roles, Grupos y Usuarios](#4-roles-grupos-y-usuarios)
- [S1-04 - Hardening Avanzado del Sistema - Isard](#s1-04---hardening-avanzado-del-sistema---isard)
    - [1. Fail2Ban](#1-fail2ban)
    - [2. Actualizaciones Automaticas de Seguridad](#2-actualizaciones-automaticas-de-seguridad)
- [S1-05 - Hardening en el Nodo AWS](#s1-05---hardening-en-el-nodo-aws)
    - [1. Firewall en AWS y en la Instancia](#1-firewall-en-aws-y-en-la-instancia)
    - [2. Fail2Ban en AWS](#2-fail2ban-en-aws)
    - [3. Actualizaciones Automaticas en AWS](#3-actualizaciones-automaticas-en-aws)
- [Recomendaciones Adicionales](#recomendaciones-adicionales)

## Informacion general

![Portada](./imagenes/imagen-001.png)

- **Grupo:** 8
- **Integrantes:** Javier Vericat, Bryan Aguilera y Giuseppe Suarez
- **Profesores:** Sergi y David Sicart
- **Objetivo:** Establecer una base solida de seguridad tanto en servidores locales (Isard) como en la nube (AWS), configurando accesos seguros, firewalls, politicas de identidad y proteccion contra ataques.

---

## S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard

### 1. Gestion de claves

#### a. Generar claves
Se generan las claves utilizando el algoritmo **ED25519**, que es mas seguro y rapido que RSA:
```bash
ssh-keygen -t ed25519 -C "giuseppe-access"
```
![Generar claves](./imagenes/imagen-002.png)

#### b. Pasar claves
Se copia la clave publica al servidor destino:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub isard@192.168.18.10
```
![Copiar claves](./imagenes/imagen-003.png)

#### c. Comprobacion
Se valida el acceso sin necesidad de contrasena:
![Comprobacion SSH](./imagenes/imagen-004.png)

### 2. Hardening de SSH

Se modifica el archivo de configuracion `/etc/ssh/sshd_config` para aumentar la seguridad:
- **Port 2222:** Cambio de puerto por defecto.
- **PermitRootLogin no:** Prohibir acceso como root.
- **PubkeyAuthentication yes:** Permitir autenticacion por clave.
- **PasswordAuthentication no:** Deshabilitar acceso por contrasena.

![Configuracion SSH](./imagenes/imagen-005.png)

**Comprobacion y reinicio:**
Se valida la sintaxis y se reinicia el servicio:
```bash
sudo sshd -t
sudo systemctl restart ssh
```
![Estado SSH](./imagenes/imagen-007.png)

### 3. Firewall (UFW)

Configuracion restrictiva del firewall:
1. Denegar todo por defecto.
2. Permitir trafico saliente.
3. Permitir solo puertos especificos (2222 para SSH, 80/443 para Nginx).

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp
sudo ufw allow 80/tcp
sudo ufw allow 8443/tcp
sudo ufw enable
```
![Configuracion UFW](./imagenes/imagen-009.png)

### 4. Regla AWS

En el **Security Group** de AWS, se abre el puerto 2222/TCP para permitir el acceso SSH personalizado.
![Regla AWS](./imagenes/imagen-011.png)

---

## S1-02 - Docker + Keycloak - Isard

### 1. Instalacion de Docker

Se instalan los paquetes necesarios y se configuran permisos para el usuario actual:
```bash
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker $USER
```
![Instalacion Docker](./imagenes/imagen-012.png)

### 2. Despliegue de Keycloak

Se crea la estructura de directorios y el archivo `docker-compose.yml` para desplegar Keycloak con una base de datos PostgreSQL:

![Docker Compose Keycloak](./imagenes/imagen-015.png)

**Acceso y Firewall:**
Se abre el puerto 8080 en UFW y se comprueba el acceso web:
![Acceso Keycloak](./imagenes/imagen-017.png)

---

## S1-03 - Configuracion de Seguridad IAM - Isard

### 1. Crear y configurar Realm

Se crea un nuevo **Realm** para gestionar la identidad de la infraestructura:
![Crear Realm](./imagenes/imagen-019.png)

### 2. Autenticacion Multifactor (MFA)

Se configura como accion obligatoria que todos los usuarios configuren un **OTP** (One-Time Password) al primer inicio de sesion:
![Configuracion MFA](./imagenes/imagen-024.png)

### 3. Politicas de Seguridad y Contrasenas

#### Proteccion contra Fuerza Bruta
Se establecen limites estrictos:
- Bloqueo tras 3 fallos.
- Bloqueo permanente hasta intervencion del administrador si persiste el ataque.
- Tiempo de espera entre intentos.

![Politicas Fuerza Bruta](./imagenes/imagen-027.png)

#### Politicas de Contrasenas
Se requiere:
- Digitos, mayusculas, minusculas y caracteres especiales.
- Longitud minima de 10 caracteres.
- No permitir el nombre de usuario en la contrasena.

![Politicas Contrasenas](./imagenes/imagen-031.png)

### 4. Roles, Grupos y Usuarios

Se definen roles con diferentes niveles de privilegio:
- **Administrador:** Acceso total.
- **Trabajador (Operador):** Gestion basica de usuarios.
- **Auditor:** Solo lectura de configuraciones y eventos.

![Gestion de Usuarios](./imagenes/imagen-047.png)

**Pruebas de acceso:**
Validacion del flujo de login con MFA para los diferentes perfiles:
![Login MFA](./imagenes/imagen-056.png)

---

## S1-04 - Hardening Avanzado del Sistema - Isard

### 1. Fail2Ban

Instalacion y configuracion de **Fail2Ban** para proteger el puerto SSH (2222). Si una IP falla 2 veces en 10 minutos, es baneada permanentemente mediante UFW.

```ini
[sshd]
enabled = true
port = 2222
maxretry = 2
bantime = -1
banaction = ufw
```
![Configuracion Fail2Ban](./imagenes/imagen-076.png)

**Comprobacion de baneo:**
![Baneo Fail2Ban](./imagenes/imagen-080.png)

### 2. Actualizaciones Automaticas de Seguridad

Se configura el paquete `unattended-upgrades` para que el sistema aplique parches de seguridad de forma automatica:
```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```
![Actualizaciones Automaticas](./imagenes/imagen-083.png)

---

## S1-05 - Hardening en el Nodo AWS

### 1. Firewall en AWS y en la Instancia

Se replican las politicas de seguridad en la nube:
- **Security Group (AWS):** Puertos 2222 (SSH), 443 (HTTPS), 51820 (WireGuard).
- **UFW (Instancia):** Configuracion identica para mayor defensa en profundidad.

![Firewall AWS](./imagenes/imagen-089.png)

### 2. Fail2Ban en AWS

Se configura Fail2Ban en la instancia de AWS con reglas similares a Isard, ajustando el numero de reintentos a 3.
![Fail2Ban AWS](./imagenes/imagen-092.png)

### 3. Actualizaciones Automaticas en AWS

Activacion de `unattended-upgrades` en el nodo de AWS para asegurar que el sistema este siempre parcheado.
![Actualizaciones AWS](./imagenes/imagen-099.png)

---

## Recomendaciones Adicionales

Para fortalecer aun mas la infraestructura, se sugieren las siguientes implementaciones:

1. **Auditoria de Logs:** Monitorizar activamente `/var/log/fail2ban.log` para identificar patrones de ataque recurrentes.
2. **Hardening de Kernel (sysctl):** Configurar `/etc/sysctl.conf` para deshabilitar IP forwarding (si no es necesario) y proteger contra ataques de ICMP redirects.
3. **Limpieza de Servicios:** Deshabilitar cualquier servicio innecesario que este en escucha (`ss -tunlp`).
4. **Permisos de Archivos:** Verificar que los archivos sensibles de configuracion (como `/etc/ssh/sshd_config`) tengan permisos restrictivos (600).

---
**Resultado:** La infraestructura cuenta ahora con multiples capas de seguridad, desde el acceso a nivel de red (Firewall) hasta la gestion de identidades (IAM/MFA) y la respuesta automatica ante ataques (Fail2Ban).
