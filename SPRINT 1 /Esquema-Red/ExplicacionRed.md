# Explicación del esquema de red
## El lado de AWS

La máquina de AWS tiene una IP pública, la **52.87.11.36**. Eso significa que en 
principio es accesible desde cualquier punto de internet, aunque obviamente tendrá 
reglas de firewall que limitan quién puede entrar y por dónde. No todo el mundo 
puede conectarse a cualquier puerto de esa máquina, sino que hay reglas que 
controlan exactamente qué tráfico se permite y cuál se descarta.

Dentro de esa máquina están corriendo contenedores Docker. Si no sabes lo que es 
Docker, imagínate que es como tener varias aplicaciones empaquetadas cada una en 
su propia cajita aislada, dentro de la misma máquina. Cada cajita tiene todo lo 
que necesita para funcionar — sus librerías, su configuración, su entorno — y no 
interfiere con las demás. Es una forma muy ordenada y eficiente de gestionar 
servicios, y además hace que sea mucho más fácil arrancarlos, pararlos, 
actualizarlos o replicarlos sin tocar el resto del sistema.

En este caso hay dos contenedores corriendo en AWS:

### PostgreSQL

PostgreSQL es un sistema de gestión de bases de datos relacional. Aquí es donde 
se almacena toda la información que maneja la aplicación — usuarios, registros, 
configuraciones, transacciones, lo que sea que necesite guardar este sistema en 
concreto. Piénsalo como el cajón donde vive todo. Sin base de datos, la aplicación 
no tiene memoria, no recuerda nada, no puede funcionar.

La base de datos es probablemente el componente más crítico de toda la 
infraestructura, porque si se pierde o se corrompe, se pierde todo. No es algo 
que se pueda recuperar fácilmente si no tienes backups, y por eso es importante 
tenerla en un entorno controlado, con copias de seguridad, con monitorización, 
y con garantías de disponibilidad.

PostgreSQL está dentro de la red interna de Docker, lo que significa que no está 
expuesta directamente a internet. Solo pueden hablar con ella los servicios que 
están dentro de esa misma red Docker o los que llegan a través del túnel VPN. 
Eso es una capa de seguridad importante — la base de datos nunca debería ser 
accesible desde fuera sin pasar por algún tipo de control.

### Keycloak (en AWS)

Keycloak es una solución de gestión de identidad y acceso. Dicho en cristiano, 
es el sistema que se encarga de los logins y de todo lo relacionado con la 
autenticación y autorización de usuarios. Cuando alguien entra a la aplicación 
y introduce su usuario y contraseña, es Keycloak quien verifica que esas 
credenciales son correctas y quien decide qué permisos tiene esa persona dentro 
del sistema.

Pero Keycloak hace mucho más que eso. También puede gestionar inicio de sesión 
único — lo que se conoce como SSO — que permite que un usuario que ya está 
autenticado en un servicio no tenga que volver a introducir sus credenciales en 
otro. Puede integrarse con proveedores externos como Google, Microsoft, o GitHub. 
Gestiona tokens de sesión, tiempos de expiración, roles, grupos de usuarios, 
políticas de contraseñas... Es una pieza muy completa y muy importante, porque 
todo lo que tenga que ver con quién puede acceder a qué pasa obligatoriamente 
por ahí.

Este Keycloak se comunica con PostgreSQL a través de la red interna de Docker, 
representada en el esquema con líneas azules discontinuas. PostgreSQL es donde 
Keycloak guarda todos sus datos — usuarios, sesiones, configuraciones de realm, 
y demás. Sin esa base de datos, Keycloak no tendría dónde persistir nada.

---

## El lado del servidor propio (On-Premise)

A la derecha del esquema tienes el servidor físico o virtual on-premise, con IP 
local **192.168.18.10**. La palabra "on-premise" significa simplemente que ese 
servidor está en un sitio físico concreto que controlas tú — puede ser una sala 
de servidores en una oficina, un rack en un datacenter, o una máquina virtual 
corriendo en un hipervisor propio. No está en la nube, está en algún lugar físico.

Este servidor también usa Docker, y tiene más contenedores corriendo que el de 
AWS, porque aquí es donde vive la mayor parte de los servicios visibles y 
operativos de la aplicación.

### Keycloak (en el servidor)

Sí, hay una instancia de Keycloak también aquí, en el servidor on-premise. 
Esto puede parecer redundante a primera vista, pero en arquitecturas más complejas 
tiene su lógica. Puede estar configurado para federarse con el Keycloak de AWS, 
de forma que ambos compartan información de usuarios y sesiones. También puede 
estar sirviendo como punto de autenticación para los servicios que corren 
localmente en este servidor, evitando que todas las peticiones de autenticación 
tengan que cruzar el túnel VPN hasta AWS cada vez. Los detalles exactos dependen 
de cómo esté configurado internamente, pero tener varias instancias de Keycloak 
en infraestructuras distribuidas es una práctica habitual cuando se quiere 
redundancia o separación de responsabilidades.

### Grafana

Grafana es una herramienta de visualización de datos orientada a métricas y 
monitorización de sistemas. Básicamente es el panel de control desde el que el 
equipo técnico puede ver en tiempo real qué está pasando en toda la 
infraestructura. Cuánta CPU está consumiendo el servidor, cuánta memoria está 
usando cada contenedor, cuántas peticiones por segundo está recibiendo la 
aplicación, cuánto tarda en responder, si hay picos de carga, si hay errores 
disparándose... todo eso aparece en Grafana en forma de gráficas, tablas y 
dashboards configurables.

Es la herramienta que diferencia saber que algo va mal cuando ya es tarde de 
detectarlo antes de que se convierta en un problema real. Está accesible en el 
**puerto 3000** de la IP del servidor.

### Prometheus

Prometheus es el motor que alimenta a Grafana. Si Grafana es el panel de control 
bonito donde se ven las gráficas, Prometheus es el sistema que va recopilando 
todos esos datos constantemente. Funciona mediante un modelo de scraping — cada 
cierto tiempo va preguntando a cada servicio configurado "¿qué métricas tienes 
ahora mismo?" y guarda esas respuestas en su base de datos interna de series 
temporales. 

Luego Grafana se conecta a Prometheus como fuente de datos y convierte todos 
esos números en las visualizaciones que el equipo puede consultar. Son dos 
herramientas que van casi siempre juntas en este tipo de infraestructuras y que 
forman un stack de monitorización muy sólido y ampliamente adoptado. Prometheus 
está escuchando en el **puerto 9090**.

### Nginx

Nginx es el servidor web principal, el que está en la primera línea de cara al 
usuario final. Cuando alguien abre el navegador y entra a la dirección web de 
esta aplicación, lo primero con lo que habla es con Nginx. 

Nginx tiene varios roles posibles en una arquitectura como esta. El primero es 
servir directamente los archivos estáticos de la aplicación — HTML, CSS, 
JavaScript, imágenes. El segundo, y probablemente más importante aquí, es actuar 
como proxy inverso: recibe las peticiones entrantes de los usuarios y las 
redirige internamente hacia el servicio que corresponda en cada caso. Si alguien 
intenta hacer login, Nginx puede redirigir esa petición a Keycloak. Si pide datos 
de la aplicación, los redirige a donde esté corriendo la lógica de negocio.

Además, en el esquema se ve que Nginx sirve por **HTTPS**, lo que significa que 
toda la comunicación entre el navegador del usuario y el servidor va cifrada con 
TLS. Esto es completamente imprescindible hoy en día para cualquier aplicación 
que maneje datos de usuarios. Sin HTTPS, cualquiera en la misma red podría leer 
el tráfico en texto plano.

---

## La VPN WireGuard

Aquí está el núcleo de todo este esquema. Tienes dos entornos completamente 
separados — uno en la nube de Amazon, en algún datacenter de AWS en algún punto 
del mundo, y otro en un servidor físico propio en algún lugar concreto. Entre 
ellos solo hay internet pública, que es un entorno hostil, no cifrado por defecto, 
y donde cualquier cosa puede ser interceptada o manipulada si no se toman medidas.

Para resolver eso está WireGuard. WireGuard es una implementación de VPN moderna 
que ha ganado mucha popularidad en los últimos años por varios motivos: es 
extremadamente rápida comparada con alternativas más antiguas como OpenVPN o 
IPSec, su base de código es mucho más pequeña y fácil de auditar desde el punto 
de vista de seguridad, y su configuración es bastante más sencilla e intuitiva.

Lo que hace WireGuard es crear un túnel cifrado punto a punto entre los dos 
extremos de la conexión. Todo el tráfico que viaja entre AWS y el servidor 
on-premise pasa por ese túnel completamente cifrado, como si estuviera dentro 
de un tubo opaco que nadie de fuera puede ver, interceptar ni manipular. 
Desde fuera solo se ve que hay tráfico UDP entre dos IPs, pero el contenido 
es completamente ilegible sin las claves criptográficas correspondientes.

Dentro de ese túnel, cada extremo tiene asignada una IP propia dentro de una 
red virtual privada:

- El servidor on-premise tiene la IP **10.8.0.1**
- La instancia de AWS tiene la IP **10.8.0.2**

Ambas pertenecen a la subred **10.8.0.0/24**, que es una red completamente 
virtual que solo existe dentro del túnel. No es accesible desde internet, no 
existe fuera de esa conexión VPN. Pero desde dentro, los dos extremos se ven 
el uno al otro como si estuvieran conectados directamente en la misma red 
local, aunque en realidad estén separados por miles de kilómetros.

Esto tiene consecuencias muy importantes para cómo se comunican los servicios. 
Keycloak en AWS puede hablar con servicios del servidor on-premise usando la IP 
10.8.0.1. Los servicios del servidor pueden acceder a PostgreSQL en AWS usando 
la IP 10.8.0.2. Todo ello sin que ninguno de esos servicios esté expuesto 
directamente a internet. Nadie que no forme parte de esa VPN puede llegar a 
ellos, porque simplemente no tienen una IP pública por la que entrar.
