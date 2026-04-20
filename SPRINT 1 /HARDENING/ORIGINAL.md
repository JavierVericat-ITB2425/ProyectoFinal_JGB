**N°:** GRUPO X
**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez
**Profesores:** Sergi - David Sicart

# **Índice**
- [S1-01: Acceso SSH Seguro (Hardening Inicial) - Isard](#s1-01-acceso-ssh-seguro-hardening-inicial---isard)
  - [1. Gestion de claves](#1-gestion-de-claves)
    - [1.1 Generar claves](#11-generar-claves)
    - [1.2 Pasar claves](#12-pasar-claves)
  - [2. Handering de SSH](#2-handering-de-ssh)
    - [2.1 Cambiar puerto y archivo de configuración](#21-cambiar-puerto-y-archivo-de-configuracin)
  - [3. Firewall](#3-firewall)
    - [3.1 Crear reglas](#31-crear-reglas)
  - [4. Regla AWS](#4-regla-aws)
  - [5. Instalación de Docker](#5-instalacin-de-docker)
    - [5.1 Instalar paquetes](#51-instalar-paquetes)
    - [5.2 Permisos](#52-permisos)
  - [6. Despliegue de KeyCloak](#6-despliegue-de-keycloak)
    - [6.1 Creación de directorios](#61-creacin-de-directorios)
    - [6.2 Crear y configurar archivo .yml](#62-crear-y-configurar-archivo-yml)
    - [6.3 Firewall Regla](#63-firewall-regla)
  - [7. Crear Realm](#7-crear-realm)
    - [7.1 Configurar Realm](#71-configurar-realm)
  - [8. Autenticación multifactor](#8-autenticacin-multifactor)
    - [8.1 Configuración de Autenticación](#81-configuracin-de-autenticacin)
  - [9. Políticas de ataques](#9-polticas-de-ataques)
  - [10. Políticas de Contraseñas](#10-polticas-de-contraseas)
  - [11. Roles y Grupos](#11-roles-y-grupos)
  - [12. Usuario](#12-usuario)
  - [13. Fail2Ban](#13-fail2ban)
    - [13.1 Instalación del Fail2Ban](#131-instalacin-del-fail2ban)
    - [13.2 Crear configuración inicial](#132-crear-configuracin-inicial)
    - [13.3 Reiniciamos y comprobamos](#133-reiniciamos-y-comprobamos)
  - [14. Actualizaciones Automáticas de Seguridad](#14-actualizaciones-automticas-de-seguridad)
    - [14.1 Configurar actualizaciones automáticas](#141-configurar-actualizaciones-automticas)
    - [14.2 Configuración de política restrictiva](#142-configuracin-de-poltica-restrictiva)
- [S1-05: Hardening en el Nodo AWS](#s1-05-hardening-en-el-nodo-aws)
  - [1. Firewall - AWS](#1-firewall---aws)
    - [1.1 Reglas](#11-reglas)
    - [1.2 Dentro de la Instancia](#12-dentro-de-la-instancia)
  - [2. Fail2Ban](#2-fail2ban)
    - [2.1 Configuración](#21-configuracin)
  - [3. Actualizaciones Automáticas de Seguridad en AWS](#3-actualizaciones-automticas-de-seguridad-en-aws)
    - [3.1 Configurar actualizaciones automáticas](#31-configurar-actualizaciones-automticas)

---

<a name="s1-01-acceso-ssh-seguro-hardening-inicial---isard"></a>
# **S1-01: Acceso SSH Seguro (Hardening Inicial) - Isard**
<a name="1-gestion-de-claves"></a>
## **1. Gestion de claves**
<a name="11-generar-claves"></a>
### **1.1 Generar claves**
   Crearemos las claves con este comando
```bash
ssh-keygen -t ed25519 -C "giuseppe-access"
```
   El algoritmo ed25519 es más seguro y más rápido que el predeterminado como es el RSA
<a name="12-pasar-claves"></a>
### **1.2 Pasar claves**
   Ahora pasaremos las claves con el servidor que en este caso es del compañero
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub isard@192.168.18.10
```
#### **Comprobación**
   Podemos hacer ssh y no nos pide la contraseña
<a name="2-handering-de-ssh"></a>
## **2. Handering de SSH**
<a name="21-cambiar-puerto-y-archivo-de-configuracin"></a>
### **2.1 Cambiar puerto y archivo de configuración**
   Accedemos al archivo de configuración
```bash
sudo nano /etc/ssh/sshd_config
```
   Ahora hemos modificado lo siguiente:

   - **Port 2222** → Modificamos el puerto que viene por defecto para evitar ataques de botnets
   - **PermitRootLogin no** → Prohibimos que un usuario con root o superusuario pueda acceder directamente
   - **PubkeyAuthentication yes** → Indicamos al servidor que puedas procesar y aceptar claves como la configurada
   - **PasswordAuthentication yes** → Obligamos a que el servidor solo acepte claves, y no contraseñas, lo cual lo hace que no se pueda intentar ataques de fuerza bruta
#### **Comprobación**
   Validamos si la configuración se ha realizado correctamente o hay algo en el archivo incorrecto
   Y seguidamente hicimos un reinicio del servicio
   Para finalizar miramos el servicio
<a name="3-firewall"></a>
## **3. Firewall**
<a name="31-crear-reglas"></a>
### **3.1 Crear reglas**
   Bloqueamos todo y solo dejaremos lo que necesitaremos
   Ahora lo que haremos es habilitar solo lo necesario:

   - **sudo ufw default allow outgoing** → Indicamos que los paquetes desde el servidor hacia fuera sean habilitados
   ```bash
   sudo ufw default allow outgoing
   ```
   - **sudo ufw allow 2222/tcp** → Son para el SSH, cambiamos el puerto por defecto a 2222 para mayor seguridad
   ```bash
   sudo ufw allow 2222/tcp
   ```
   - **sudo ufw allow 80/tcp y 8443/tcp** → Son para el servicio de Nginx (HTTP y HTTPS)
   ```bash
   sudo ufw allow 80/tcp
   sudo ufw allow 8443/tcp
   ```
   - **sudo ufw enable** → Activa el Firewall con la configuración establecida
   ```bash
   sudo ufw enable
   ```
<a name="4-regla-aws"></a>
## **4. Regla AWS**
   1. Security Group
      Ahora desde AWS, deberemos de crear una regla de entrada indicando el protocolo TCP y que sea por el puerto 2222
      # **S1-02: Docker + Keycloak - Isard**
<a name="5-instalacin-de-docker"></a>
## **5. Instalación de Docker**
<a name="51-instalar-paquetes"></a>
### **5.1 Instalar paquetes**
   Ejecutamos lo siguiente para instalar los paquetes
```bash
sudo apt install docker.io docker-compose -y
```
<a name="52-permisos"></a>
### **5.2 Permisos**
   Para evitar usar sudo, lo que haremos es lo siguiente
```bash
sudo usermod -aG docker $USER
```
<a name="6-despliegue-de-keycloak"></a>
## **6. Despliegue de KeyCloak**
<a name="61-creacin-de-directorios"></a>
### **6.1 Creación de directorios**
   Crearemos las carpetas
```bash
mkdir -p ~/zth-node-cloud/keycloak
```
```bash
cd ~/zth-node-cloud/keycloak
```
<a name="62-crear-y-configurar-archivo-yml"></a>
### **6.2 Crear y configurar archivo .yml**
   Creamos el archivo con el comando
```bash
sudo nano docker-compose.yml
```
   Y el contenido de este es el siguiente
```yaml
version: '3.8'
services:
  zth-postgres:
    image: postgres:16
    container_name: zth-keycloak-db
    restart: always
    volumes:
      - zth_db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: K3yl0ack_ZTH-db
    networks:
      - zth-network
  zth-keycloak:
    image: quay.io/keycloak/keycloak:24.0
    container_name: zth-keycloak-server
    command: start-dev
    restart: always
    environment:
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://zth-postgres:5432/keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: K3yl0ack_ZTH-db
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: K3yl0ack-ZTH_
      KC_HTTP_ENABLED: "true"
      KC_PROXY: edge
    ports:
      - "8080:8080"
    depends_on:
      - zth-postgres
    networks:
      - zth-network
networks:
  zth-network:
    driver: bridge
volumes:
  zth_db_data:
```
<a name="63-firewall-regla"></a>
### **6.3 Firewall Regla**
   Ahora deberemos de crear una nueva regla para poder acceder al Keycloak
```bash
sudo ufw allow 8080/tcp
```
#### **Comprobación**
   Ahora accederemos mediante la IP del servidor :8080 y nos pide las credenciales
   Después de poner las credenciales, accedemos correctamente
   # **S1-03: Configuración de Seguridad IAM - Isard**
<a name="7-crear-realm"></a>
## **7. Crear Realm**
<a name="71-configurar-realm"></a>
### **7.1 Configurar Realm**
   Ejecutamos lo siguiente para instalar los paquetes
   Ahora le damos clic en Create Realm
   Ahora crearemos el Realm
   Ya hemos creado el Realm Correctamente
<a name="8-autenticacin-multifactor"></a>
## **8. Autenticación multifactor**
<a name="81-configuracin-de-autenticacin"></a>
### **8.1 Configuración de Autenticación**
   Desde nuestro Realm, nos vamos al apartado de configure y nos dirigimos donde nos dice Authentication
   Ahora nos dirigimos donde nos indican Requiere Actions
   Ahora marcamos la opción de Set as Default Action
   Esto lo que hará es que todo usuario nuevo deberá de tener una app para autenticarse
<a name="9-polticas-de-ataques"></a>
## **9. Políticas de ataques**
   Ahora deberemos de dirigirnos a donde dice Realm Settings y después a Security Defense
   En este caso en el apartado de Brute Force
   Elegimos esta última opción porque es la más segura de todas, es la que tiene tolerancia cero
   Lo configuraremos de la siguiente manera:

   - **Max login failures** → Indicamos que si el “usuario” ha fallado 3 veces, el sistema ejecuta las acciones que hemos configurado a continuación
   - **Maximum temporary lockouts** → Aquí le decimos que si el “usuario” ya ha fallado 1 vez y en este segundo intento vuelve a fallar, la cuenta queda bloqueada para siempre y el unico metodo de desbloqueo es que el administrador, lo haga manualmente
   - **Wait increment** → Después de haber fallado la 1a vez, no pueden intentarlo al momento, deberán de esperar unos 5 minutos para volver a intentar
   - **Quick login check** → Bloqueamos ataque de fuerza bruta o de diccionario, aquí le indicamos que si han intentado iniciar sesion en menos de 2 segundos son bots o ataques
   - **Failure reset time** → Es el tiempo en que quedan registrados los intentos, se quedan guardados en memoria durante X tiempo, si por ejemplo fallan 1 vez por la mañana y 2 por la tarde se bloquea la cuenta
#### **Comprobación**
   SI intentamos acceder y fallamos 3 veces la contraseña
   En el panel de administración, vemos que se ha bloqueado temporalmente
<a name="10-polticas-de-contraseas"></a>
## **10. Políticas de Contraseñas**
   Nos vamos al apartado de Authentication y nos vamos al apartado de Polices
   Ahora en el apartado de Add Policy y marcamos las siguientes opciones
- **Digit**: Obligamos a poner dígito como mínimo
- **Not Username**: Evitamos que la contraseña pongan el nombre del usuario
- **Lowercase Characters**: Obligatorio poner mayúscula como mínimo
- **Uppercase Characters**: Obligatorio poner mayúscula como mínimo
- **Special Characters**: ** Obligatorio poner un carácter especial
- **Minimum Length**: ** Un mínimo de longitud obligatoria
   Ahora lo configuramos de la siguiente manera
   Obligamos a que las contraseñas tengan como mínimo 10 caracteres
<a name="11-roles-y-grupos"></a>
## **11. Roles y Grupos**
   Ahora crearemos los roles estos se hacen desde Realm Roles
   El primer role que crearemos será el de Administrador
   Ahora el otro Role seria de Trabajadores
   Ahora el otro Role seria de Auditoría
   Ahora crearemos también un grupo que sea Administradores
   Le asignamos un nombre
   Ahora crearemos el otro grupo como es el de los Trabajadores
   Ahora crearemos el Grupo de Auditoría
<a name="12-usuario"></a>
## **12. Usuario**

   Antes de realizar la comprobación, deberemos de crear un usuario:

   - **Configurar OTP**: Seleccionamos esta opción para que el usuario deba usar una aplicación de OTP en su próximo inicio de sesión.
   - **Formulario**: Completamos los datos y marcamos que el email debe ser verificado.
   - **Grupo**: Le asignamos al grupo de **Administradores**.
   - **Contraseña**: Asignamos una contraseña y desactivamos la opción "temporal" para que muestre el código QR de MFA de inmediato.
   - **Rol**: Asignamos el rol de **Administrador** al usuario.

   **Otros usuarios creados**:

   - **Operador**: Asignado al grupo de **Trabajadores** con su respectiva contraseña y rol.
   - **Auditor**: Asignado al grupo de **Auditoría** con su respectiva contraseña y rol.

#### **Comprobación**

   ##### **giuseppe-admin**

   Iniciaremos sesión con el usuario de administrador:

   - **Aplicación MFA**: Nos pedirá instalar una app como Microsoft Authenticator, Google Authenticator o FreeOT. En nuestro caso, usamos **Google Authenticator**.
   - **Configuración**: Al escanear el QR, se añade la cuenta `zth-node-cloud`.
   - **Acceso**: Tras introducir el código y darle a Submit, accedemos al panel.
   - **Panel de Usuario**: Podemos ver los detalles del inicio de sesión, la contraseña y el estado del MFA (dispositivo, fecha y hora).

   ##### **operador-bryan**

   Iniciaremos sesión con permisos de trabajador:

   - **MFA**: Al igual que el administrador, solicitará la configuración de la aplicación OTP.
   - **Permisos**: Al ser un rol de Trabajador, el acceso es limitado (solo funciones básicas como usuarios y eventos).
   - **Gestión**: Puede ver otros usuarios y, al ser Operador LVL 1, puede añadir usuarios, resetear contraseñas o borrar cuentas con menos privilegios que él.

   ##### **Auditor-Javi**

   Iniciaremos sesión con los permisos de auditor:

   - **MFA**: Configuramos el acceso mediante la aplicación.
   - **Roles y Usuarios**: Puede visualizar los roles y usuarios, pero no tiene permisos para crear o modificar nada (aparecerá un mensaje de error si intenta crear un usuario).
   - **Grupos y Eventos**: Puede ver los grupos, sus miembros y, fundamentalmente, los eventos del sistema y de los administradores para su labor de auditoría.
   # **S1-04: Hardening Avanzado del Sistema - Isard**
<a name="13-fail2ban"></a>
## **13. Fail2Ban**
<a name="131-instalacin-del-fail2ban"></a>
### **13.1 Instalación del Fail2Ban**
   Primero de todo realizaremos la instalación
```bash
sudo apt install fail2ban -y
```
<a name="132-crear-configuracin-inicial"></a>
### **13.2 Crear configuración inicial**
   Creamos un .local para que si hay alguna actualización, que no se nos escriba el archivo .conf
```bash
sudo nano /etc/fail2ban/jail.local
```
```bash
[sshd]
   enabled = true
   port = 2222
   filter = sshd
   logpath = /var/log/auth.log
   maxretry = 2
   findtime = 600
   bantime = -1
   banaction = ufw
```
   - **[sshd]** → Indicamos que esta configuración se aplica al ssh
   - **enabled = true** → Indicamos que se ejecute
   - **port = 2222** → Indicamos en qué puerto debe de ejecutarse
   - **filter = sshd** → Es un filtro para que busque errores como invalid password etc
   - **logpath = /var/log/auth.log** → Es donde se revisaran los logs
   - **maxretry = 2** → Número máximo de intentos
   - **findtime = 600** → Si fallo 2 veces, en menos de 10 min, se ejecuta el baneo
   - **bantime = -1** → Indicamos que el baneo sea permanente
   - **banaction = ufw** → Indicamos que lo haga mediante ufw en vez de iptables, cuya ventaja es que lo bloquea directamente el firewall
<a name="133-reiniciamos-y-comprobamos"></a>
### **13.3 Reiniciamos y comprobamos**
   Ahora vamos a reiniciar el servicio
```bash
sudo systemctl restart fail2ban
```
   Ahora lo vamos a comprobar
```bash
sudo fail2ban-client status sshd
```
#### **Comprobación**
   Si desde cliente intentó acceder y fallo las contraseñas, nos sale este mensaje
   Si accedemos al server y miramos el fail2ban podemos ver que tenemos esta IP bloqueada
   Corroboramos que la IP del cliente es la baneada
   Para desbanear haremos esto
   Como podemos ver, tenemos la IP baneada, haremos un unban y la IP que queremos desbloquear y después de hacer eso, vemos que ya no tenemos la IP baneada
<a name="14-actualizaciones-automticas-de-seguridad"></a>
## **14. Actualizaciones Automáticas de Seguridad**
<a name="141-configurar-actualizaciones-automticas"></a>
### **14.1 Configurar actualizaciones automáticas**
      Para minimizar las vulnerabilidades, deberemos de indicar que los “parches” de seguridad se hagan de manera automática
      Para ello usaremos el siguiente comando
```bash
sudo apt install unattended-upgrades -y
```

```bash
sudo dpkg-reconfigure --priority=low
```
  - **sudo apt install unattended-upgrades -y** → Es un paquete el cual permite descargar actualizaciones y aplicarse automáticamente sin necesidad de interacción con el usuario
  - **sudo dpkg-reconfigure** → Aquí le indicamos que queremos reconfigurar el programa X
  - **--priority=low** → Indicamos que nos muestre todas las opciones posibles
  - **unattended-upgrades** → Su función es la de revisar repositorios y si hay un nuevo “parche” lo descarga y se aplica, busca vulnerabilidades
  Ejecutamos el siguiente comando
```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```
#### **Comprobación**
   Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando
```bash
systemctl status unattended-upgrades
```
<a name="142-configuracin-de-poltica-restrictiva"></a>
### **14.2 Configuración de política restrictiva**
Ahora le indicaremos al firewall que bloquee cualquier paquete siempre y cuando no le hemos indicado lo contrario
```bash
sudo ufw default deny incoming
```
```bash
sudo ufw status verbose
```
[http://192.168.18.10:8080/realms/zth-node-cloud/account](http://192.168.18.10:8080/realms/zth-node-cloud/account)
---
<a name="s1-05-hardening-en-el-nodo-aws"></a>
# **S1-05: Hardening en el Nodo AWS**
<a name="1-firewall---aws"></a>
## **1. Firewall - AWS**
<a name="11-reglas"></a>
### **1.1 Reglas**
   En AWS creamos reglas para el ssh por el puerto 2222
   Ahora indicamos el puerto de HTTPS para la web
   Todo lo que no se encuentra aqui sera denegado con DROP
<a name="12-dentro-de-la-instancia"></a>
### **1.2 Dentro de la Instancia**
   Ahora realizaremos la configuración dentro de la instancia, denegamos todo
```bash
sudo ufw default deny incoming
```
```bash
sudo ufw default allow outgoing
```
   Creamos las reglas de SSH con puerto 222, tenemos también HTTPS con 443 y tenemos también el WireGuard
```bash
sudo ufw allow 2222/tcp
```
```bash
sudo ufw allow 8080/tcp
```
```bash
sudo ufw allow 443/tcp
```
```bash
sudo ufw allow 51820/udp
```
```bash
sudo ufw enable
```
   Revisión de las reglas
```bash
sudo ufw status verbose
```
   Para finalizar activamos el Firewall
```bash
sudo ufw enable
```
   Revisión de las reglas
```bash
sudo ufw status verbose
```
<a name="2-fail2ban"></a>
## **2. Fail2Ban**
<a name="21-configuracin"></a>
### **2.1 Configuración**
   Ahora el archivo de configuración, tendríamos lo siguiente
```bash
sudo nano /etc/fail2ban/jail.local
```
```bash
[sshd]
   enabled = true
   port = 2222
   filter = sshd
   logpath = /var/log/auth.log
   maxretry = 3
   findtime = 600
   bantime = -1
   banaction = ufw
```
```bash
sudo cat /etc/fail2ban/jail.local
```
#### **Comprobación**
   Desde la máquina host fallamos 3 veces la contraseña a propósito
   Ahora miramos la IP que tenemos PÚBLICA
   Ahora desde el servidor de AWS podemos ver el baneo
<a name="3-actualizaciones-automticas-de-seguridad-en-aws"></a>
## **3. Actualizaciones Automáticas de Seguridad en AWS**
<a name="31-configurar-actualizaciones-automticas"></a>
### **3.1 Configurar actualizaciones automáticas**
   Como hemos realizado antes en ISARD, configuraremos para que se hagan las actualizaciones automáticas
   Instalaremos el paquete inicialmente
```bash
sudo apt update && sudo apt install unattended-upgrades -y
```
   Ejecutamos el siguiente comando
```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```
#### **Comprobación**
   Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando
```bash
systemctl status unattended-upgrades
```
