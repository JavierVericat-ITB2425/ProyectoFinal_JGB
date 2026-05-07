
# Monitorización Avanzada y Gestión de Alertas de Seguridad

## Informacion general

![Portada](./imagenes/Portada.png)

- Grupo: 8
- Integrantes: Javier Vericat, Bryan Aguilera y Giuseppe Suarez
- Profesores: Sergi y David Sicart
- Objetivo: implementar monitorizacion avanzada y gestion de alertas de seguridad con Grafana, Prometheus y Loki.

## Indice

- [Informacion general](#informacion-general)
- [1. Preparativos](#1-preparativos)
- [1.1 Origen de datos y configuracion de logs (auth.log / syslog)](#11-origen-de-datos-y-configuracion-de-logs-authlog--syslog)
- [2. Dashboard de Hardware](#2-dashboard-de-hardware)
- [3. Dashboard de Auditoria de Accesos](#3-dashboard-de-auditoria-de-accesos)
- [4. Dashboard SSH](#4-dashboard-ssh)
- [5. Monitorizacion de trafico VPN (Wireguard)](#5-monitorizacion-de-trafico-vpn-wireguard)
- [6. Panel de control global](#6-panel-de-control-global)
- [7. Configuracion de alertas criticas y disponibilidad](#7-configuracion-de-alertas-criticas-y-disponibilidad)
- [7.1 Alerta de fuerza bruta SSH (Seguridad)](#71-alerta-de-fuerza-bruta-ssh-seguridad)
- [7.2 Alerta de CPU critica (Disponibilidad)](#72-alerta-de-cpu-critica-disponibilidad)
- [7.3 Caida de servicios](#73-caida-de-servicios)
- [7.4 Login sin MFA](#74-login-sin-mfa)
- [7.5 Conexion VPN desconocida](#75-conexion-vpn-desconocida)
- [8. Auditoria de seguridad y validacion MFA](#8-auditoria-de-seguridad-y-validacion-mfa)
- [Resultado](#resultado)

## 1. Preparativos

### 1.1 Origen de datos y configuracion de logs (auth.log / syslog)

Antes de realizar cualquier accion, debemos asegurar la correcta recepcion de eventos verificando que el agente de logs esta capturando los ficheros de sistema en tiempo real:

```bash
sudo tail -f /var/log/auth.log
sudo tail -f /var/log/syslog
```

Como podemos ver en la captura siguiente, los logs se leen correctamente:

![Logs leidos correctamente](./imagenes/imagen-002.png)

## 2. Dashboard de Hardware

Para crear los dashboards, tendremos que dirigirnos al apartado de Dashboard y crear uno nuevo:

![Crear dashboard nuevo](./imagenes/imagen-003.png)

Ahora le damos a **Import Dashboard**:

![Importar dashboard](./imagenes/imagen-004.png)

Se ha importado el **Dashboard ID 1860 (Node Exporter Full)**, ya que este ofrece metricas detalladas de salud del nodo (CPU, Memoria, disco y Red) extraidas mediante Prometheus:

![Importar Node Exporter Full](./imagenes/imagen-005.png)

Nos aparecera el Dashboard de Node Exporter Full:

![Dashboard Node Exporter Full](./imagenes/imagen-006.png)

Al darle a Import nos aparecera todo vacio al principio; esto es normal, porque aun no le hemos indicado de donde debe extraerse la informacion:

![Dashboard vacio](./imagenes/imagen-007.png)

Le tendremos que dar a **DataSource**:

![Seleccionar DataSource](./imagenes/imagen-008.png)

Debemos de seleccionar Prometheus:

![Seleccionar Prometheus](./imagenes/imagen-009.png)

Ahora si que sale correctamente los datos:

![Datos correctos](./imagenes/imagen-010.png)

## 3. Dashboard de Auditoria de Accesos

Para crear otro Dashboard tendremos que darle a **New** y despues a **DashBoard**:

![Nuevo dashboard](./imagenes/imagen-011.png)

Ahora le damos a **Add Visualization**:

![Add Visualization](./imagenes/imagen-012.png)

Seleccionamos en este caso Loki:

![Seleccionar Loki](./imagenes/imagen-013.png)

Elegimos Loki porque es bastante completo para verificar los logs y ver quien ha accedido, de donde y a que hora. Una vez elegimos Loki, nos apareceran los logs:

![Logs en Loki](./imagenes/imagen-014.png)

## 4. Dashboard SSH

Ahora tenemos que crear un dashboard para poder monitorizar ataques de fuerza bruta por ejemplo al servicio del SSH. Como mencionamos antes, le daremos a **New DashBoard**, y le damos de nuevo a **Add Visualization**, le damos a Loki nuevamente:

![Dashboard SSH](./imagenes/imagen-015.png)

Ahora en el apartado de Code escribiremos lo siguiente para filtrar por SSH:

```
{job="auth"} |= "sshd"
```

Le damos al **Run Query**:

![Filtrar logs SSH](./imagenes/imagen-016.png)

Anadimos un titulo y podemos ver los logs en el apartado de line:

![Logs SSH](./imagenes/imagen-017.png)

Y vemos que nos aparece los logs:

![Logs SSH completos](./imagenes/imagen-018.png)

### Alertas de ataque

Crearemos una alerta para ataques de fuerza bruta SSH:

![Crear alerta SSH](./imagenes/imagen-019.png)

Ahora crearemos la alerta, seleccionamos Loki y en el apartado de code pondremos esto:

```
count_over_time({job=~"auth|system_logs"} |~ "Failed password" [5m])
```

![Query de alerta SSH](./imagenes/imagen-020.png)

En expression quedaria asi:

![Expression alerta SSH](./imagenes/imagen-021.png)

El motor de alertas evalua las expresiones que tiene este lock, en intervalos que nosotros le indicamos. Si el conteo supera el umbral (Threshold) establecido, la alerta cambia a estado **Firing** y dispara la notificacion.

Ahora hemos creado una carpeta llamada **Alertas de Seguridad** y despues en **evaluation group** pusimos esto:

![Carpeta de alertas](./imagenes/imagen-022.png)

### Verificacion

Desde AWS ejecutamos este comando para generar log:

![Generar log desde AWS](./imagenes/imagen-023.png)

Ahora desde Grafana sale como **Pending** en amarillo:

![Alerta Pending](./imagenes/imagen-024.png)

Finalmente sale asi, en rojo:

![Alerta Firing](./imagenes/imagen-025.png)

## 5. Monitorizacion de trafico VPN (Wireguard)

Como hemos realizado hasta ahora, tendremos que dar a **New - New Dashboard**. Ahora seleccionamos Prometheus:

![Dashboard VPN](./imagenes/imagen-026.png)

Seleccionamos la opcion de Code, y pondremos el siguiente comando:

```
irate(node_network_receive_bytes_total{device="wg0"}[5m])
```

Ahora le damos a **Run Queries**:

![Query VPN](./imagenes/imagen-027.png)

Ahora le daremos a **Add Query**:

![Add Query VPN](./imagenes/imagen-028.png)

Y pondremos lo siguiente para ver trafico de subida y bajada:

```
sum(irate(node_network_receive_bytes_total[5m]))
sum(irate(node_network_transmit_bytes_total[5m]))
```

![Queries VPN](./imagenes/imagen-029.png)

Ahora podremos ver los logs, de baja como de subida respectivamente:

![Trafico VPN](./imagenes/imagen-030.png)

## 6. Panel de control global

Crearemos un nuevo Dashboard para observar todo a nivel general, para saber si el Servidor esta vivo o ha caido.

Como antes, crearemos un nuevo DashBoard con el **Add Visualization** y elegiremos Prometheus. En el apartado de Code pondremos lo siguiente para ver la carga de CPU:

```
100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

De nombre le pusimos **Carga Actual de CPU**:

![Carga de CPU](./imagenes/imagen-031.png)

Ahora anadiremos otro con Prometheus mismo pero este sera de Red. Pondremos de titulo **Actividad en Red** y en la opcion de Code le pondremos lo siguiente:

```
sum(rate(node_network_receive_bytes_total[5m]))
```

![Actividad en Red](./imagenes/imagen-032.png)

Para finalizar nuestro Dashboard General tendriamos lo siguiente:

El contador de intentos de SSH que han intentado acceder y han fallado. Este sera el unico que tendra Loki en nuestro panel Overview. De titulo pusimos **Alertas de intrusion por el servicio en este caso del SSH**, y pusimos en el code la siguiente:

```
count_over_time({job="auth"} |= "sshd" |= "Failed" [1h])
```

![Alertas de intrusion](./imagenes/imagen-033.png)

Una vez guardado este Dashboard quedaria asi:

![Dashboard General](./imagenes/imagen-034.png)

Aunque este ultimo panel se parezca al del Punto 2, no son lo mismo: el primer panel creado es solo de Hardware (miramos todas las metricas que Prometheus nos facilita), pero aqui juntamos tanto Loki como Prometheus; aqui podemos observar de manera mas general el estado del servidor.

Si vemos que la actividad de la red sube y tenemos el contador subiendo tambien a la vez podemos deducir que es un ataque, mientras que si simplemente sube la carga de la CPU podria ser algo momentaneo.

Y aqui tendremos los Dashboard creados:

![Listado de dashboards](./imagenes/imagen-035.png)

## 7. Configuracion de alertas criticas y disponibilidad

### 7.1 Alerta de fuerza bruta SSH (Seguridad)

Ahora crearemos alertas; para ello nos dirigimos al apartado de alertas → **Alert Rules** → **New Alert Rule**:

![Nueva alerta](./imagenes/imagen-036.png)

Ahora rellenaremos la informacion con lo siguiente:

![Rellenar alerta SSH](./imagenes/imagen-037.png)

Para el motor de datos usaremos Loki. Luego le daremos click en code y le ponemos lo siguiente:

```
count_over_time({job=~"auth|system_logs"} |~ "Failed password" [5m])
```

Esto lo que hace es indicar cuantas veces aparece la frase de "Failed Password" en un ratio de 5 minutos.

Luego tenemos el apartado llamado **Expression**:

![Expression alerta SSH](./imagenes/imagen-038.png)

En la expresion B Reduce, lo dejamos por defecto; actualmente esto lo que nos indica es que recogera la informacion de los ultimos ataques detectados.

Por otro lado en **C Threshold** cambiamos el apartado de **IS ABOVE** al numero 3. Esto indica que si hay 3 intentos fallidos en los ultimos 5 min que la alarma salte.

Ahora creamos la alerta:

![Guardar alerta SSH](./imagenes/imagen-039.png)

#### Comprobacion

La alerta estara asi si todo esta tranquilo sin ataques:

![Alerta normal](./imagenes/imagen-040.png)

Pero si hacemos este comando que simula realizar un ataque:

![Simular ataque](./imagenes/imagen-041.png)

El estado de la alerta se pondra en **Pending**, primero esperara 1 min para saber si realmente es un ataque o no:

![Alerta Pending](./imagenes/imagen-042.png)

Si se detecta que es un ataque saldra lo siguiente en rojo:

![Alerta Firing](./imagenes/imagen-043.png)

Nos llegara una alerta al Gmail conforme nos estan haciendo un ataque:

![Alerta por Gmail](./imagenes/imagen-044.png)

### 7.2 Alerta de CPU critica (Disponibilidad)

Crearemos una nueva alerta:

![Nueva alerta CPU](./imagenes/imagen-045.png)

Lo rellenamos con los siguientes datos:

![Rellenar alerta CPU](./imagenes/imagen-046.png)

De nombre **Disponibilidad - CPU Critica (&gt;85%)**. En este caso el motor de datos sera Prometheus, y ponemos la siguiente query:

```
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

Este lo que nos indica es el uso de la CPU en tiempo real, restando el uso cuando no se esta haciendo en reposo.

En el apartado de Expresion lo eliminaremos dando click en el siguiente icono de Papelera:

![Eliminar expression B](./imagenes/imagen-047.png)

Por otro lado en la expresion C tendremos que poner **IS ABOVE 85**. Guardamos la regla:

![Guardar alerta CPU](./imagenes/imagen-048.png)

#### Comprobacion

Si ejecutamos el siguiente comando para realizar una prueba de estres:

![Prueba de estres](./imagenes/imagen-049.png)

Podremos observar que como anteriormente, pasa al estado de Pending, alerta amarilla:

![Alerta CPU Pending](./imagenes/imagen-050.png)

Despues pasara a la alerta de color rojo, lo cual saltara:

![Alerta CPU Firing](./imagenes/imagen-051.png)

Y por ende nos llegara el correo con el aviso de dicha alerta:

![Alerta CPU por Gmail](./imagenes/imagen-052.png)

### 7.3 Caida de servicios

Crearemos la nueva alerta, llamado **caida de nodo/servicio**:

![Nueva alerta servicio](./imagenes/imagen-053.png)

En este caso ponemos la siguiente query:

```
up{instance="node-exporter-nodeA:9100"}
```

Esto nos indica que si la respuesta del nodo es 1 esta vivo pero si es 0 esta caido o no disponible.

Ahora ponemos en **Expression C** el input A y ponemos **IS BELOW** y de valor 1; conseguimos que si este valor pasa de 1 a 0 la alerta saltara:

![Expression alerta servicio](./imagenes/imagen-054.png)

Ahora guardaremos la alerta:

![Guardar alerta servicio](./imagenes/imagen-055.png)

#### Comprobacion

Simularemos una caida de servicio parando el contenedor un momento:

![Parar contenedor](./imagenes/imagen-056.png)

El estado Pending actua como un filtro de seguridad para evitar "falsos positivos". La alerta solo pasa a estado Firing (rojo) si la condicion critica persiste durante el tiempo de evaluacion configurado:

![Alerta servicio Pending](./imagenes/imagen-057.png)

Si despues de 1 min ve que sigue sin responder si no esta "vivo", salta la alerta roja:

![Alerta servicio Firing](./imagenes/imagen-058.png)

Como antes, la alerta tambien nos llega al Gmail:

![Alerta servicio por Gmail](./imagenes/imagen-059.png)

### 7.4 Login sin MFA

Crearemos la alerta para los login sin el MFA:

![Nueva alerta sin MFA](./imagenes/imagen-060.png)

De nombre pusimos **Seguridad - Login sin MFA detectado** y de la query lo siguiente:

```
count_over_time({job="auth"} |~ "Accepted password" !~ "google_authenticator" [5m])
```

Mirara los logs de autenticacion, revisara el apartado de contrasena aceptada pero mirara los que no hayan realizado o no tengan el google_authenticator.

Ahora eliminamos como antes el Expression B y nos quedamos con el C de la siguiente manera:

![Expression alerta sin MFA](./imagenes/imagen-061.png)

Esto lo que nos dice es que si hay solo 1 login sin MFA que nos avise la alerta. Guardamos la alerta y esta todo Ok:

![Guardar alerta sin MFA](./imagenes/imagen-062.png)

#### Comprobacion

Para realizar la comprobacion realizaremos el siguiente comando:

![Simular login sin MFA](./imagenes/imagen-063.png)

Y nos saltara la alerta:

![Alerta sin MFA Firing](./imagenes/imagen-064.png)

Nos llega la alerta:

![Alerta sin MFA por Gmail](./imagenes/imagen-065.png)

### 7.5 Conexion VPN desconocida

Crearemos la alerta de conexiones con una VPN que no sea la nuestra:

![Nueva alerta VPN desconocida](./imagenes/imagen-066.png)

El nombre de esta seria **Seguridad - Conexion VPN/Origen Desconocido** y de query ponemos lo siguiente:

```
count_over_time({job="auth"} |~ "Accepted" !~ "10.0.0.5" [5m])
```

Esta IP es una inventada, para que cuando se realice la comprobacion, salte la alerta.

En el apartado de Expression, quitamos la B y nos quedamos con la C con INPUT en A y luego tenemos el apartado de **IS ABOVE** en 0:

![Expression alerta VPN](./imagenes/imagen-067.png)

Con esto lo que conseguimos es que nos alertara por cualquier acceso detectado por una IP no autorizada o fuera del tunel creado de la VPN.

Ahora guardamos la alerta, y veremos que esta normal y creada correctamente:

![Guardar alerta VPN](./imagenes/imagen-068.png)

#### Comprobacion

Ejecutamos el siguiente comando para simular un logueo mediante una VPN desconocida:

![Simular VPN desconocida](./imagenes/imagen-069.png)

La alerta salta en rojo:

![Alerta VPN Firing](./imagenes/imagen-070.png)

Seguidamente nos salta tambien el aviso en Gmail:

![Alerta VPN por Gmail](./imagenes/imagen-071.png)

## 8. Auditoria de seguridad y validacion MFA

### Objetivo

El objetivo de este punto es realizar unas pruebas del comportamiento del sistema de Login.

#### Acceso con credenciales correctas (Sin MFA)

En este caso lo haremos con las credenciales del Grafana:

![Login Grafana](./imagenes/imagen-072.png)

Si le damos al Login, podemos ver que accedemos correctamente:

![Acceso correcto Grafana](./imagenes/imagen-073.png)

#### Acceso con codigo OTP erroneo

Ahora probaremos a acceder al Grafana con sistema de Keycloak pero con el codigo OTP erroneo para comprobar que no se puede acceder:

![Login Keycloak](./imagenes/imagen-074.png)

Ponemos las credenciales:

![Credenciales Keycloak](./imagenes/imagen-075.png)

Ahora nos pide el codigo OTP:

![Pedir OTP](./imagenes/imagen-076.png)

Ahora pondremos un codigo erroneo:

![OTP erroneo](./imagenes/imagen-077.png)

Como podemos observar, nos da error y no podemos acceder:

![Error acceso](./imagenes/imagen-078.png)

Si nos vamos al Keycloak podemos ver que por fallar el codigo OTP nos aparecera que la cuenta ha sido bloqueada:

![Cuenta bloqueada](./imagenes/imagen-079.png)

#### Acceso exitoso con OTP

Ahora accederemos con un codigo de OTP correcto:

![Login Keycloak correcto](./imagenes/imagen-079.png)

Ahora nos pedira un codigo de OTP:

![Pedir OTP correcto](./imagenes/imagen-080.png)

Como podemos ver hemos accedido correctamente. Otra cosa a tener en cuenta, es que podemos ver que dice que la informacion que tenemos como nombre de usuario, correo electronico etc., ha sido sincronizado con los datos que proporciona el Keycloak:

![Acceso correcto con OTP](./imagenes/imagen-081.png)

## Resultado

Resumen de las pruebas realizadas:

| ID       | Caso de Prueba          | Metodo de Acceso       | Resultado Obtenido                          | Estado  |
|----------|---------------------------|-------------------------|----------------------------------------------|---------|
| TEST-01  | Acceso Directo            | Usuario/Pass Grafana   | Acceso exitoso                               | Correcto|
| TEST-02  | Acceso con OTP Erroneo   | Keycloak + MFA          | Bloqueo de cuenta y acceso denegado         | Correcto|
| TEST-03  | Acceso con OTP Correcto  | Keycloak + MFA          | Acceso exitoso y sincronizacion de perfil   | Correcto|

La monitorizacion avanzada y la gestion de alertas de seguridad quedan completadas con exito. Gracias a esta configuracion, se pueden detectar ataques de fuerza bruta, sobrecargas de CPU, caidas de servicios, logins sin MFA y conexiones VPN desconocidas, con notificaciones automaticas por Gmail.

