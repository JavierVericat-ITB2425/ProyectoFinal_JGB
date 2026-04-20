<a name="s1-02---acceso-ssh-seguro-hardening-inicial"></a>
# **S1-02 - Acceso SSH Seguro (Hardening Inicial)**
# **Índice**
- [S1-02 - Acceso SSH Seguro (Hardening Inicial)](#s1-02---acceso-ssh-seguro-hardening-inicial)
- [S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard](#s1-01---acceso-ssh-seguro-hardening-inicial---isard)
  - [Gestion de claves](#gestion-de-claves)
    - [Generar claves](#generar-claves)
    - [Pasar claves](#pasar-claves)
    - [Comprobación](#comprobacin)
  - [Handering de SSH](#handering-de-ssh)
    - [Cambiar puerto y archivo de configuración](#cambiar-puerto-y-archivo-de-configuracin)
    - [Comprobacion](#comprobacion)
  - [Firewall](#firewall)
    - [Crear reglas](#crear-reglas)
  - [Regla AWS](#regla-aws)
  - [Instalación de Docker](#instalacin-de-docker)
    - [Instalar paquetes](#instalar-paquetes)
    - [Permisos](#permisos)
  - [Despliegue de KeyCloak](#despliegue-de-keycloak)
    - [Creación de directorios](#creacin-de-directorios)
    - [Crear y configurar archivo .yml](#crear-y-configurar-archivo-yml)
    - [Firewall Regla](#firewall-regla)
    - [Comprobación](#comprobacin)
  - [Crear Realm](#crear-realm)
    - [Configurar Realm](#configurar-realm)
  - [Autenticación multifactor](#autenticacin-multifactor)
    - [Configuración de Autenticación](#configuracin-de-autenticacin)
    - [Políticas de ataques](#polticas-de-ataques)
    - [Comprobación](#comprobacin)
    - [Políticas de Contraseñas](#polticas-de-contraseas)
    - [Roles y Grupos](#roles-y-grupos)
    - [Usuario](#usuario)
    - [Comprobacion](#comprobacion)
  - [Fail2Ban](#fail2ban)
    - [Instalación del Fail2Ban](#instalacin-del-fail2ban)
    - [Crear configuración inicial](#crear-configuracin-inicial)
    - [Reiniciamos y comprobamos](#reiniciamos-y-comprobamos)
    - [COMPROBACION](#comprobacion)
  - [Actualizaciones Automáticas de Seguridad](#actualizaciones-automticas-de-seguridad)
    - [Comprobacion](#comprobacion)
- [S1-05: Hardening en el Nodo AWS](#s1-05-hardening-en-el-nodo-aws)
  - [Firewall - AWS](#firewall---aws)
    - [Reglas](#reglas)
    - [Dentro de la Instancia](#dentro-de-la-instancia)
  - [Fail2Ban](#fail2ban)
    - [Configuración](#configuracin)
    - [COmprobacion](#comprobacion)
  - [Actualizaciones Automáticas de Seguridad en AWS](#actualizaciones-automticas-de-seguridad-en-aws)
    - [Configurar actualizaciones automáticas](#configurar-actualizaciones-automticas)
    - [Comprobacion](#comprobacion)


---

**N°:** GRUPO X

**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez

**Profesores:** Sergi - David Sicart

<a name="s1-01---acceso-ssh-seguro-hardening-inicial---isard"></a>
# **S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard**
<a name="gestion-de-claves"></a>
1. **Gestion de claves**

<a name="generar-claves"></a>
1. **Generar claves**

   Crearemos las claves con este comando

```bash
ssh-keygen -t ed25519 -C "giuseppe-access"
```

   El algoritmo ed25519 es más seguro y más rápido que el predeterminado como es el RSA

<a name="pasar-claves"></a>
2. **Pasar claves**

   Ahora pasaremos las claves con el servidor que en este caso es del compañero

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub isard@192.168.18.10
```

<a name="comprobacin"></a>
3. **Comprobación**

   Podemos hacer ssh y no nos pide la contraseña

<a name="handering-de-ssh"></a>
2. **Handering de SSH**

<a name="cambiar-puerto-y-archivo-de-configuracin"></a>
1. **Cambiar puerto y archivo de configuración**

   Accedemos al archivo de configuración

```bash
sudo nano /etc/ssh/sshd_config
```

   Ahora hemos modificado lo siguiente

   **Port 2222** → Modificamos el puerto que viene por defecto para evitar ataques de botnets

   **PermitRootLogin no →** Prohibimos que un usuario con root o superusuario pueda acceder directamente

   **PubkeyAuthentication yes →** Indicamos al servidor que puedas procesar y aceptar claves como la configurada

   **PasswordAuthentication yes →** Obligamos a que el servidor solo acepte claves, y no contraseñas, lo cual lo hace que no se pueda intentar ataques de fuerza bruta

<a name="comprobacion"></a>
2. **Comprobacion**

   Validamos si la configuración se ha realizado correctamente o hay algo en el archivo incorrecto

   Y seguidamente hicimos un reinicio del servicio

   Para finalizar miramos el servicio

<a name="firewall"></a>
3. **Firewall**

<a name="crear-reglas"></a>
1. **Crear reglas**

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

<a name="regla-aws"></a>
4. **Regla AWS**

<a name="security-group"></a>
   1. Security Group

      Ahora desde AWS, deberemos de crear una regla de entrada indicando el protocolo TCP y que sea por el puerto 2222

      # **S1-02 - Docker + Keycloak - Isard**

<a name="instalacin-de-docker"></a>
1. **Instalación de Docker**

<a name="instalar-paquetes"></a>
1. **Instalar paquetes**

   Ejecutamos lo siguiente para instalar los paquetes

```bash
sudo apt install docker.io docker-compose -y
```

<a name="permisos"></a>
2. **Permisos**

   Para evitar usar sudo, lo que haremos es lo siguiente

```bash
sudo usermod -aG docker $USER
```

<a name="despliegue-de-keycloak"></a>
2. **Despliegue de KeyCloak**

<a name="creacin-de-directorios"></a>
1. **Creación de directorios**

   Crearemos las carpetas

```bash
mkdir -p ~/zth-node-cloud/keycloak
```



```bash
cd ~/zth-node-cloud/keycloak
```

<a name="crear-y-configurar-archivo-yml"></a>
2. **Crear y configurar archivo .yml**

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

   ###

<a name="firewall-regla"></a>
3. **Firewall Regla**

   Ahora deberemos de crear una nueva regla para poder acceder al Keycloak

```bash
sudo ufw allow 8080/tcp
```

<a name="comprobacin"></a>
4. **Comprobación**

   Ahora accederemos mediante la IP del servidor :8080 y nos pide las credenciales

   Después de poner las credenciales, accedemos correctamente

   # **S1-03 - Configuración de Seguridad IAM - Isard**

<a name="crear-realm"></a>
1. **Crear Realm**

<a name="configurar-realm"></a>
1. **Configurar Realm**

   Ejecutamos lo siguiente para instalar los paquetes

   Ahora le damos clic en Create Realm

   Ahora crearemos el Realm

   Ya hemos creado el Realm Correctamente

<a name="autenticacin-multifactor"></a>
2. **Autenticación multifactor**

<a name="configuracin-de-autenticacin"></a>
1. **Configuración de Autenticación**

   Desde nuestro Realm, nos vamos al apartado de configure y nos dirigimos donde nos dice Authentication

   Ahora nos dirigimos donde nos indican Requiere Actions

   Ahora marcamos la opción de Set as Default Action

   Esto lo que hará es que todo usuario nuevo deberá de tener una app para autenticarse

<a name="polticas-de-ataques"></a>
2. **Políticas de ataques**

   Ahora deberemos de dirigirnos a donde dice Realm Settings y después a Security Defense

   En este caso en el apartado de Brute Force

   Elegimos esta última opción porque es la más segura de todas, es la que tiene tolerancia cero

   Lo configuraremos de la siguiente manera

   **Max login failures →** Indicamos que si el “usuario” ha fallado 3 veces, el sistema ejecuta las acciones que hemos configurado a continuación

   **Maximum temporary lockouts →** Aquí le decimos que si el “usuario” ya ha fallado 1 vez y en este segundo intento vuelve a fallar, la cuenta queda bloqueada para siempre y el unico metodo de desbloqueo es que el administrador, lo haga manualmente

   **Wait increment →** Después de haber fallado la 1a vez, no pueden intentarlo al momento, deberán de esperar unos 5 minutos para volver a intentar

   **Quick login check →** Bloqueamos ataque de fuerza bruta o de diccionario, aquí le indicamos que si han intentado iniciar sesion en menos de 2 segundos son bots o ataques

   **Failure reset time →** Es el tiempo en que quedan registrados los intentos, se quedan guardados en memoria durante X tiempo, si por ejemplo fallan 1 vez por la mañana y 2 por la tarde se bloquea la cuenta

<a name="comprobacin"></a>
3. **Comprobación**

   SI intentamos acceder y fallamos 3 veces la contraseña

   En el panel de administración, vemos que se ha bloqueado temporalmente

   ###

<a name="polticas-de-contraseas"></a>
4. **Políticas de Contraseñas**

   Nos vamos al apartado de Authentication y nos vamos al apartado de Polices

   Ahora en el apartado de Add Policy y marcamos las siguientes opciones

<a name="digit-obligamos-a-poner-dgito-como-mnimo"></a>
1. **Digit** →Obligamos a poner dígito como mínimo
<a name="not-username-evitamos-que-la-contrasea-pongan-el-nombre-del-usuario"></a>
2. **Not Username** → Evitamos que la contraseña pongan el nombre del usuario
<a name="lowercase-characters-obligatorio-poner-mayscula-como-mnimo"></a>
3. **Lowercase Characters** → Obligatorio poner mayúscula como mínimo
<a name="uppercase-characters-obligatorio-poner-mayscula-como-mnimo"></a>
4. **Uppercase Characters** → Obligatorio poner mayúscula como mínimo
<a name="special-characters-obligatorio-poner-un-carcter-especial"></a>
5. **Special Characters →** Obligatorio poner un carácter especial
<a name="minimum-length-un-mnimo-de-longitud-obligatoria"></a>
6. **Minimum Length →** Un mínimo de longitud obligatoria

   Ahora lo configuramos de la siguiente manera

   Obligamos a que las contraseñas tengan como mínimo 10 caracteres

<a name="roles-y-grupos"></a>
5. **Roles y Grupos**

   Ahora crearemos los roles estos se hacen desde Realm Roles

   El primer role que crearemos será el de Administrador

   Ahora el otro Role seria de Trabajadores

   Ahora el otro Role seria de Auditoría

   Ahora crearemos también un grupo que sea Administradores

   Le asignamos un nombre

   Ahora crearemos el otro grupo como es el de los Trabajadores

   Ahora crearemos el Grupo de Auditoría

   ###

<a name="usuario"></a>
6. **Usuario**

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

<a name="comprobacion"></a>
7. **Comprobacion**

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

   ####

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

<a name="fail2ban"></a>
1. **Fail2Ban**

<a name="instalacin-del-fail2ban"></a>
1. **Instalación del Fail2Ban**

   Primero de todo realizaremos la instalación

```bash
sudo apt install fail2ban -y
```

<a name="crear-configuracin-inicial"></a>
2. **Crear configuración inicial**

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

<a name="reiniciamos-y-comprobamos"></a>
3. **Reiniciamos y comprobamos**

   Ahora vamos a reiniciar el servicio

```bash
sudo systemctl restart fail2ban
```

   Ahora lo vamos a comprobar

```bash
sudo fail2ban-client status sshd
```

   ###

<a name="comprobacion"></a>
4. **COMPROBACION**

   Si desde cliente intentó acceder y fallo las contraseñas, nos sale este mensaje

   Si accedemos al server y miramos el fail2ban podemos ver que tenemos esta IP bloqueada

   ###

   Corroboramos que la IP del cliente es la baneada

   ###

   Para desbanear haremos esto

   Como podemos ver, tenemos la IP baneada, haremos un unban y la IP que queremos desbloquear y después de hacer eso, vemos que ya no tenemos la IP baneada

<a name="actualizaciones-automticas-de-seguridad"></a>
2. **Actualizaciones Automáticas de Seguridad**

<a name="configurar-actualizaciones-automticas"></a>
   1. **Configurar actualizaciones automáticas**

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

<a name="comprobacion"></a>
2. **Comprobacion**

   Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando

```bash
systemctl status unattended-upgrades
```

<a name="configuracin-de-poltica-restrictiva"></a>
   3. **Configuración de política restrictiva**

Ahora le indicaremos al firewall que bloquee cualquier paquete siempre y cuando no le hemos indicado lo contrario

```bash
sudo ufw default deny incoming
```

bash

```bash
sudo ufw status verbose
```

[http://192.168.18.10:8080/realms/zth-node-cloud/account](http://192.168.18.10:8080/realms/zth-node-cloud/account)

<a name="s1-05-hardening-en-el-nodo-aws"></a>
# **S1-05: Hardening en el Nodo AWS**
<a name="firewall---aws"></a>
1. **Firewall - AWS**

<a name="reglas"></a>
1. **Reglas**

   En AWS creamos reglas para el ssh por el puerto 2222

   Ahora indicamos el puerto de HTTPS para la web

   Todo lo que no se encuentra aqui sera denegado con DROP

<a name="dentro-de-la-instancia"></a>
2. **Dentro de la Instancia**

   Ahora realizaremos la configuración dentro de la instancia, denegamos todo

```bash
sudo ufw default deny incoming
```

bash
sudo ufw default allow outgoing

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

<a name="fail2ban"></a>
2. **Fail2Ban**

<a name="instalacin-del-fail2ban"></a>
1. Instalación del Fail2Ban

   Haremos la actualización, y procederemos a la instalación

```bash
sudo apt update && sudo apt install fail2ban -y
```

<a name="configuracin"></a>
2. **Configuración**

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

   # ubuntu@zth-node-
```



```bash
   sudo cat /etc/fail2ban/jail.local
```

<a name="reiniciamos-el-servicio-y-miramos-el-estado"></a>
3. Reiniciamos el servicio y miramos el estado

```bash
sudo systemctl restart fail2ban
```

bash
sudo systemctl status fail2ban

<a name="comprobacion"></a>
4. **COmprobacion**

   Desde la máquina host fallamos 3 veces la contraseña a propósito

   Ahora miramos la IP que tenemos PÚBLICA

   Ahora desde el servidor de AWS podemos ver el baneo

   ##

<a name="actualizaciones-automticas-de-seguridad-en-aws"></a>
3. **Actualizaciones Automáticas de Seguridad en AWS**

<a name="configurar-actualizaciones-automticas"></a>
1. **Configurar actualizaciones automáticas**

   Como hemos realizado antes en ISARD, configuraremos para que se hagan las actualizaciones automáticas

   Instalaremos el paquete inicialmente

```bash
   sudo apt update && sudo apt install unattended-upgrades -y
```

   Ejecutamos el siguiente comando

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

<a name="comprobacion"></a>
2. **Comprobacion**

   Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando

```bash
systemctl status unattended-upgrades
```
