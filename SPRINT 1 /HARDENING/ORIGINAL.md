**N°:** GRUPO X
**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez
**Profesores:** Sergi - David Sicart

# **Índice**
- [S1-01: Acceso SSH Seguro (Hardening Inicial) - Isard](#s1-01-acceso-ssh-seguro-hardening-inicial---isard)
  - [1. Gestion de claves](#1-gestion-de-claves)
    - [2. Generar claves](#2-generar-claves)
    - [3. Pasar claves](#3-pasar-claves)
  - [4. Handering de SSH](#4-handering-de-ssh)
    - [5. Cambiar puerto y archivo de configuración](#5-cambiar-puerto-y-archivo-de-configuracin)
  - [6. Firewall](#6-firewall)
    - [7. Crear reglas](#7-crear-reglas)
  - [8. Regla AWS](#8-regla-aws)
  - [9. Instalación de Docker](#9-instalacin-de-docker)
    - [10. Instalar paquetes](#10-instalar-paquetes)
    - [11. Permisos](#11-permisos)
  - [12. Despliegue de KeyCloak](#12-despliegue-de-keycloak)
    - [13. Creación de directorios](#13-creacin-de-directorios)
    - [14. Crear y configurar archivo .yml](#14-crear-y-configurar-archivo-yml)
    - [15. Firewall Regla](#15-firewall-regla)
  - [16. Crear Realm](#16-crear-realm)
    - [17. Configurar Realm](#17-configurar-realm)
  - [18. Autenticación multifactor](#18-autenticacin-multifactor)
    - [19. Configuración de Autenticación](#19-configuracin-de-autenticacin)
  - [20. Políticas de ataques](#20-polticas-de-ataques)
  - [21. Políticas de Contraseñas](#21-polticas-de-contraseas)
  - [22. Roles y Grupos](#22-roles-y-grupos)
  - [23. Usuario](#23-usuario)
  - [24. Fail2Ban](#24-fail2ban)
    - [25. Instalación del Fail2Ban](#25-instalacin-del-fail2ban)
    - [26. Crear configuración inicial](#26-crear-configuracin-inicial)
    - [27. Reiniciamos y comprobamos](#27-reiniciamos-y-comprobamos)
  - [28. Actualizaciones Automáticas de Seguridad](#28-actualizaciones-automticas-de-seguridad)
    - [29. Configurar actualizaciones automáticas](#29-configurar-actualizaciones-automticas)
    - [30. Configuración de política restrictiva](#30-configuracin-de-poltica-restrictiva)
- [S1-05: Hardening en el Nodo AWS](#s1-05-hardening-en-el-nodo-aws)
  - [1. Firewall - AWS](#1-firewall---aws)
    - [2. Reglas](#2-reglas)
    - [3. Dentro de la Instancia](#3-dentro- de-la-instancia)
  - [4. Fail2Ban](#4-fail2ban)
    - [5. Configuración](#5-configuracin)
  - [6. Actualizaciones Automáticas de Seguridad en AWS](#6-actualizaciones-automticas-de-seguridad-en-aws)
    - [7. Configurar actualizaciones automáticas](#7-configurar-actualizaciones-automticas)

---

<a name="s1-01-acceso-ssh-seguro-hardening-inicial---isard"></a>
# **S1-01: Acceso SSH Seguro (Hardening Inicial) - Isard**

<a name="1-gestion-de-claves"></a>
## **1. Gestion de claves**

<a name="2-generar-claves"></a>
### **2. Generar claves**

   Crearemos las claves con este comando

```bash
ssh-keygen -t ed25519 -C "giuseppe-access"
```

   El algoritmo ed25519 es más seguro y más rápido que el predeterminado como es el RSA

<a name="3-pasar-claves"></a>
### **3. Pasar claves**

   Ahora pasaremos las claves con el servidor que en este caso es del compañero

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub isard@192.168.18.10
```

#### **Comprobación**

   Podemos hacer ssh y no nos pide la contraseña

<a name="4-handering-de-ssh"></a>
## **4. Handering de SSH**

<a name="5-cambiar-puerto-y-archivo-de-configuracin"></a>
### **5. Cambiar puerto y archivo de configuración**

   Accedemos al archivo de configuración

```bash
sudo nano /etc/ssh/sshd_config
```

   Ahora hemos modificado lo siguiente

   **Port 2222** → Modificamos el puerto que viene por defecto para evitar ataques de botnets

   **PermitRootLogin no →** Prohibimos que un usuario con root o superusuario pueda acceder directamente

   **PubkeyAuthentication yes →** Indicamos al servidor que puedas procesar y aceptar claves como la configurada

   **PasswordAuthentication yes →** Obligamos a que el servidor solo acepte claves, y no contraseñas, lo cual lo hace que no se pueda intentar ataques de fuerza bruta

#### **Comprobación**

   Validamos si la configuración se ha realizado correctamente o hay algo en el archivo incorrecto

   Y seguidamente hicimos un reinicio del servicio

   Para finalizar miramos el servicio

<a name="6-firewall"></a>
## **6. Firewall**

<a name="7-crear-reglas"></a>
### **7. Crear reglas**

   Bloqueamos todo y solo dejaremos lo que necesitaremos

   Ahora lo que haremos es habilitar solo lo necesario

**sudo ufw default allow outgoing →** Indicamos que los paquetes desde el servidor hacia fuera sean habilitados

```bash
sudo ufw default allow outgoing
```

**sudo ufw allow 2222/tcp como el puerto 22** → Son para el SSH, primero el ssh es puerto 22 pero luego lo cambiamos a puerto 2222 para seguridad

```bash
sudo ufw allow 2222/tcp
```

**sudo ufw allow 80/tcp y 8443/tcp**→ Son para el servicio de Nginx tanto el http como https

```bash
sudo ufw allow 80/tcp
```

```bash
sudo ufw allow 8443/tcp
```

**sudo ufw enable** Hacemos que ahora quede configurado siempre que se inicie el servidor con esta configuración

```bash
sudo ufw enable
```

<a name="8-regla-aws"></a>
## **8. Regla AWS**

   1. Security Group

      Ahora desde AWS, deberemos de crear una regla de entrada indicando el protocolo TCP y que sea por el puerto 2222

      # **S1-02: Docker + Keycloak - Isard**

<a name="9-instalacin-de-docker"></a>
## **9. Instalación de Docker**

<a name="10-instalar-paquetes"></a>
### **10. Instalar paquetes**

   Ejecutamos lo siguiente para instalar los paquetes

```bash
sudo apt install docker.io docker-compose -y
```

<a name="11-permisos"></a>
### **11. Permisos**

   Para evitar usar sudo, lo que haremos es lo siguiente

```bash
sudo usermod -aG docker $USER
```

<a name="12-despliegue-de-keycloak"></a>
## **12. Despliegue de KeyCloak**

<a name="13-creacin-de-directorios"></a>
### **13. Creación de directorios**

   Crearemos las carpetas

```bash
mkdir -p ~/zth-node-cloud/keycloak
```

```bash
cd ~/zth-node-cloud/keycloak
```

<a name="14-crear-y-configurar-archivo-yml"></a>
### **14. Crear y configurar archivo .yml**

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

<a name="15-firewall-regla"></a>
### **15. Firewall Regla**

   Ahora deberemos de crear una nueva regla para poder acceder al Keycloak

```bash
sudo ufw allow 8080/tcp
```

#### **Comprobación**

   Ahora accederemos mediante la IP del servidor :8080 y nos pide las credenciales

   Después de poner las credenciales, accedemos correctamente

   # **S1-03: Configuración de Seguridad IAM - Isard**

<a name="16-crear-realm"></a>
## **16. Crear Realm**

<a name="17-configurar-realm"></a>
### **17. Configurar Realm**

   Ejecutamos lo siguiente para instalar los paquetes

   Ahora le damos clic en Create Realm

   Ahora crearemos el Realm

   Ya hemos creado el Realm Correctamente

<a name="18-autenticacin-multifactor"></a>
## **18. Autenticación multifactor**

<a name="19-configuracin-de-autenticacin"></a>
### **19. Configuración de Autenticación**

   Desde nuestro Realm, nos vamos al apartado de configure y nos dirigimos donde nos dice Authentication

   Ahora nos dirigimos donde nos indican Requiere Actions

   Ahora marcamos la opción de Set as Default Action

   Esto lo que hará es que todo usuario nuevo deberá de tener una app para autenticarse

<a name="20-polticas-de-ataques"></a>
## **20. Políticas de ataques**

   Ahora deberemos de dirigirnos a donde dice Realm Settings y después a Security Defense

   En este caso en el apartado de Brute Force

   Elegimos esta última opción porque es la más segura de todas, es la que tiene tolerancia cero

   Lo configuraremos de la siguiente manera

   **Max login failures →** Indicamos que si el “usuario” ha fallado 3 veces, el sistema ejecuta las acciones que hemos configurado a continuación

   **Maximum temporary lockouts →** Aquí le decimos que si el “usuario” ya ha fallado 1 vez y en este segundo intento vuelve a fallar, la cuenta queda bloqueada para siempre y el unico metodo de desbloqueo es que el administrador, lo haga manualmente

   **Wait increment →** Después de haber fallado la 1a vez, no pueden intentarlo al momento, deberán de esperar unos 5 minutos para volver a intentar

   **Quick login check →** Bloqueamos ataque de fuerza bruta o de diccionario, aquí le indicamos que si han intentado iniciar sesion en menos de 2 segundos son bots o ataques

   **Failure reset time →** Es el tiempo en que quedan registrados los intentos, se quedan guardados en memoria durante X tiempo, si por ejemplo fallan 1 vez por la mañana y 2 por la tarde se bloquea la cuenta

#### **Comprobación**

   SI intentamos acceder y fallamos 3 veces la contraseña

   En el panel de administración, vemos que se ha bloqueado temporalmente

<a name="21-polticas-de-contraseas"></a>
## **21. Políticas de Contraseñas**

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

<a name="22-roles-y-grupos"></a>
## **22. Roles y Grupos**

   Ahora crearemos los roles estos se hacen desde Realm Roles

   El primer role que crearemos será el de Administrador

   Ahora el otro Role seria de Trabajadores

   Ahora el otro Role seria de Auditoría

   Ahora crearemos también un grupo que sea Administradores

   Le asignamos un nombre

   Ahora crearemos el otro grupo como es el de los Trabajadores

   Ahora crearemos el Grupo de Auditoría

<a name="23-usuario"></a>
## **23. Usuario**

   Antes de realizar la comprobación. deberemos de crear un usuario

   Aquí seleccionamos la opción de Configurar OTP

   Esto lo que hará es decir al KeyCloak que este usuario la próxima vez que inicie sesión deberá de usar una aplicación de OTP para poder iniciar sesión

   Completamos el formulario

   Indicamos que el mail deberá de ser verificado

   Ahora le asignamos al grupo de Administradores

   Le asignaremos una contraseña

   Aquí desactivamos la contraseña temporal para que no la pida cambiar y para que salga el código QR para de MFA

   Ahora asignaremos este usuario creado al Rol de Administrador

   Indicamos que es del rol Administrador y listo ya lo tendríamos

   Ahora creamos el usuario Operador, lo asignamos al grupo de Trabajadores

   Ahora asignamos una contraseña

   Ahora indicamos el rol que en este caso es Trabajador

   Ahora creamos el usuario de Auditor

   Como hemos realizado antes, asignamos una contraseña

   Una vez creado, le indicamos el rol que es en este caso de Auditoría

#### **Comprobación**

   #### **giuseppe-admin**     Iniciaremos sesión con el usuario de administrador

   Ahora nos aparecerá lo siguiente

   Vemos que nos pide instalar una aplicación de autenticación como puede ser Microsoft Authenticator

   Google Authenticator

   FreeOT

   En nuestro caso lo haríamos con Google Authenticator

   Al escanearlo, nos aparecerá que se añadió un nuevo código y que de nombre tiene zth-node-cloud. Después de poner el código puse desde el dispositivo el cual se hizo en este caso fue con el S24 Ultra de Giuseppe

   Cuando el demos a Submit nos aparecerá lo siguiente

   En este apartado podemos ver que tenemos el inicio de sesión, tanto la contraseña y si queremos cambiarla como MFA que en este caso es desde el dispositivo que hemos indicado, podemos ver cuando fue y a que hora

   #### **operador-bryan**     Ahora iniciaremos sesión con permisos de trabajador
   Nos pedirá como antes usar una aplicación de la ya mencionadas

   Iniciamos

   Este usuario al ser solo Trabajador tiene sólo acceso básico

   Como puede ser usuarios y eventos

   Puede ver los otros usuarios

   Y como es un Operador LVL 1 puede añadir usuarios, puede resetear contraseñas o borrar, siempre y cuando tengan menos privilegios que el

   #### **Auditor-Javi**

   Ahora iniciamos sesión con los permisos de auditor

   Nos pedirá el MFA

   Configuramos

   Una vez iniciado sesión
   Con este usuario podemos ver los roles, pero no crear ni hacer otra operación

   Por otro lado también podemos ver los Usuarios

   Aunque ponga add user este usuario solo puede ver los campos que existen, porque si intenta crear aparecerá el siguiente mensaje

   Podemos ver grupos y sus miembros

   Como auditor, debe de ver los eventos

   También los eventos de administradores

   # **S1-04: Hardening Avanzado del Sistema - Isard**

<a name="24-fail2ban"></a>
## **24. Fail2Ban**

<a name="25-instalacin-del-fail2ban"></a>
### **25. Instalación del Fail2Ban**

   Primero de todo realizaremos la instalación

```bash
sudo apt install fail2ban -y
```

<a name="26-crear-configuracin-inicial"></a>
### **26. Crear configuración inicial**

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

   **[sshd] →** Indicamos que esta configuración se aplica al ssh

   **enabled = true →** Indicamos que se ejecute

   **port = 2222 →** Indicamos en qué puerto debe de ejecutarse

   **filter = sshd →** Es un filtro para que busque errores como invalid password etc

   **logpath = /var/log/auth.log →** Es donde se revisaran los logs

   **maxretry = 2 →** Número máximo de intentos

   **findtime = 600 →** Si fallo 2 veces, en menos de 10 min, se ejecuta el baneo

   **bantime = -1 →** Indicamos que el baneo sea permanente

   **banaction = ufw →** Indicamos que lo haga mediante ufw en vez de iptables, cuya ventaja es que lo bloquea directamente el firewall

<a name="27-reiniciamos-y-comprobamos"></a>
### **27. Reiniciamos y comprobamos**

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

<a name="28-actualizaciones-automticas-de-seguridad"></a>
## **28. Actualizaciones Automáticas de Seguridad**

<a name="29-configurar-actualizaciones-automticas"></a>
### **29. Configurar actualizaciones automáticas**

      Para minimizar las vulnerabilidades, deberemos de indicar que los “parches” de seguridad se hagan de manera automática

      Para ello usaremos el siguiente comando

```bash
sudo apt install unattended-upgrades -y sudo dpkg-reconfigure --priority=low
```

  **sudo apt install unattended-upgrades -y →** Es un paquete el cual permite descargar actualizaciones y aplicarse automáticamente sin necesidad de interacción con el usuario

  **sudo dpkg-reconfigure →** Aquí le indicamos que queremos reconfigurar el programa X

  **--priority=low →** Indicamos que nos muestre todas las opciones posibles

  **unattended-upgrades →** Su función es la de revisar repositorios y si hay un nuevo “parche” lo descarga y se aplica, busca vulnerabilidades

  Ejecutamos el siguiente comando

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

#### **Comprobación**

   Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando

```bash
systemctl status unattended-upgrades
```

<a name="30-configuracin-de-poltica-restrictiva"></a>
### **30. Configuración de política restrictiva**

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

<a name="2-reglas"></a>
### **2. Reglas**

   En AWS creamos reglas para el ssh por el puerto 2222

   Ahora indicamos el puerto de HTTPS para la web

   Todo lo que no se encuentra aqui sera denegado con DROP

<a name="3-dentro-de-la-instancia"></a>
### **3. Dentro de la Instancia**

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

<a name="4-fail2ban"></a>
## **4. Fail2Ban**

<a name="5-configuracin"></a>
### **5. Configuración**

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

<a name="6-actualizaciones-automticas-de-seguridad-en-aws"></a>
## **6. Actualizaciones Automáticas de Seguridad en AWS**

<a name="7-configurar-actualizaciones-automticas"></a>
### **7. Configurar actualizaciones automáticas**

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
