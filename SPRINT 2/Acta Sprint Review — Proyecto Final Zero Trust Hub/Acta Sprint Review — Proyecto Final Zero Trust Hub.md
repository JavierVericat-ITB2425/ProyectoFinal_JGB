# Acta Sprint Review — Proyecto Final Zero Trust Hub

## 1) Datos de la sesión

Acta de cierre/review general del proyecto.


Fecha: 5/11/2026
Participantes: Bryan Aguilera, Javier Vericat, Giuseppe Suarez
Sprint: Review general del proyecto finalActa Sprint Review — Proyecto Final Zero Trust Hub


## 2) Objetivo del sprint / cierre del proyecto

El objetivo del proyecto es diseñar, desplegar y validar una infraestructura híbrida basada en Zero Trust: nodo local + nodo cloud (AWS) conectados por WireGuard, identidad con Keycloak, proxy inverso con Nginx, monitorización con Grafana/Loki/Prometheus y pruebas de seguridad.


Infraestructura híbrida Zero Trust:
- WireGuard (túnel)
- Keycloak (identidad)
- Nginx (proxy inverso)
- Grafana + Loki + Prometheus (monitorización)
- Pruebas de seguridad (escaneos, bruteforce, validación cifrado)


## 3) Trabajo realizado (verificaciones técnicas)

Se desplegó nodo local (Isard) y nodo cloud (AWS), se configuró WireGuard y se validó la comunicación. Se endureció SSH moviéndolo al puerto 2222.
```bash
# Comprobar WireGuard e interfaz
sudo wg
ip a show wg0

# Validar conectividad por el túnel
ping 10.8.0.1 -c 4
ping 10.8.0.2 -c 4

# Verificar SSH en puerto 2222
sudo ss -lntp | grep 2222
ssh -p 2222 usuario@ip-del-nodo
```



## 4) Servicios desplegados (Keycloak, Nginx, monitorización)

Se desplegó Keycloak (realm/usuarios/roles/políticas y protección anti-bruteforce), Nginx como proxy inverso y el stack de monitorización (Grafana/Loki/Prometheus) con dashboards y alertas.
```bash
# Ver contenedores/servicios activos
docker ps

# Ver puertos publicados
docker ps --format "table {{.Names}}\t{{.Ports}}"
```



## 5) Pruebas de seguridad realizadas

Se realizaron escaneos de puertos (nodo local y nodo cloud), validación del cifrado del túnel WireGuard, pruebas de fuerza bruta sobre SSH y validación del bloqueo temporal de usuarios en Keycloak ante múltiples intentos fallidos.
```bash
# Escaneo de puertos
sudo nmap -Pn -sV 192.168.18.10
sudo nmap -Pn -sV 52.87.11.36

# Escaneo de puertos UDP de WireGuard
sudo nmap -Pn -sU -p 51820 52.87.11.36

# Fuerza bruta SSH (prueba controlada)
hydra -l testssh -P passwords_test.txt ssh://192.168.18.10:2222 -V

```


## 6) Incidencias y dificultades

Se reportan incidencias relevantes:
- Expiración de la clase/recursos de AWS y pérdida del nodo cloud, obligando a redeploy y reconfiguración.
- Caídas de accesibilidad del nodo cloud y reconstrucción parcial del trabajo.
- Dificultades de integración de métricas/alertas en Grafana entre nodo local y AWS.
- Problemas de Nginx/WireGuard por cambio de IP pública del nodo local (requiere actualizar endpoint del peer).
- Problemas de estabilidad de acceso SSH por cambios de configuración (puerto/servicio).


Checklist de recuperación rápida (resumen):
- Validar IP pública actual del nodo local.
- Actualizar endpoint del peer WireGuard.
- Revalidar túnel (wg/ping).
- Revalidar acceso SSH (puerto 2222, servicio activo).
- Revalidar dashboards/alertas (fuentes de datos en Grafana).

## 7) Tareas finalizadas

Durante el cierre del proyecto se completaron las siguientes tareas finalizadas visibles en el tablero:

- Integrar Grafana con Keycloak mediante OIDC.
- Arreglar web.
- Ejecutar la prueba 1: MitM sobre el túnel WireGuard.
- Acabar de documentar el nginx.
- Configuración de nginx para que sea visible desde el exterior.
- Arquitectura de la red.
- Crear y explicar el esquema de red.
- Instalar y configurar el sitio web.
- Realizar Sprint Planning del Sprint 2.
- Configurar auth_request con Keycloak.
- Ejecutar la prueba 2: Bruteforce SSH con Hydra.
- Activar el event listener de Keycloak.
- Ejecutar la prueba 3: Bypass MFA en Keycloak.
- Realizar test end-to-end de autenticación.
- Completar la prueba 4: Acceso a servicios internos sin VPN.
- Instalar Loki y Grafana en el nodo principal.
- Completar la prueba 5: Escaneo de puertos desde Internet.
- Instalar Promtail en ambos nodos.
- Ejecutar la prueba 6: Bruteforce web contra Keycloak.
- Instalar Prometheus y Node Exporter.
- Crear dashboards de Grafana.
- Redactar la documentación técnica de administrador.
- Probar las alertas.
- Revisar y limpiar el repositorio GitHub.
- Configurar la VM Kali Linux aislada.
- Preparar los diagramas de arquitectura y material visual.
- Instalar herramientas de pentesting.
- Preparar las slides de la presentación final.
- Ejecutar la prueba 4 de validación parcial.
- Ensayar la demostración práctica.
- Realizar revisión cruzada del conocimiento.
- Realizar el Sprint Review del Sprint 2.

Según el tablero, el total de tareas finalizadas es 34, pero en las capturas facilitadas solo se visualizan 32 de ellas, por lo que quedarían 2 tareas no visibles en la imagen.

## 8) Resultado final y valoración

El proyecto se completa satisfactoriamente con una infraestructura híbrida funcional, conectividad segura (WireGuard), acceso endurecido (SSH), identidad centralizada (Keycloak), proxy inverso (Nginx) y monitorización operativa (Grafana/Loki/Prometheus), validando controles de seguridad mediante pruebas prácticas y dejando documentación y evidencias recopiladas.


Conclusión:
Solución coherente con Zero Trust, con conectividad segura, autenticación reforzada,
servicios protegidos, monitorización operativa y validaciones de seguridad.

**Evidencias:**  
![img1](https://github.com/JavierVericat-ITB2425/ProyectoFinal_JGB/blob/bbc72b223e1640a900f237699c56e2ddc7608c71/SPRINT%202/Acta%20Sprint%20Review%20%E2%80%94%20Proyecto%20Final%20Zero%20Trust%20Hub/images/Captura%20de%20pantalla%20de%202026-05-11%2020-01-41.png)
![img2](https://github.com/JavierVericat-ITB2425/ProyectoFinal_JGB/blob/bbc72b223e1640a900f237699c56e2ddc7608c71/SPRINT%202/Acta%20Sprint%20Review%20%E2%80%94%20Proyecto%20Final%20Zero%20Trust%20Hub/images/Captura%20de%20pantalla%20de%202026-05-11%2020-01-52.png)
