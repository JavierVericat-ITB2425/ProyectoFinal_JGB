# **Acta Sprint Review — Sprint 1**

Fecha: 21/04/2026  
Participantes: Bryan Aguilera, Javier Vericat, Giuseppe Suarez  
Sprint: Sprint 1 — Fundamentos y conectividad

**Objetivo previsto:**  
Configurar el entorno de trabajo del proyecto, desplegar y validar la conectividad entre el nodo local y el nodo cloud mediante WireGuard, e instalar Keycloak con la configuración inicial de identidad y MFA.

**Trabajo realizado:**

* Se creó el repositorio GitHub con la estructura inicial del proyecto.  
* Se configuró ProofHub para la gestión del Sprint 1\.  
* Se aprovisionó el nodo local en Isard.  
* Se creó y configuró una instancia AWS EC2 para el entorno cloud.  
* Se configuró el acceso SSH mediante clave en los nodos.  
* Se aplicó hardening básico en el sistema.  
* Se instaló WireGuard en el nodo local y en AWS.  
* Se generaron las claves y se configuró la interfaz wg0 en AWS.  
* Se instaló Keycloak mediante Docker Compose.  
* Se iniciaron las configuraciones del realm, los roles y el MFA.

**Tareas completadas:**

* Repositorio inicial creado.  
* ProofHub configurado.  
* Nodo local desplegado.  
* Nodo AWS desplegado.  
* SSH seguro configurado.  
* WireGuard instalado y levantado en AWS.  
* Keycloak instalado.

**Incidencias encontradas:**

* Hubo problemas al cambiar el puerto SSH al 2222\.  
* Se perdió el acceso a una instancia AWS por una configuración incorrecta del servicio SSH.  
* Fue necesario recrear la instancia cloud para corregir el acceso.  
* Se detectaron ajustes pendientes en la configuración del peer local de WireGuard.

**Resultado del sprint:**  
Se ha dejado preparada la base del proyecto: repositorio, nodos, acceso remoto seguro, despliegue inicial de Keycloak y configuración de WireGuard en AWS. 

**Decisiones para el siguiente sprint:**

* Continuar con la integración de servicios mediante Keycloak.  
* Iniciar el despliegue del sistema de monitorización.  
* Mantener la documentación técnica actualizada en GitHub.

**Evidencias:**  
Se añadirá una captura del estado final de ProofHub, junto con los commits del sprint y la documentación técnica subida al repositorio.

