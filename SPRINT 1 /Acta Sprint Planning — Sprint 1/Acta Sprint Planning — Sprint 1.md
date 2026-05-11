# **Acta Sprint Planning — Sprint 1**

**Fecha:** 20/04/2026  
**Participantes:** Bryan Aguilera, Javier Vericat, Giuseppe Suarez  
**Sprint:** Sprint 1 — Fundamentos y conectividad

**Objetivo del sprint:**  
Configurar el entorno de trabajo del proyecto, desplegar y validar la conectividad entre el nodo local y el nodo cloud mediante WireGuard, e instalar Keycloak con la configuración inicial de identidad y MFA.

**Tareas planificadas:**

* Crear el repositorio GitHub con la estructura inicial del proyecto.  
* Configurar ProofHub con sprints, etiquetas y responsables.  
* Aprovisionar la máquina virtual de Isard.  
* Crear la instancia AWS EC2.  
* Configurar acceso SSH seguro en ambos nodos.  
* Aplicar hardening básico del sistema.  
* Instalar WireGuard en el nodo local.  
* Instalar WireGuard en el nodo AWS.  
* Generar e intercambiar claves de WireGuard.  
* Configurar el túnel VPN entre ambos nodos.  
* Verificar la conectividad entre nodos.  
* Instalar Keycloak mediante Docker Compose.  
* Crear el realm, usuarios y roles.  
* Activar MFA obligatorio.  
* Configurar Nginx como proxy inverso de Keycloak.

**Reparto de trabajo:**  
Bryan Aguilera se encargará del aprovisionamiento de la instancia AWS, la instalación de WireGuard en AWS, la validación del túnel WireGuard, la conexión segura y la configuración del SSH del nodo cloud. Además, asumirá el rol de Scrum Master general, coordinando el seguimiento del sprint y el cierre con actas y evidencias.

Javier Vericat se encargará del aprovisionamiento del nodo Isard, la creación de la base del proyecto, la instalación de WireGuard en Isard, la configuración de claves y reglas de red, la configuración de Nginx como proxy inverso y la instalación y configuración de Docker.

Giuseppe Suarez se encargará del hardening del sistema, incluyendo fail2ban, la configuración del acceso SSH seguro, el despliegue de Keycloak con Docker Compose, la creación del realm, usuarios y roles, y la activación del MFA y de las políticas de seguridad.

**Riesgos detectados:**

* Pueden surgir problemas de acceso o permisos en AWS.  
* Puede haber errores en la configuración de SSH.  
* La conectividad de WireGuard entre ambos nodos puede fallar durante la configuración inicial.  
* También puede haber retrasos en la integración entre el nodo local y el nodo cloud.

**Resultado esperado del sprint:**  
Dejar operativos los dos nodos del proyecto, con acceso SSH seguro, túnel WireGuard configurado y Keycloak desplegado con la configuración inicial de identidad.

**Evidencias:**  
Se añadirá una captura del tablero de ProofHub en la acta de Sprint Review.
