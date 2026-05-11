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


## 7) Resultado final y valoración

El proyecto se completa satisfactoriamente con una infraestructura híbrida funcional, conectividad segura (WireGuard), acceso endurecido (SSH), identidad centralizada (Keycloak), proxy inverso (Nginx) y monitorización operativa (Grafana/Loki/Prometheus), validando controles de seguridad mediante pruebas prácticas y dejando documentación y evidencias recopiladas.


Conclusión:
Solución coherente con Zero Trust, con conectividad segura, autenticación reforzada,
servicios protegidos, monitorización operativa y validaciones de seguridad.
**Evidencias:**  

![Profhub](https://github.com/JavierVericat-ITB2425/ProyectoFinal_JGB/blob/f3e63f69758781801340a9838a71eb9d3d88ec55/SPRINT%201%20/Acta%20Sprint%20Review%20%E2%80%94%20Sprint%201/images/Captura%20de%20pantalla%20de%202026-05-11%2019-45-20.png)

