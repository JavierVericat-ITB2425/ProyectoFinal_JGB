# Acta Sprint Review — Proyecto Final Zero Trust Hub

## 1) Datos de la sesión

Acta de cierre/review general del proyecto.

```text
Fecha: 5/11/2026
Participantes: Bryan Aguilera, Javier Vericat, Giuseppe Suarez
Sprint: Review general del proyecto final
```

## 2) Objetivo del sprint / cierre del proyecto

El objetivo del proyecto es diseñar, desplegar y validar una infraestructura híbrida basada en Zero Trust: nodo local + nodo cloud (AWS) conectados por WireGuard, identidad con Keycloak, proxy inverso con Nginx, monitorización con Grafana/Loki/Prometheus y pruebas de seguridad.

```text
Infraestructura híbrida Zero Trust:
- WireGuard (túnel)
- Keycloak (identidad)
- Nginx (proxy inverso)
- Grafana + Loki + Prometheus (monitorización)
- Pruebas de seguridad (escaneos, bruteforce, validación cifrado)
```

## 3) Trabajo realizado (verificaciones técnicas)

Se desplegó nodo local (Isard) y nodo cloud (AWS), se configuró WireGuard y se validó la comunicación. Se endureció SSH moviéndolo al puerto 2222.

```bash
# Comprobar WireGuard e interfaz
sudo wg && ip a show wg0

# Validar conectividad por el túnel
ping <IP_PEER_WG>

# Verificar SSH en puerto 2222
sudo ss -lntp | grep 2222
ssh -p 2222 <usuario>@<ip_nodo>
```

Sin captura disponible en esta ruta.

## 4) Servicios desplegados (Keycloak, Nginx, monitorización)

Se desplegó Keycloak (realm/usuarios/roles/políticas y protección anti-bruteforce), Nginx como proxy inverso y el stack de monitorización (Grafana/Loki/Prometheus) con dashboards y alertas.

```bash
# Ver contenedores/servicios activos (ejemplo)
docker ps

# Ver puertos publicados (superficie de exposición)
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

Sin captura disponible en esta ruta.

## 5) Pruebas de seguridad realizadas

Se realizaron escaneos de puertos (nodo local y nodo cloud), validación del cifrado del túnel WireGuard, pruebas de fuerza bruta sobre SSH y validación del bloqueo temporal de usuarios en Keycloak ante múltiples intentos fallidos.

```bash
# Escaneo de puertos (ejemplo)
sudo nmap -Pn -sV <IP_PUBLICA_NODO_LOCAL>
sudo nmap -Pn -sV <IP_PUBLICA_NODO_AWS>

# Fuerza bruta SSH (si estuviera habilitado password auth)
hydra -l <usuario> -P passwords_test.txt ssh://<ip>:2222 -V
```

Sin captura disponible en esta ruta.

## 6) Incidencias y dificultades

Se reportan incidencias relevantes:
- Expiración de la clase/recursos de AWS y pérdida del nodo cloud, obligando a redeploy y reconfiguración.
- Caídas de accesibilidad del nodo cloud y reconstrucción parcial del trabajo.
- Dificultades de integración de métricas/alertas en Grafana entre nodo local y AWS.
- Problemas de Nginx/WireGuard por cambio de IP pública del nodo local (requiere actualizar endpoint del peer).
- Problemas de estabilidad de acceso SSH por cambios de configuración (puerto/servicio).

```text
Checklist de recuperación rápida (resumen):
- Validar IP pública actual del nodo local.
- Actualizar endpoint del peer WireGuard.
- Revalidar túnel (wg/ping).
- Revalidar acceso SSH (puerto 2222, servicio activo).
- Revalidar dashboards/alertas (fuentes de datos en Grafana).
```

## 7) Resultado final y valoración

El proyecto se completa satisfactoriamente con una infraestructura híbrida funcional, conectividad segura (WireGuard), acceso endurecido (SSH), identidad centralizada (Keycloak), proxy inverso (Nginx) y monitorización operativa (Grafana/Loki/Prometheus), validando controles de seguridad mediante pruebas prácticas y dejando documentación y evidencias recopiladas.

```text
Conclusión:
Solución coherente con Zero Trust, con conectividad segura, autenticación reforzada,
servicios protegidos, monitorización operativa y validaciones de seguridad.
```

