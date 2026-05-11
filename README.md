# ProyectoFinal JGB — Zero Trust Hub

Proyecto de Integración de Sistemas de Seguridad Híbridos — Grupo 8

---

## Índice

- [Descripción](#descripción)
- [Arquitectura de red](#arquitectura-de-red)
- [Sprint 1 — Infraestructura base](#sprint-1--infraestructura-base)
- [Sprint 2 — Monitorización, seguridad y documentación final](#sprint-2--monitorización-seguridad-y-documentación-final)

---

## Descripción

Zero Trust Hub es un proyecto que implementa el modelo de seguridad Zero Trust sobre una infraestructura híbrida formada por un nodo en la nube (AWS) y un nodo local (IsardVDI). La comunicación entre nodos se cifra mediante WireGuard, los servicios se despliegan en contenedores Docker, la autenticación se centraliza en Keycloak y la observabilidad se cubre con Grafana y Loki.

---

## Arquitectura de red

- [Explicación del esquema de red](<SPRINT 1 /Esquema-Red/ExplicacionRed.md>) — descripción detallada de la topología híbrida
- [Diagrama de red](<SPRINT 1 /Esquema-Red/esquema red.png>)

---

## Sprint 1 — Infraestructura base

### Actas

- [Acta Sprint Planning — Sprint 1](<SPRINT 1 /Acta Sprint Planning — Sprint 1/Acta Sprint Planning — Sprint 1.md>)
- [Acta Sprint Review — Sprint 1](<SPRINT 1 /Acta Sprint Review — Sprint 1/Acta Sprint Review — Sprint 1.md>)

### Infraestructura AWS

- [Creación y configuración de la instancia AWS (zth-node-cloud)](<SPRINT 1 /Creacion y Configuración de la instancia en aws zth-node-cloud/Creacion y Configuración de la instancia en aws zth-node-cloud.md>)
- [WireGuard en AWS (zth-node-cloud)](<SPRINT 1 /Creación y Configuración de WireGuard en AWS zth-node-cloud/Creación y Configuración de WireGuard en AWS zth-node-cloud.md>)

### Nodo local (IsardVDI)

- [WireGuard en nodo Isard](<SPRINT 1 /Instalación y Configuración del WireGuard en el node Isard/Instalación y Configuración del WireGuard en el node Isard.md>)

### Docker

- [Instalación y despliegue de Docker](<SPRINT 1 /Docker/Instalación y Despliegue de DOCKER.md>)

### Hardening

- [Hardening inicial y despliegue de infraestructura segura](<SPRINT 1 /Hardening Inicial y Despliegue de Infraestructura Segura/Hardening-Inicial-y-Despliegue-de-Infraestructura-Segura.md>)

---

## Sprint 2 — Monitorización, seguridad y documentación final

### Actas

- [Acta Sprint Planning — Sprint 2](<SPRINT 2/Acta Sprint Planning — Sprint 2/Acta Sprint Planning — Sprint 2.md>)
- [Acta Sprint Review — Zero Trust Hub](<SPRINT 2/Acta Sprint Review — Proyecto Final Zero Trust Hub/Acta Sprint Review — Proyecto Final Zero Trust Hub.md>)

### Autenticación

- [Configuración de Event Listeners (Keycloak)](<SPRINT 2/Configuracion_Event_Listeners/Configuracion_Event_Listeners.md>)
- [Integración de Grafana con Keycloak](<SPRINT 2/Integración de Grafana con Keycloak/Integracion-de-Grafana-con-Keycloak.md>)

### Monitorización

- [Despliegue del stack Grafana + Loki (Nodo Local)](<SPRINT 2/Despliegue de Stack Grafana + Loki (Nodo Local)/Despliegue_Grafana_Loki_Local.md>)
- [Configuración de Promtail en entorno Isard](<SPRINT 2/Configuración de Promtail en Entorno Isard/Configuracion_Promtail_Isard.md>)
- [Configuración de Promtail en instancia AWS](<SPRINT 2/Configuración de Promtail en Instancia AWS/Configuracion_Promtail_AWS.md>)
- [Monitorización y alertas de seguridad](<SPRINT 2/Monitorizacion Alertas Seguridad/Monitorizacion-Alertas-Seguridad.md>)

### Exposición de servicios

- [Configuración de NGINX](<SPRINT 2/NGINX/Configuracion-Nginx.md>)
- [Configuración de NGINX visible desde el exterior](<SPRINT 2/Configuración de Nginx para que sea visible desde el exterior/Configuración de Nginx para que sea visible desde el exterior.md>)

### Pruebas de seguridad

- [Creación del entorno Kali](<SPRINT 2/Creación del KALI/Creación del KALI.md>)
- [Pruebas de seguridad contra los nodos](<SPRINT 2/PRUEBAS DE SEGURIDAD CONTRA LOS NODOS/PRUEBAS DE SEGURIDAD CONTRA LOS NODOS.md>)
- [Pruebas MitM sobre el túnel WireGuard (nodo local a AWS)](<SPRINT 2/Pruebas MitM sobre el túnel WireGuard entel el nodo local y el nodo en aws/Pruebas MitM sobre el túnel WireGuard entel el nodo local y el nodo en aws.md>)

### Documentación final

- [Documentación de cliente — Zero Trust Hub](<SPRINT 2/Documentación de cliente — Zero Trust Hub/Documentación de cliente — Zero Trust Hub.md>)
- [Documentación técnica de administrador — Zero Trust Hub](<SPRINT 2/Documentación técnica de administrador — Zero Trust Hub/Documentación técnica de administrador — Zero Trust Hub.md>)
