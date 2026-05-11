# **Acta Sprint Review — Sprint 1**

Fecha: 21/04/2026  
Participantes: Bryan Aguilera, Javier Vericat, Giuseppe Suarez  
Sprint: Sprint 1 — Fundamentos y conectividad

**Objetivo previsto:**  
Configurar el entorno de trabajo del proyecto, desplegar y validar la conectividad entre el nodo local y el nodo cloud mediante WireGuard, e instalar Keycloak con la configuración inicial de identidad y MFA.

**Trabajo realizado:**

* Se creó el repositorio GitHub con la estructura inicial del proyecto.  
* Se configuró ProofHub para la gestión del Sprint 1\.  
* Se aprovisionó el nodo Captura de pantalla de 2026-05-11 19-45-20.pnglocal en Isard.  
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

![Profhub](https://github.com/JavierVericat-ITB2425/ProyectoFinal_JGB/blob/f3e63f69758781801340a9838a71eb9d3d88ec55/SPRINT%201%20/Acta%20Sprint%20Review%20%E2%80%94%20Sprint%201/images/Captura%20de%20pantalla%20de%202026-05-11%2019-45-20.png)

