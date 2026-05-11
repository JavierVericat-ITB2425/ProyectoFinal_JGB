# Acta Sprint Planning — Sprint 2

## 1) Datos de la sesión

Acta de planificación del Sprint 2 (integración, monitorización, pentesting y cierre del proyecto).


Fecha: 20/04/2026
Integrantes: Bryan Aguilera, Javier Vericat, Giuseppe Suarez
Sprint: Sprint 2 — Integración, monitorización, pentesting y cierre del proyecto




## 2) Objetivo del sprint

Completar la integración de servicios con Keycloak, desplegar y validar la monitorización, ejecutar las pruebas de intrusión, cerrar la documentación final y preparar la presentación/demostración para la defensa.


Objetivo:
- Integración Keycloak (SSO/OIDC)
- Monitorización (Grafana/Loki/Prometheus)
- Pentesting (pruebas del proyecto)
- Cierre documentación y demo final




## 3) Tareas planificadas

Se listan las tareas previstas para completar el sprint.


Integración y autenticación:
- Integrar Grafana con Keycloak mediante OIDC.
- Configurar auth_request en Nginx para proteger los servicios internos.
- Activar el event listener de Keycloak para el registro de autenticaciones.
- Realizar pruebas end-to-end de autenticación con MFA.

Monitorización:
- Desplegar Loki y Grafana en el nodo principal.
- Instalar y configurar Promtail en los nodos necesarios.
- Instalar y configurar Prometheus y Node Exporter.
- Crear los dashboards de Grafana del proyecto.
- Configurar las alertas de seguridad y disponibilidad.
- Probar las alertas generando eventos reales de prueba.

Pentesting:
- Preparar la máquina Kali Linux en red de pruebas independiente.
- Instalar y verificar herramientas: Wireshark, Hydra, Burp Suite, nmap y Ettercap.
- Prueba 1: MitM sobre el túnel WireGuard.
- Prueba 2: Bruteforce SSH con Hydra.
- Prueba 3: Bypass MFA en Keycloak.
- Prueba 4: Acceso a servicios internos sin VPN.
- Prueba 5: Escaneo de puertos desde Internet.
- Prueba 6: Bruteforce web contra Keycloak.
- Redactar el informe final de pentesting.

Documentación y cierre:
- Redactar la documentación técnica de administrador.
- Redactar la documentación de cliente.
- Revisar y limpiar el repositorio GitHub.
- Preparar diagramas y material visual del proyecto.
- Preparar las slides de la presentación final.
- Ensayar la demostración práctica.
- Revisión cruzada del conocimiento entre los miembros del equipo.
- Actualizar ProofHub y mantener evidencias del trabajo realizado.




## 4) Reparto de trabajo

Se define el reparto de tareas por integrante.


Bryan Aguilera:
- Coordinación general del sprint
- Integración autenticación con Keycloak
- Validación de accesos
- Parte de las pruebas de seguridad
- Seguimiento de documentación y evidencias

Javier Vericat:
- Infraestructura local
- Servicios base, Nginx, Docker
- Monitorización a nivel de sistema
- Soporte técnico para la demo y documentación técnica

Giuseppe Suarez:
- Parte cloud
- Hardening y seguridad operativa
- Configuraciones de acceso seguro
- Parte del pentesting
- Apoyo en documentación final y validación de resultados




## 5) Riesgos detectados

Riesgos que pueden afectar a la ejecución del sprint.


- Errores de integración entre Keycloak, Nginx y Grafana.
- Alertas que no disparan por mala recolección de logs/métricas.
- Pruebas de intrusión que requieran ajustes previos de red/configuración.
- Retrasos de documentación final si no se redacta en paralelo.
- Fallos en la demo práctica si no se ensaya en condiciones realistas.
- Partes del proyecto no suficientemente dominadas por todos los miembros.




## 6) Resultado esperado del sprint

Dejar el proyecto funcional, monitorizado, validado mediante pruebas de intrusión, documentado y preparado para la defensa final.


Resultado esperado:
- Proyecto funcional
- Monitorización operativa
- Pentesting ejecutado y reportado
- Documentación completa
- Material y demo listos para la defensa




## 7) Evidencias previstas

Evidencias que se espera recopilar durante el sprint.


- ProofHub con tareas asignadas y completadas.
- Commits y documentación continua en GitHub.
- Capturas de dashboards, alertas y pruebas de pentesting.
- Slides y material de apoyo para la defensa.




