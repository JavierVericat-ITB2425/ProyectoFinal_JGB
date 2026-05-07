# **Infraestructura de Monitorización y Observabilidad (Loki & Grafana)**

![Portada](imagenes/img-000.png)

**N°:** GRUPO 8  
**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez  
**Profesores:** Sergi - David Sicart

# **Índice**
- [1. Gestion de Directorios](#1-gestion-de-directorios)
- [2. Docker Compose](#2-docker-compose)
  - [2.1 Configuración](#21-configuración)
  - [2.2 Ejecución](#22-ejecución)
  - [2.3 Firewall](#23-firewall)
  - [2.4 Comprobación de acceso](#24-comprobación-de-acceso)
  - [2.5 Configuración de Loki Web](#25-configuración-de-loki-web)
  - [2.6 Comprobaciones de LOGS](#26-comprobaciones-de-logs)

---

## **1. Gestion de Directorios**

   Crearemos un directorio donde tendremos los archivos de monitoreo:

```bash
mkdir -p ~/zth-node-cloud/monitoring
```
   ![Creación de loki-config.yml](imagenes/img-001.png)

   Accedemos al directorio:

```bash
cd ~/zth-node-cloud/monitoring
```
   ![Configuración de Loki](imagenes/img-002.png)

---

## **2. Docker Compose**

### **2.1 Configuración**

   Crearemos un archivo `.yml`, donde estarán los contenedores para que se enciendan juntos.

   Primero crearemos la configuración de nuestro servicio de **Loki**:

```bash
sudo nano loki-config.yml
```
   ![Creación de docker-compose.yml](imagenes/img-003.png)

   Y contendría lo siguiente:

```yaml
auth_enabled: false

server:
  http_listen_port: 3100

common:
  path_prefix: /loki
  storage:
    filesystem:
      chunks_directory: /loki/chunks
      rules_directory: /loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

limits_config:
  allow_structured_metadata: false
  reject_old_samples: true
  reject_old_samples_max_age: 168h

compactor:
  working_directory: /loki/compactor
```
   ![Configuración de servicios en Docker Compose](imagenes/img-004.png)

   Ahora crearemos el archivo para los contenedores:

```bash
sudo nano docker-compose.yml
```
   ![Ejecución de Docker Compose](imagenes/img-005.png)

   Y este archivo contendrá lo siguiente:

```yaml
services:
  # Interfaz visual para consultar los logs
  grafana:
    image: grafana/grafana:11.0.0
    container_name: grafana
    restart: unless-stopped
    environment:
      GF_SECURITY_ADMIN_PASSWORD: admin1234 # Contraseña definida tras el reset
      GF_AUTH_GENERIC_OAUTH_ENABLED: "false" # Desactivado para validación inicial
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitoring-net

  # Motor de almacenamiento y procesamiento de logs
  loki:
    image: grafana/loki:3.0.0
    container_name: loki
    restart: unless-stopped
    ports:
      - "3100:3100"
    volumes:
      - ./loki-config.yml:/etc/loki/local-config.yaml
      - loki_data:/loki
    command: -config.file=/etc/loki/local-config.yaml
    networks:
      - monitoring-net

  # Agente recolector de logs del servidor
  promtail:
    image: grafana/promtail:3.0.0
    container_name: promtail-nodeA
    restart: unless-stopped
    volumes:
      - ./promtail-config.yml:/etc/promtail/config.yml
      - /var/log:/var/log:ro
    command: -config.file=/etc/promtail/config.yml
    networks:
      - monitoring-net

volumes:
  grafana_data:
  loki_data:

networks:
  monitoring-net:
    driver: bridge
```
   ![Configuración de Firewall](imagenes/img-006.png)

### **2.2 Ejecución**

   Ahora ejecutamos los dockers para encender los servicios:

```bash
docker compose up -d
```
   ![Acceso a Grafana](imagenes/img-007.png)


### **2.3 Firewall**

   Una vez tenemos los servicios ejecutándose, deberemos de aplicar una nueva regla para el puerto del grafana:

```bash
sudo ufw allow 3000/tcp
```
   ![Panel de inicio de Grafana](imagenes/img-008.png)


### **2.4 Comprobación de acceso**

   Ahora para acceder pondremos lo siguiente en la URL:

   `http://192.168.18.10:3000`


   Pondremos las credenciales y podremos acceder.
   ![Búsqueda de Loki en Grafana](imagenes/img-009.png)


### **2.5 Configuración de Loki Web**

   Ahora procederemos a configurar el **Loki** en el Grafana. Nos dirigiremos al apartado de **Connections** y después a **Add Data Source** y buscamos **Loki**.
   ![Selección de Loki](imagenes/img-010.png)
   ![Configuración de URL de Loki](imagenes/img-011.png)


   Ahora damos clic:

   ![Validación de conexión exitosa](imagenes/img-012.png)

   Ahora lo que hacemos es indicar como URL la siguiente:

   `http://loki:3100`

  ![Configuración de Explore](imagenes/img-013.png)


   Ahora lo que haremos es darle a **Save & Test** y aparecerá el mensaje de que la validación es correcta.

   ![Validación de conexión exitosa](imagenes/Save&Test.png)
### **2.6 Comprobaciones de LOGS**

   Para comprobar si funciona o no, nos vamos al apartado de **Explore**. En **Select Label** seleccionamos la opción de `job` y el **Select Value** la opción de `auth`. Para finalizar, le damos clic donde dice **Live**.

   ![Configuración de Explore](imagenes/img-013.png)

   Esto debería de quedar tal que así:

   ![Visualización de logs en vivo](imagenes/img-014.png)

   Ahora lo que hacemos es un SSH desde nuestro cliente hacia el servidor y fallamos la contraseña a propósito:

```bash
ssh user@server -p 2222
```

   ![Intento de SSH fallido](imagenes/img-015.png)

   Así generamos logs y podemos ver cómo aparecen al instante los logs:

   ![Logs de error en tiempo real](imagenes/img-016.png)

   En estos logs también podemos ver desde donde se hace, es decir la IP de origen, vemos que el puerto del origen es 2222 que es el configurado, etc.

   ![Detalle de los logs de SSH](imagenes/img-017.png)

   Si inspeccionamos uno de los logs podemos ver más a fondo y concreto lo que nos dice.

   ![Inspección detallada de log](imagenes/img-018.png)
