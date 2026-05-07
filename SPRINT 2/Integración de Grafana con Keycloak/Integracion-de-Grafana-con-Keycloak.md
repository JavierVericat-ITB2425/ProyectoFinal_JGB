# Integracion de Grafana con Keycloak mediante OIDC

## Indice

- [Informacion general](#informacion-general)
- [1. Configuracion en Keycloak](#1-configuracion-en-keycloak)
- [1.1 Crear el cliente en Keycloak](#11-crear-el-cliente-en-keycloak)
- [1.2 Rol del cliente](#12-rol-del-cliente)
- [2. Configurar Docker Compose](#2-configurar-docker-compose)
- [3. Comprobacion](#3-comprobacion)
- [Resultado](#resultado)

## Informacion general

![Portada](./imagenes-integracion-grafana-keycloak/imagen-001.png)

- Grupo: 8
- Integrantes: Javier Vericat, Bryan Aguilera y Giuseppe Suarez
- Profesores: Sergi y David Sicart
- Objetivo: integrar Grafana con Keycloak mediante OIDC para centralizar el inicio de sesion.

## 1. Configuracion en Keycloak

### 1.1 Crear el cliente en Keycloak

Primero, desde Keycloak se crea un cliente nuevo para Grafana.

![Creacion del cliente](./imagenes-integracion-grafana-keycloak/imagen-002.png)

Despues se configura con los parametros necesarios para permitir la autenticacion mediante OIDC.

![Configuracion inicial del cliente](./imagenes-integracion-grafana-keycloak/imagen-003.png)

Puntos importantes de esta configuracion:

- `Client Authentication`: genera una contrasena maestra para que Grafana pueda conectarse con Keycloak.
- `Authorization`: se deja desactivado porque no se necesitan permisos avanzados en este escenario.
- `Direct Access Grants`: se habilita para permitir pruebas directas de obtencion de token.

La siguiente configuracion define el flujo de autenticacion completo entre ambos servicios.

![Configuracion del flujo OIDC](./imagenes-integracion-grafana-keycloak/imagen-004.png)

Flujo esperado:

1. El usuario entra en Grafana.
2. Grafana redirige al usuario a Keycloak.
3. Keycloak solicita credenciales y el codigo MFA.
4. Si la autenticacion es correcta, Keycloak redirige de vuelta a Grafana con un token.
5. Grafana valida el token con el `client secret` y concede el acceso.

Cliente generado correctamente:

![Cliente creado](./imagenes-integracion-grafana-keycloak/imagen-005.png)

### 1.2 Rol del cliente

Una vez creado el cliente, hay que indicar el rol que va a tener, por ejemplo administrador o trabajador. Esta configuracion se realiza desde `Client Scopes`.

![Client Scopes](./imagenes-integracion-grafana-keycloak/imagen-006.png)

Despues se selecciona la opcion `Configure a new mapper`.

![Nuevo mapper](./imagenes-integracion-grafana-keycloak/imagen-007.png)

A continuacion se rellena la configuracion del mapper:

![Configuracion del mapper](./imagenes-integracion-grafana-keycloak/imagen-008.png)

Con estos datos conseguimos que la informacion del rol viaje correctamente dentro del token.

![Datos del mapper](./imagenes-integracion-grafana-keycloak/imagen-009.png)

## 2. Configurar Docker Compose

Antes de modificar Grafana, hay que obtener el `client secret` desde la pestana `Credentials` de Keycloak.

![Client secret](./imagenes-integracion-grafana-keycloak/imagen-010.png)

Una vez copiado, se edita el archivo `docker-compose.yml`.

```bash
sudo nano ~/zth-node-cloud/monitoring/docker-compose.yml
```

Configuracion utilizada:

```yaml
services:
  grafana:
    image: grafana/grafana:11.0.0
    container_name: grafana
    restart: unless-stopped
    environment:
      GF_SECURITY_ADMIN_PASSWORD: <GRAFANA_ADMIN_PASS>
      GF_AUTH_GENERIC_OAUTH_ENABLED: "true"
      GF_AUTH_GENERIC_OAUTH_NAME: Keycloak
      GF_AUTH_GENERIC_OAUTH_CLIENT_ID: grafana
      GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET: AQUI_IRA_EL_CLIENT_SECRET
      GF_AUTH_GENERIC_OAUTH_AUTH_URL: http://192.168.18.10:8080/realms/zth-node-cloud/protocol/openid-connect/auth
      GF_AUTH_GENERIC_OAUTH_TOKEN_URL: http://192.168.18.10:8080/realms/zth-node-cloud/protocol/openid-connect/token
      GF_AUTH_GENERIC_OAUTH_USERINFO_URL: http://192.168.18.10:8080/realms/zth-node-cloud/protocol/openid-connect/userinfo
      GF_AUTH_GENERIC_OAUTH_REDIRECT_URL: http://192.168.18.10:3000/login/generic_oauth
      GF_AUTH_GENERIC_OAUTH_SCOPES: openid profile email
      GF_SERVER_DOMAIN: 192.168.18.10
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitoring-net

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

  prometheus:
    image: prom/prometheus:v2.51.0
    container_name: prometheus
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - monitoring-net

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

  node-exporter:
    image: prom/node-exporter:v1.7.0
    container_name: node-exporter-nodeA
    restart: unless-stopped
    pid: host
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - "--path.procfs=/host/proc"
      - "--path.sysfs=/host/sys"
    ports:
      - "127.0.0.1:9100:9100"
    networks:
      - monitoring-net

volumes:
  grafana_data:
  loki_data:
  prometheus_data:

networks:
  monitoring-net:
    driver: bridge
```

Ejemplo del resultado en el fichero:

![Docker Compose configurado](./imagenes-integracion-grafana-keycloak/imagen-011.png)

## 3. Comprobacion

Al entrar en la pagina de Grafana aparece la opcion para iniciar sesion con Keycloak.

![Login con Keycloak en Grafana](./imagenes-integracion-grafana-keycloak/imagen-012.png)

Al pulsar en `Sign in with Keycloak`, se muestra el formulario de autenticacion.

![Formulario de acceso](./imagenes-integracion-grafana-keycloak/imagen-013.png)

Se introducen las credenciales del usuario.

![Credenciales de usuario](./imagenes-integracion-grafana-keycloak/imagen-014.png)

Despues se solicita el codigo MFA.

![Codigo MFA](./imagenes-integracion-grafana-keycloak/imagen-015.png)

Cuando la autenticacion finaliza correctamente, se accede a Grafana sin necesidad de usuarios locales.

![Acceso correcto a Grafana](./imagenes-integracion-grafana-keycloak/imagen-016.png)

## Resultado

La integracion OIDC entre Grafana y Keycloak queda completada con exito. Gracias a esta configuracion, la autenticacion se centraliza en Keycloak y ya no es necesario gestionar usuarios locales directamente desde Grafana.
