# Pruebas de seguridad contra los nodos

## 1) Comprobar IP pública/privada del nodo local

Se comprueba la IP pública del nodo local, y se identifica también su IP privada en la red interna.

```bash
curl ifconfig.me
```

![IP pública del nodo local](images/image7.png)

## 2) Comprobar IP pública del nodo cloud (AWS)

Se realiza la misma comprobación de IP pública en el nodo cloud.

```bash
curl ifconfig.me
```

![IP pública del nodo cloud](images/image2.png)

## 3) Escaneo de puertos al nodo local

Se revisa la exposición de servicios del nodo local. Inicialmente, algunos servicios de contenedores estaban publicados en `0.0.0.0` (accesibles desde fuera). Para reducir exposición, se modificaron los `docker-compose.yml` para bindearlos a `127.0.0.1` (solo accesibles desde el propio nodo), manteniendo expuestos únicamente los puertos necesarios.

```bash
sudo nmap -Pn -sV 79.153.202.41
```

![Resultado del escaneo externo al nodo local](images/image3.png)

Se valida que desde el exterior queden abiertos únicamente los puertos necesarios (p. ej. 80/443 para Nginx y 2222 para SSH), y que los puertos de servicios internos (Grafana/Keycloak/etc.) aparezcan cerrados.

![Validación de restricción de servicios internos](images/image9.png)

## 4) Escaneo de puertos al nodo cloud (AWS)

Se escanean los servicios expuestos por el nodo cloud desde Internet para confirmar que solo queda accesible el puerto requerido para administración remota.

```bash
sudo nmap -Pn -sV 52.87.11.36
```

![Resultado del escaneo externo al nodo cloud](images/image12.png)

## 5) Bruteforce SSH con Hydra hacia los nodos (password auth deshabilitado)

Se intenta un ataque de fuerza bruta SSH desde Kali contra el nodo local. Si SSH tiene autenticación por contraseña deshabilitada, Hydra no puede ejecutar el ataque.

```bash
hydra -l isard -P passwords_test.txt ssh://192.168.18.10:2222 -V
```

![Hydra indicando que no hay autenticación por contraseña](images/image10.png)

## 6) Validar fail2ban habilitando temporalmente password auth y forzando intentos fallidos

Se desactiva temporalmente la restricción en SSH para permitir autenticación por contraseña, se reinicia el servicio y se crea un usuario de prueba para validar que fail2ban banea tras múltiples intentos fallidos.

```bash
sudo nano /etc/ssh/sshd_config
sudo systemctl restart ssh
sudo adduser testssh
```

![Cambios en SSH y creación de usuario de pruebas](images/image6.png)

Se lanza el ataque de fuerza bruta desde Kali usando un diccionario.

```bash
hydra -l testssh -P passwords_test.txt ssh://192.168.18.10:2222 -V
```

![Ataque de fuerza bruta con Hydra](images/image11.png)

Se comprueba que fail2ban banea la IP atacante y se revisan logs para ver intentos fallidos y el baneo.

```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
sudo tail -n 200 /var/log/auth.log
```

![Baneo confirmado en fail2ban y logs de intentos fallidos](images/image8.png)

## 7) Bruteforce web contra Keycloak

Se comprueba que el contenedor de Keycloak está levantado y funcionando.

```bash
docker ps
```

![Contenedor Keycloak en ejecución](images/image15.png)

Se usa un usuario de pruebas para ejecutar el ataque.

```text
Usuario de pruebas para login en Keycloak
```

![Usuario de pruebas](images/image14.png)

Se valida acceso a la web de Keycloak desde Kali.

```text
Abrir Keycloak desde Kali (URL del entorno de pruebas)
```

![Acceso web a Keycloak desde Kali](images/image1.png)

Se lanza fuerza bruta contra el login web. Se observa que puede producirse un falso positivo, pero Keycloak termina bloqueando temporalmente al usuario ante múltiples intentos fallidos.

```bash
hydra -l <usuario> -P passwords_test.txt <host> http-post-form "<ruta_login>:username=^USER^&password=^PASS^:<condición_fallo>" -V
```

![Hydra contra Keycloak y bloqueo temporal](images/image5.png)

Se revisan eventos de Keycloak y se confirma el motivo de detección por fuerza bruta y el bloqueo temporal del usuario.

```text
Keycloak Admin Console → Events (ver motivo brute_force_attack_detected y user_temporarily_disabled)
```

![Eventos de Keycloak mostrando bloqueo](images/image13.png)

![Detalle del evento y validación de la defensa anti-bruteforce](images/image4.png)

