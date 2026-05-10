# Hardening Inicial y Despliegue de

## Indice



- [Hardening Inicial y Despliegue de](#hardening-inicial-y-despliegue-de)

- [Infraestructura Segura](#infraestructura-segura)

- [N°: GRUPO 8](#n-grupo-8)

- [Integrantes: Javier Vericat - Bryan Aguilera - Giuseppe Suarez](#integrantes-javier-vericat-bryan-aguilera-giuseppe-suarez)

- [Profesores: Sergi - David Sicart](#profesores-sergi-david-sicart)

- [S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard](#s1-01-acceso-ssh-seguro-hardening-inicial-isard)

- [1. ​ Gestion de claves](#1-gestion-de-claves)

- [a. ​ Generar claves](#a-generar-claves)

- [b. ​ Pasar claves](#b-pasar-claves)

- [c. ​ Comprobación](#c-comprobación)

- [2. ​ Handering de SSH](#2-handering-de-ssh)

- [a. ​ Cambiar puerto y archivo de configuración](#a-cambiar-puerto-y-archivo-de-configuración)

- [b. ​ Comprobacion](#b-comprobacion)

- [3. ​ Firewall](#3-firewall)

- [a. ​ Crear reglas](#a-crear-reglas)

- [4. ​ Regla AWS](#4-regla-aws)

- [S1-02 - Docker + Keycloak - Isard](#s1-02-docker-keycloak-isard)

- [1. ​ Instalación de Docker](#1-instalación-de-docker)

- [a. ​ Instalar paquetes](#a-instalar-paquetes)

- [b. ​ Permisos](#b-permisos)

- [2. ​ Despliegue de KeyCloak](#2-despliegue-de-keycloak)

- [a. ​ Creación de directorios](#a-creación-de-directorios)

- [b. ​ Crear y configurar archivo .yml](#b-crear-y-configurar-archivo-yml)

- [c. ​ Firewall Regla](#c-firewall-regla)

- [d. ​ Comprobación](#d-comprobación)

- [S1-03 - Configuración de Seguridad IAM - Isard](#s1-03-configuración-de-seguridad-iam-isard)

- [1. ​ Crear Realm](#1-crear-realm)

- [a. ​ Configurar Realm](#a-configurar-realm)

- [2.​ Autenticación multifactor](#2-autenticación-multifactor)

- [a. ​ Configuración de Autenticación](#a-configuración-de-autenticación)

- [b. ​ Políticas de ataques](#b-políticas-de-ataques)

- [d. ​ Políticas de Contraseñas](#d-políticas-de-contraseñas)

- [e. ​ Roles y Grupos](#e-roles-y-grupos)

- [f. ​ Usuario](#f-usuario)

- [g. ​ Comprobacion](#g-comprobacion)

- [S1-04: Hardening Avanzado del Sistema - Isard](#s1-04-hardening-avanzado-del-sistema-isard)

- [1. ​ Fail2Ban](#1-fail2ban)

- [a. ​ Instalación del Fail2Ban](#a-instalación-del-fail2ban)

- [b. ​ Crear configuración inicial](#b-crear-configuración-inicial)

- [c. ​ Reiniciamos y comprobamos ​](#c-reiniciamos-y-comprobamos)

- [d. ​ COMPROBACION](#d-comprobacion)

- [2. ​ Actualizaciones Automáticas de Seguridad](#2-actualizaciones-automáticas-de-seguridad)

- [a. ​ Configurar actualizaciones automáticas](#a-configurar-actualizaciones-automáticas)

- [Ejecutamos el siguiente comando](#ejecutamos-el-siguiente-comando)

- [c. ​ Configuración de política restrictiva](#c-configuración-de-política-restrictiva)

- [S1-05: Hardening en el Nodo AWS](#s1-05-hardening-en-el-nodo-aws)

- [1. ​ Firewall - AWS](#1-firewall-aws)

- [a. ​ Reglas](#a-reglas)

- [Ahora indicamos el puerto de HTTPS para la web](#ahora-indicamos-el-puerto-de-https-para-la-web)

- [Todo lo que no se encuentra aqui sera denegado con DROP](#todo-lo-que-no-se-encuentra-aqui-sera-denegado-con-drop)

- [b. ​ Dentro de la Instancia](#b-dentro-de-la-instancia)

- [2. ​ Fail2Ban](#2-fail2ban)

- [b. ​ Configuración](#b-configuración)

- [d.​COmprobacion](#dcomprobacion)

- [3. ​ Actualizaciones Automáticas de Seguridad en AWS](#3-actualizaciones-automáticas-de-seguridad-en-aws)



## Infraestructura Segura

![Imagen 1](./imagenes/imagen-001.png)

#### N°: GRUPO 8

#### Integrantes: Javier Vericat - Bryan Aguilera - Giuseppe Suarez

#### Profesores: Sergi - David Sicart



S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard............................................................4

1. Gestion de claves.................................................................................................................4

a. Generar claves...............................................................................................................4

b. Pasar claves...................................................................................................................5

c. Comprobación................................................................................................................ 5

2. Handering de SSH............................................................................................................... 6

a. Cambiar puerto y archivo de configuración....................................................................6

b. Comprobacion................................................................................................................7

3. Firewall.................................................................................................................................8

a. Crear reglas....................................................................................................................8

4. Regla AWS...........................................................................................................................9

S1-02 - Docker + Keycloak - Isard............................................................................................ 10

1. Instalación de Docker.........................................................................................................10

a. Instalar paquetes..........................................................................................................10

b. Permisos...................................................................................................................... 10

2. Despliegue de KeyCloak.................................................................................................... 11

a. Creación de directorios.................................................................................................11

b. Crear y configurar archivo .yml.................................................................................... 12

c. Firewall Regla...............................................................................................................14

d. Comprobación..............................................................................................................14

S1-03 - Configuración de Seguridad IAM - Isard.....................................................................16

1. Crear Realm.......................................................................................................................16

a. Configurar Realm......................................................................................................... 16

2. Autenticación multifactor....................................................................................................18

a. Configuración de Autenticación................................................................................... 18

b. Políticas de ataques.....................................................................................................19

c. Comprobación.............................................................................................................. 21

d. Políticas de Contraseñas............................................................................................. 22

e. Roles y Grupos.............................................................................................................24

f. Usuario..........................................................................................................................28

g. Comprobacion..............................................................................................................34

giuseppe-admin​

​

Iniciaremos sesión con el usuario de administrador.................................................. 34

operador-bryan​

​

Ahora iniciaremos sesión con permisos de trabajador...............................................38

Auditor-Javi................................................................................................................ 42

S1-04: Hardening Avanzado del Sistema - Isard.....................................................................48

1. Fail2Ban.............................................................................................................................48

a. Instalación del Fail2Ban............................................................................................... 48

b. Crear configuración inicial............................................................................................48



c. Reiniciamos y comprobamos....................................................................................... 50

d. COMPROBACION....................................................................................................... 51

2. Actualizaciones Automáticas de Seguridad.......................................................................53

a. Configurar actualizaciones automáticas...................................................................... 53

Ejecutamos el siguiente comando....................................................................................54

b. Comprobacion..............................................................................................................54

c. Configuración de política restrictiva..............................................................................54

S1-05: Hardening en el Nodo AWS...........................................................................................56

1. Firewall - AWS....................................................................................................................56

a. Reglas.......................................................................................................................... 56

b. Dentro de la Instancia.................................................................................................. 57

2. Fail2Ban.............................................................................................................................59

b. Configuración............................................................................................................... 59

d. COmprobacion............................................................................................................. 60

3. Actualizaciones Automáticas de Seguridad en AWS.........................................................62

a. Configurar actualizaciones automáticas...................................................................... 62

b. Comprobacion..............................................................................................................63



## S1-01 - Acceso SSH Seguro (Hardening Inicial) - Isard

### 1. ​ Gestion de claves

#### a. ​ Generar claves

Crearemos las claves con este comando

ssh-keygen -t ed25519 -C "giuseppe-access"

El algoritmo ed25519 es más seguro y más rápido que el predeterminado como es el RSA

![Imagen 2](./imagenes/imagen-002.png)



#### b. ​ Pasar claves

Ahora pasaremos las claves con el servidor que en este caso es del compañero

ssh-copy-id -i ~/.ssh/id_ed25519.pub isard@192.168.18.10

![Imagen 3](./imagenes/imagen-003.png)

#### c. ​ Comprobación

Podemos hacer ssh y no nos pide la contraseña

![Imagen 4](./imagenes/imagen-004.png)



### 2. ​ Handering de SSH

#### a. ​ Cambiar puerto y archivo de configuración

Accedemos al archivo de configuración

sudo nano /etc/ssh/sshd_config

Ahora hemos modificado lo siguiente

![Imagen 5](./imagenes/imagen-005.png)

Puerto →  Modificamos el puerto que viene por defecto para evitar ataques de botnets

PermitRootLogin  → Prohibimos que un usuario con root o superusuario pueda acceder

directamente

PubkeyAuthentication  → Indicamos al servidor que puedas procesar y aceptar claves

como la configurada

PasswordAuthentication  → Obligamos a que el servidor solo acepte claves, y no

contraseñas, lo cual lo hace que no se pueda intentar ataques de fuerza bruta



#### b. ​ Comprobacion

Validamos si la configuración se ha realizado correctamente o hay algo en el archivo

incorrecto

![Imagen 6](./imagenes/imagen-006.png)

Y seguidamente hicimos un reinicio del servicio

Para finalizar miramos el servicio

![Imagen 7](./imagenes/imagen-007.png)



### 3. ​ Firewall

#### a. ​ Crear reglas

Bloqueamos todo y solo dejaremos lo que necesitaremos

![Imagen 8](./imagenes/imagen-008.png)

Ahora lo que haremos es habilitar solo lo necesario

![Imagen 9](./imagenes/imagen-009.png)

![Imagen 10](./imagenes/imagen-010.png)

sudo ufw default allow outgoing  →  Indicamos que los paquetes desde el servidor hacia

fuera sean habilitados

sudo ufw allow 2222/tcp como el puerto 22  →  Son para el SSH, primero el ssh es puerto

22 pero luego lo cambiamos a puerto 2222 para seguridad

sudo ufw allow 80/tcp y 8443/tcp  →  Son para el servicio de Nginx tanto el http como

https

sudo ufw enable  →  Hacemos que ahora quede configurado siempre que se inicie el

servidor con esta configuración



### 4. ​ Regla AWS

a. ​ Security Group

Ahora desde AWS, deberemos de crear una regla de entrada indicando el protocolo TCP

y que sea por el puerto 2222 ​

![Imagen 11](./imagenes/imagen-011.png)



## S1-02 - Docker + Keycloak - Isard

### 1. ​ Instalación de Docker

#### a. ​ Instalar paquetes

Ejecutamos lo siguiente para instalar los paquetes

sudo apt install docker.io docker-compose -y

![Imagen 12](./imagenes/imagen-012.png)

#### b. ​ Permisos

Para evitar usar sudo, lo que haremos es lo siguiente

sudo usermod -aG docker $USER

![Imagen 13](./imagenes/imagen-013.png)



### 2. ​ Despliegue de KeyCloak

#### a. ​ Creación de directorios

Crearemos las carpetas

mkdir -p ~/zth-node-cloud/keycloak

cd ~/zth-node-cloud/keycloak

![Imagen 14](./imagenes/imagen-014.png)



#### b. ​ Crear y configurar archivo .yml

Creamos el archivo con el comando

sudo nano docker-compose.yml

Y el contenido de este es el siguiente

version: '3.8'

services:

zth-postgres:

image: postgres:16

container_name: zth-keycloak-db

restart: always  # <-- Esto hace que el contenedor se reinicie solo si el server falla

volumes:

- zth_db_data:/var/lib/postgresql/data  # <-- PERSISTENCIA AQUÍ

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

restart: always  # <-- Persistencia de disponibilidad

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

# Definición de los volúmenes persistentes

volumes:

zth_db_data:  # Docker gestionará este espacio en /var/lib/docker/volumes/

![Imagen 15](./imagenes/imagen-015.png)



#### c. ​ Firewall Regla

Ahora deberemos de crear una nueva regla para poder acceder al Keycloak

sudo ufw allow 8080/tcp

![Imagen 16](./imagenes/imagen-016.png)

#### d. ​ Comprobación

Ahora accederemos mediante la IP del servidor :8080 y nos pide las credenciales

![Imagen 17](./imagenes/imagen-017.png)



Después de poner las credenciales, accedemos correctamente

![Imagen 18](./imagenes/imagen-018.png)



## S1-03 - Configuración de Seguridad IAM - Isard

### 1. ​ Crear Realm

#### a. ​ Configurar Realm

Ejecutamos lo siguiente para instalar los paquetes

Ahora le damos clic en Create Realm

![Imagen 19](./imagenes/imagen-019.png)



Ahora crearemos el Realm

![Imagen 20](./imagenes/imagen-020.png)

Ya hemos creado el Realm Correctamente

![Imagen 21](./imagenes/imagen-021.png)



### 2.​ Autenticación multifactor

#### a. ​ Configuración de Autenticación

Desde nuestro Realm, nos vamos al apartado de configure y nos dirigimos donde nos

dice Authentication

![Imagen 22](./imagenes/imagen-022.png)

Ahora nos dirigimos donde nos indican Requiere Actions

![Imagen 23](./imagenes/imagen-023.png)



Ahora marcamos la opción de Set as Default Action

Esto lo que hará es que todo usuario nuevo deberá de tener una app para autenticarse

![Imagen 24](./imagenes/imagen-024.png)

#### b. ​ Políticas de ataques

Ahora deberemos de dirigirnos a donde dice Realm Settings y después a Security

Defense

![Imagen 25](./imagenes/imagen-025.png)



En este caso en el apartado de Brute Force

Elegimos esta última opción porque es la más segura de todas, es la que tiene tolerancia

cero

![Imagen 26](./imagenes/imagen-026.png)

Lo configuraremos de la siguiente manera

![Imagen 27](./imagenes/imagen-027.png)



Max login failures  →  Indicamos que si el “usuario” ha fallado 3 veces, el sistema ejecuta

las acciones que hemos configurado a continuación

Maximum temporary lockouts  →  Aquí le decimos que si el “usuario” ya ha fallado 1 vez

y en este segundo intento vuelve a fallar, la cuenta queda bloqueada para siempre y el

unico metodo de desbloqueo es que el administrador, lo haga manualmente

Wait increment  →  Después de haber fallado la 1a vez, no pueden intentarlo al

momento, deberán de esperar unos 5 minutos para volver a intentar

Quick login check  →  Bloqueamos ataque de fuerza bruta o de diccionario, aquí le

indicamos que si han intentado iniciar sesion en menos de 2 segundos son bots o

ataques

Failure reset time  →  Es el tiempo en que quedan registrados los intentos, se quedan

guardados en memoria durante X tiempo, si por ejemplo fallan 1 vez por la mañana y 2

por la tarde se bloquea la cuenta

#### c. ​ Comprobación

SI intentamos acceder y fallamos 3 veces la contraseña

En el panel de administración, vemos que se ha bloqueado temporalmente

![Imagen 28](./imagenes/imagen-028.png)



#### d. ​ Políticas de Contraseñas

Nos vamos al apartado de Authentication y nos vamos al apartado de Polices

![Imagen 29](./imagenes/imagen-029.png)

Ahora en el apartado de Add Policy y marcamos las siguientes opciones

1.​ Digit → Obligamos a poner dígito como mínimo

2.​ Not Username →  Evitamos que la contraseña pongan el nombre del usuario

3.​ Lowercase Characters →  Obligatorio poner mayúscula como mínimo

4.​ Uppercase Characters  →  Obligatorio poner mayúscula como mínimo

5.​ Special Characters  → Obligatorio poner un carácter especial

6.​ Minimum Length  → Un mínimo de longitud obligatoria



![Imagen 30](./imagenes/imagen-030.png)

Ahora lo configuramos de la siguiente manera

![Imagen 31](./imagenes/imagen-031.png)

Obligamos a que las contraseñas tengan como mínimo 10 caracteres



#### e. ​ Roles y Grupos

Ahora crearemos los roles estos se hacen desde Realm Roles

![Imagen 32](./imagenes/imagen-032.png)

El primer role que crearemos será el de Administrador

![Imagen 33](./imagenes/imagen-033.png)



Ahora el otro Role seria de Trabajadores

![Imagen 34](./imagenes/imagen-034.png)



Ahora el otro Role seria de Auditoría

![Imagen 35](./imagenes/imagen-035.png)

Ahora crearemos también un grupo que sea Administradores

![Imagen 36](./imagenes/imagen-036.png)



Le asignamos un nombre ​

![Imagen 37](./imagenes/imagen-037.png)

Ahora crearemos el otro grupo como es el de los Trabajadores

![Imagen 38](./imagenes/imagen-038.png)

Ahora crearemos el Grupo de Auditoría

![Imagen 39](./imagenes/imagen-039.png)



#### f. ​ Usuario

Antes de realizar la comprobación. deberemos de crear un usuario ​

![Imagen 40](./imagenes/imagen-040.png)

Aquí seleccionamos la opción de Configurar OTP

Esto lo que hará es decir al KeyCloak que este usuario la próxima vez que inicie sesión

deberá de usar una aplicación de OTP para poder iniciar sesión

![Imagen 41](./imagenes/imagen-041.png)



Completamos el formulario

Indicamos que el mail deberá de ser verificado

![Imagen 42](./imagenes/imagen-042.png)

Ahora le asignamos al grupo de Administradores

![Imagen 43](./imagenes/imagen-043.png)



Le asignaremos una contraseña

![Imagen 44](./imagenes/imagen-044.png)

Aquí desactivamos la contraseña temporal para que no la pida cambiar y para que salga

el código QR para de MFA

![Imagen 45](./imagenes/imagen-045.png)



Ahora asignaremos este usuario creado al Rol de Administrador

![Imagen 46](./imagenes/imagen-046.png)

Indicamos que es del rol Administrador y listo ya lo tendríamos

![Imagen 47](./imagenes/imagen-047.png)

Ahora creamos el usuario Operador, lo asignamos al grupo de Trabajadores

![Imagen 48](./imagenes/imagen-048.png)



Ahora asignamos una contraseña

![Imagen 49](./imagenes/imagen-049.png)

Ahora indicamos el rol que en este caso es Trabajador

![Imagen 50](./imagenes/imagen-050.png)



Ahora creamos el usuario de Auditor

![Imagen 51](./imagenes/imagen-051.png)

Como hemos realizado antes, asignamos una contraseña

![Imagen 52](./imagenes/imagen-052.png)



Una vez creado, le indicamos el rol que es en este caso de Auditoría

![Imagen 53](./imagenes/imagen-053.png)

#### g. ​ Comprobacion

giuseppe-admin ​

​

Iniciaremos sesión con el usuario de administrador

![Imagen 54](./imagenes/imagen-054.png)



Ahora nos aparecerá lo siguiente

![Imagen 55](./imagenes/imagen-055.png)



Vemos que nos pide instalar una aplicación de autenticación como puede ser Microsoft

Authenticator

Google Authenticator

FreeOT

En nuestro caso lo haríamos con Google Authenticator

![Imagen 56](./imagenes/imagen-056.png)



Al escanearlo, nos aparecerá que se añadió un nuevo código y que de nombre tiene

zth-node-cloud. Después de poner el código puse desde el dispositivo el cual se hizo en

este caso fue con el S24 Ultra de Giuseppe

Cuando el demos a Submit nos aparecerá lo siguiente

![Imagen 57](./imagenes/imagen-057.png)

En este apartado podemos ver que tenemos el inicio de sesión, tanto la contraseña y si

queremos cambiarla como MFA que en este caso es desde el dispositivo que hemos

indicado, podemos ver cuando fue y a que hora

![Imagen 58](./imagenes/imagen-058.png)



operador-bryan ​

​

Ahora iniciaremos sesión con permisos de trabajador

![Imagen 59](./imagenes/imagen-059.png)



Nos pedirá como antes usar una aplicación de la ya mencionadas

![Imagen 60](./imagenes/imagen-060.png)



Iniciamos ​

![Imagen 61](./imagenes/imagen-061.png)



Este usuario al ser solo Trabajador tiene sólo acceso básico

![Imagen 62](./imagenes/imagen-062.png)

Como puede ser usuarios y eventos

Puede ver los otros usuarios

![Imagen 63](./imagenes/imagen-063.png)



Y como es un Operador LVL 1 puede añadir usuarios, puede resetear contraseñas o

borrar, siempre y cuando tengan menos privilegios que el

![Imagen 64](./imagenes/imagen-064.png)

Auditor-Javi

Ahora iniciamos sesión con los permisos de auditor

![Imagen 65](./imagenes/imagen-065.png)



Nos pedirá el MFA ​

![Imagen 66](./imagenes/imagen-066.png)



Configuramos

![Imagen 67](./imagenes/imagen-067.png)



Una vez iniciado sesión ​

Con este usuario podemos ver los roles, pero no crear ni hacer otra operación

![Imagen 68](./imagenes/imagen-068.png)

Por otro lado también podemos ver los Usuarios

![Imagen 69](./imagenes/imagen-069.png)



Aunque ponga add user este usuario solo puede ver los campos que existen, porque si

intenta crear aparecerá el siguiente mensaje

![Imagen 70](./imagenes/imagen-070.png)

Podemos ver grupos y sus miembros

![Imagen 71](./imagenes/imagen-071.png)



![Imagen 72](./imagenes/imagen-072.png)

Como auditor, debe de ver los eventos

![Imagen 73](./imagenes/imagen-073.png)

También los eventos de administradores

![Imagen 74](./imagenes/imagen-074.png)



## S1-04: Hardening Avanzado del Sistema - Isard

### 1. ​ Fail2Ban

#### a. ​ Instalación del Fail2Ban

Primero de todo realizaremos la instalación

sudo apt install fail2ban -y

![Imagen 75](./imagenes/imagen-075.png)

#### b. ​ Crear configuración inicial

Creamos un .local para que si hay alguna actualización, que no se nos escriba el archivo

.conf

sudo nano /etc/fail2ban/jail.local



[sshd]

enabled = true

port = 2222

filter = sshd

logpath = /var/log/auth.log

maxretry = 2

findtime = 600

bantime = -1

banaction = ufw

[sshd]  →  Indicamos que esta configuración se aplica al ssh

enabled = true  →  Indicamos que se ejecute

port = 2222  →  Indicamos en qué puerto debe de ejecutarse

filter = sshd  →  Es un filtro para que busque errores como invalid password etc

logpath = /var/log/auth.log  →  Es donde se revisaran los logs

maxretry = 2  →  Número máximo de intentos

findtime = 600  →  Si fallo 2 veces, en menos de 10 min, se ejecuta el baneo

bantime = -1  →  Indicamos que el baneo sea permanente

banaction = ufw  →  Indicamos que lo haga mediante ufw en vez de iptables, cuya ventaja

es que lo bloquea directamente el firewall

![Imagen 76](./imagenes/imagen-076.png)



#### c. ​ Reiniciamos y comprobamos ​

Ahora vamos a reiniciar el servicio

sudo systemctl restart fail2ban ​

![Imagen 77](./imagenes/imagen-077.png)

Ahora lo vamos a comprobar

sudo fail2ban-client status sshd

![Imagen 78](./imagenes/imagen-078.png)

​



#### d. ​ COMPROBACION

Si desde cliente intentó acceder y fallo las contraseñas, nos sale este mensaje

![Imagen 79](./imagenes/imagen-079.png)

Si accedemos al server y miramos el fail2ban podemos ver que tenemos esta IP

bloqueada

![Imagen 80](./imagenes/imagen-080.png)

#### ​



Corroboramos que la IP del cliente es la baneada

![Imagen 81](./imagenes/imagen-081.png)

Para desbanear haremos esto ​

![Imagen 82](./imagenes/imagen-082.png)

Como podemos ver, tenemos la IP baneada, haremos un unban y la IP que queremos

desbloquear y después de hacer eso, vemos que ya no tenemos la IP baneada



### 2. ​ Actualizaciones Automáticas de Seguridad

#### a. ​ Configurar actualizaciones automáticas

Para minimizar las vulnerabilidades, deberemos de indicar que los “parches” de

seguridad se hagan de manera automática ​

Para ello usaremos el siguiente comando

sudo apt install unattended-upgrades -y sudo dpkg-reconfigure --priority=low

unattended-upgrades

sudo apt install unattended-upgrades -y  →  Es un paquete el cual permite descargar

actualizaciones y aplicarse automáticamente sin necesidad de interacción con el usuario

sudo dpkg-reconfigure  → Aquí le indicamos que queremos reconfigurar el programa X

--priority=low  → Indicamos que nos muestre todas las opciones posibles

unattended-upgrades  → Su función es la de revisar repositorios y si hay un nuevo

“parche” lo descarga y se aplica, busca vulnerabilidades



#### Ejecutamos el siguiente comando

sudo dpkg-reconfigure --priority=low unattended-upgrades

![Imagen 83](./imagenes/imagen-083.png)

#### b. ​ Comprobacion

Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando ​

systemctl status unattended-upgrades

![Imagen 84](./imagenes/imagen-084.png)

#### c. ​ Configuración de política restrictiva

​

Ahora le indicaremos al firewall que bloquee cualquier paquete siempre y cuando no le

hemos indicado lo contrario

sudo ufw default deny incoming

sudo ufw status verbose



![Imagen 85](./imagenes/imagen-085.png)

http://192.168.18.10:8080/realms/zth-node-cloud/account



## S1-05: Hardening en el Nodo AWS

### 1. ​ Firewall - AWS

#### a. ​ Reglas

En AWS creamos reglas para el ssh por el puerto 2222

![Imagen 86](./imagenes/imagen-086.png)



#### Ahora indicamos el puerto de HTTPS para la web

![Imagen 87](./imagenes/imagen-087.png)

#### Todo lo que no se encuentra aqui sera denegado con DROP

#### b. ​ Dentro de la Instancia

Ahora realizaremos la configuración dentro de la instancia, denegamos todo

sudo ufw default deny incoming

sudo ufw default allow outgoing

![Imagen 88](./imagenes/imagen-088.png)



Creamos las reglas de SSH con puerto 222, tenemos también HTTPS con 443 y tenemos

también el WireGuard

Para finalizar activamos el Firewall

sudo ufw allow 2222/tcp

sudo ufw allow 8080/tcp

sudo ufw allow 443/tcp

sudo ufw allow 51820/udp

sudo ufw enable

![Imagen 89](./imagenes/imagen-089.png)

Revisión de las reglas

sudo ufw status verbose

![Imagen 90](./imagenes/imagen-090.png)



### 2. ​ Fail2Ban

a. ​ Instalación del Fail2Ban

Haremos la actualización, y procederemos a la instalación

sudo apt update && sudo apt install fail2ban -y

![Imagen 91](./imagenes/imagen-091.png)

#### b. ​ Configuración

Ahora el archivo de configuración, tendríamos lo siguiente

sudo nano /etc/fail2ban/jail.local

[sshd]

enabled = true

port = 2222

filter = sshd

logpath = /var/log/auth.log

maxretry = 3

findtime = 600

bantime = -1

banaction = ufw

ubuntu@zth-node-

sudo cat /etc/fail2ban/jail.local



![Imagen 92](./imagenes/imagen-092.png)

c. ​ Reiniciamos el servicio y miramos el estado

sudo systemctl restart fail2ban

sudo systemctl status fail2ban

![Imagen 93](./imagenes/imagen-093.png)

#### d.​COmprobacion

Desde la máquina host fallamos 3 veces la contraseña a propósito

![Imagen 94](./imagenes/imagen-094.png)



Ahora miramos la IP que tenemos PÚBLICA

![Imagen 95](./imagenes/imagen-095.png)

Ahora desde el servidor de AWS podemos ver el baneo

![Imagen 96](./imagenes/imagen-096.png)



### 3. ​ Actualizaciones Automáticas de Seguridad en AWS

#### a. ​ Configurar actualizaciones automáticas

Como hemos realizado antes en ISARD, configuraremos para que se hagan las

actualizaciones automáticas

Instalaremos el paquete inicialmente

sudo apt update && sudo apt install unattended-upgrades -y

![Imagen 97](./imagenes/imagen-097.png)

Ejecutamos el siguiente comando

sudo dpkg-reconfigure --priority=low unattended-upgrades

![Imagen 98](./imagenes/imagen-098.png)



#### b. ​ Comprobacion

Para realizar si se está ejecutando correctamente, ejecutaremos el siguiente comando ​

systemctl status unattended-upgrades

![Imagen 99](./imagenes/imagen-099.png)

---

## Recomendaciones Adicionales

Para fortalecer aun mas la infraestructura, se sugieren las siguientes implementaciones:

1. **Auditoria de Logs:** Monitorizar activamente /var/log/fail2ban.log para identificar patrones de ataque recurrentes.
2. **Hardening de Kernel (sysctl):** Configurar /etc/sysctl.conf para deshabilitar IP forwarding (si no es necesario) y proteger contra ataques de ICMP redirects.
3. **Limpieza de Servicios:** Deshabilitar cualquier servicio innecesario que este en escucha (ss -tunlp).
4. **Permisos de Archivos:** Verificar que los archivos sensibles de configuracion (como /etc/ssh/sshd_config) tengan permisos restrictivos (600).

---
**Resultado Final:** La infraestructura cuenta ahora con multiples capas de seguridad, desde el acceso a nivel de red (Firewall) hasta la gestion de identidades (IAM/MFA) y la respuesta automatica ante ataques (Fail2Ban).
