
# ZeroTrustHub — Despliegue Web con Nginx + Docker + HTTPS

## Descripción

Este módulo implementa el frontend público de ZeroTrustHub utilizando Nginx como servidor web y reverse proxy ligero, desplegado dentro de un contenedor Docker.

La infraestructura sirve una landing page estática orientada a servicios de ciberseguridad y arquitectura Zero Trust, incluyendo:

- HTTPS habilitado mediante certificados TLS
- Redirección automática HTTP → HTTPS
- Hosting estático optimizado
- Exposición controlada de puertos
- Aislamiento mediante red Docker dedicada
- Configuración minimalista y portable

El despliegue se encuentra contenido en:

```bash
~/zth-node-cloud/nginx
```

---

# Estructura del proyecto

```bash
nginx/
├── certs/
│   ├── cert.pem
│   ├── key.pem
│   ├── selfsigned.crt
│   └── selfsigned.key
├── docker-compose.yml
├── nginx.conf
└── zerotrusthub.html
```

---

# Objetivo del despliegue

El objetivo principal de esta configuración es:

- Publicar la web corporativa de ZeroTrustHub
- Forzar tráfico cifrado mediante TLS
- Mantener una arquitectura sencilla y reproducible
- Separar frontend del resto de servicios internos
- Facilitar integración futura con:
  - Keycloak
  - OAuth2 Proxy
  - Grafana
  - APIs internas
  - Reverse proxy avanzado

---

# Docker Compose

## Archivo

```yaml
version: "3.8"

services:
  nginx:
    image: nginx:1.24
    container_name: nginx-proxy
    restart: unless-stopped

    ports:
      - "80:80"
      - "443:443"
      - "127.0.0.1:8090:8090"

    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./zerotrusthub.html:/usr/share/nginx/html/zth/zerotrusthub.html:ro
      - ./certs:/etc/nginx/certs:ro

    networks:
      - zth-network

networks:
  zth-network:
    driver: bridge
```

---

# Explicación de la configuración

## Imagen utilizada

```yaml
image: nginx:1.24
```

Se utiliza una imagen oficial estable de Nginx basada en Debian.

Ventajas:

- Ligera
- Muy estable
- Fácil de mantener
- Compatible con TLS moderno
- Bajo consumo de recursos

---

## Nombre del contenedor

```yaml
container_name: nginx-proxy
```

Permite identificar el servicio fácilmente desde Docker.

Ejemplo:

```bash
docker ps
docker logs nginx-proxy
docker exec -it nginx-proxy bash
```

---

## Reinicio automático

```yaml
restart: unless-stopped
```

Garantiza persistencia del servicio tras:

- Reinicio del host
- Caída del contenedor
- Reinicio de Docker

---

# Exposición de puertos

## HTTP

```yaml
- "80:80"
```

Permite recibir tráfico HTTP externo.

Este tráfico es automáticamente redirigido a HTTPS.

---

## HTTPS

```yaml
- "443:443"
```

Puerto principal de producción.

Todo el contenido público se sirve mediante TLS.

---

## Puerto interno de testing

```yaml
- "127.0.0.1:8090:8090"
```

Se expone únicamente en localhost.

Objetivo:

- Testing local
- Debugging
- Validación sin TLS
- Verificación rápida del frontend

No es accesible externamente.

---

# Volúmenes montados

## Configuración Nginx

```yaml
- ./nginx.conf:/etc/nginx/nginx.conf:ro
```

Monta la configuración personalizada dentro del contenedor.

Modo:

```bash
ro = read-only
```

Esto evita modificaciones accidentales desde el contenedor.

---

## HTML principal

```yaml
- ./zerotrusthub.html:/usr/share/nginx/html/zth/zerotrusthub.html:ro
```

Monta el frontend estático principal.

La web completa está implementada en un único archivo HTML autosuficiente:

- HTML
- CSS
- JavaScript

Ventajas:

- Simplicidad
- Portabilidad
- Carga extremadamente rápida
- Sin dependencias externas complejas

---

## Certificados TLS

```yaml
- ./certs:/etc/nginx/certs:ro
```

Monta certificados SSL/TLS dentro del contenedor.

---

# Configuración Nginx

## Archivo principal

```nginx
events {
    worker_connections 1024;
}
```

Define el número máximo de conexiones concurrentes.

---

# Bloque HTTP

```nginx
http {
```

Aquí se configura toda la lógica web.

---

## MIME Types

```nginx
include /etc/nginx/mime.types;
```

Permite servir correctamente:

- HTML
- CSS
- JS
- JSON
- fuentes
- imágenes

---

## Optimización TCP

```nginx
sendfile on;
tcp_nopush on;
tcp_nodelay on;
```

Mejoras de rendimiento para transferencia de archivos.

---

## Compresión GZIP

```nginx
gzip on;
gzip_types text/plain text/css application/javascript application/json;
```

Reduce tamaño de respuesta para:

- CSS
- JavaScript
- JSON
- texto plano

Beneficios:

- Menor ancho de banda
- Mejor tiempo de carga
- Menor latencia

---

# Cabeceras de seguridad

## Protección clickjacking

```nginx
add_header X-Frame-Options "SAMEORIGIN";
```

Evita que la web sea embebida desde dominios externos.

---

## Protección MIME sniffing

```nginx
add_header X-Content-Type-Options "nosniff";
```

Impide interpretación incorrecta de contenido por navegadores.

---

# Redirección HTTP → HTTPS

## Configuración

```nginx
server {
    listen 80;
    server_name _;

    location / {
        return 301 https://$host$request_uri;
    }
}
```

Todo acceso HTTP es redirigido permanentemente hacia HTTPS.

Beneficios:

- Seguridad
- SEO
- Consistencia
- Evita tráfico no cifrado

---

# Servidor HTTPS principal

## Listener TLS

```nginx
listen 443 ssl;
```

Habilita HTTPS.

---

## Certificados

```nginx
ssl_certificate /etc/nginx/certs/cert.pem;
ssl_certificate_key /etc/nginx/certs/key.pem;
```

Certificado y clave privada montados desde Docker.

---

## Protocolos TLS

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
```

Se deshabilitan versiones inseguras:

- SSLv2
- SSLv3
- TLS 1.0
- TLS 1.1

---

## Root web

```nginx
root /usr/share/nginx/html/zth;
```

Directorio donde se sirve la web.

---

## Archivo principal

```nginx
index zerotrusthub.html;
```

Landing principal de ZeroTrustHub.

---

## Routing SPA-like

```nginx
location / {
    try_files $uri $uri/ /zerotrusthub.html;
}
```

Permite fallback automático al HTML principal.

Útil para:

- navegación interna
- anchors
- futuras SPAs
- rutas amigables

---

# Servidor local de testing

## Puerto 8090

```nginx
server {
    listen 8090;
}
```

Servidor HTTP local sin TLS.

Objetivos:

- pruebas rápidas
- validación visual
- troubleshooting

---

# Certificados TLS

## Estructura

```bash
certs/
├── cert.pem
├── key.pem
├── selfsigned.crt
└── selfsigned.key
```

---

## cert.pem / key.pem

Certificados actualmente utilizados por Nginx.

---

## selfsigned.*

Certificados autofirmados utilizados inicialmente para pruebas locales.

---

# Frontend ZeroTrustHub

## Arquitectura

La web se encuentra implementada en un único archivo:

```bash
zerotrusthub.html
```

Contiene:

- HTML5
- CSS3 avanzado
- JavaScript Vanilla

Sin frameworks externos.

---

# Características del frontend

## Diseño

La interfaz implementa una estética:

- Cybersecurity
- Zero Trust
- Terminal-style
- Futurista
- Minimalista

---

## Responsive Design

Incluye soporte responsive mediante media queries para:

- escritorio
- tablets
- móviles

---

## Animaciones

Se implementan:

- reveal on scroll
- glow effects
- terminal simulation
- ticker animado
- métricas dinámicas
- logs simulados
- dashboards visuales

Todo usando únicamente CSS + JavaScript nativo.

---

# Secciones implementadas

## Hero Section

Presentación principal de servicios:

- Zero Trust
- VPN
- IAM
- observabilidad
- hardening

---

## Servicios

Describe:

- WireGuard
- Keycloak
- MFA
- Grafana
- Prometheus
- Loki
- Pentesting

---

## Dashboard de estado

Simulación visual de:

- servicios activos
- túneles WireGuard
- métricas uptime
- logs de amenazas

---

## Proceso de despliegue

Pipeline operacional:

1. Auditoría
2. Diseño Zero Trust
3. Despliegue
4. Vigilancia continua

---

## Stack tecnológico

Visualización de tecnologías utilizadas:

- WireGuard
- Keycloak
- Docker
- Nginx
- Grafana
- Prometheus
- Loki
- AWS
- Kali Linux

---

## Terminal simulada

Simulación dinámica de comandos reales:

```bash
docker compose up -d
sudo wg show
docker ps
```

Genera sensación de infraestructura viva.

---

## Pentesting

Representación visual de pruebas de intrusión:

- MitM
- bruteforce
- bypass MFA
- scans
- acceso externo
- validaciones

---

## Pricing

Planes simulados:

- Starter
- Professional
- Enterprise

---

## Contacto

Formulario simple orientado a captación de leads.

---

# Seguridad aplicada

## HTTPS obligatorio

Todo tráfico externo cifrado.

---

## TLS moderno

Solo TLS 1.2 y 1.3.

---

## Headers de seguridad

Protecciones básicas habilitadas.

---

## Contenedor aislado

Servicio separado mediante Docker network.

---

## Montajes read-only

Reduce superficie de ataque.

---

# Red Docker

## Configuración

```yaml
networks:
  zth-network:
    driver: bridge
```

Permite futura integración con:

- Keycloak
- OAuth2 Proxy
- APIs privadas
- monitoring stack

---

# Comandos útiles

## Levantar servicio

```bash
docker compose up -d
```

---

## Ver logs

```bash
docker logs nginx-proxy
```

---

## Reiniciar

```bash
docker restart nginx-proxy
```

---

## Validar configuración Nginx

```bash
docker exec -it nginx-proxy nginx -t
```

---

## Acceder al contenedor

```bash
docker exec -it nginx-proxy bash
```

---

## Parar servicios

```bash
docker compose down
```

---

# Flujo de tráfico

```text
Cliente
   │
   ▼
Puerto 80
   │
   ▼
Redirección HTTPS
   │
   ▼
Puerto 443
   │
   ▼
Nginx
   │
   ▼
zerotrusthub.html
```

---

# Resultado final

La infraestructura consigue:

- Hosting web seguro
- HTTPS funcional
- Despliegue reproducible
- Frontend moderno
- Base preparada para Zero Trust real
- Integración futura con IAM y observabilidad

Todo ello utilizando únicamente:

- Docker
- Nginx
- HTML/CSS/JS nativo

sin necesidad de frameworks pesados ni dependencias innecesarias.

---

# Próximas mejoras previstas

## Certificados Let's Encrypt

Sustituir certificados manuales por renovación automática.

---

## Reverse Proxy avanzado

Integración completa con:

- Keycloak
- OAuth2 Proxy
- APIs internas

---

## CSP Headers

Añadir:

```nginx
Content-Security-Policy
```

---

## Rate Limiting

Protección contra bruteforce y scraping.

---

## Fail2Ban

Mitigación automática de ataques.

---

## HSTS

Forzar HTTPS permanente.

---

# Estado actual

Estado del despliegue:

```text
Nginx: operativo
HTTPS: operativo
Docker: operativo
Frontend: operativo
TLS: operativo
Redirección HTTP→HTTPS: operativa
Puerto local testing 8090: operativo
```


