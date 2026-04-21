# Creación del entorno en AWS ZTH-Node-Cloud

## Índice
- [Añadimos reglas al Security Group](#añadimos-reglas-al-security-group)
- [Creación de la instancia](#creación-de-la-instancia)
- [Acceso al nodo de AWS mediante la terminal](#acceso-al-nodo-de-aws-mediante-la-terminal)
- [Configuración para dejar accesible para el nodo local](#configuración-para-dejar-accesible-para-el-nodo-local)

Creamos un Security Group inicial para crear la instancia:

![Security Group Inicial](images/image8.png)

## Añadimos reglas al Security Group

Añadimos una regla de entrada para acceder vía SSH desde solo la IP pública del instituto, además añadimos la máscara /32 ya que esta solo permite una sola IP:

![Regla SSH](images/image12.png)

Añadimos la regla de HTTPS para la web y que solo se pueda acceder desde la IP pública del ITB:

![Regla HTTPS](images/image14.png)

Por último creamos la regla para Wireguard VPN, mediante el puerto 51820 y la IP pública del nodo local (Nuvolet):

![Regla Wireguard](images/image3.png)

Creamos una regla para poder acceder al Keycloak:

![Regla Keycloak](images/image6.png)

## Creación de la instancia

Asignamos el nombre a la instancia de AWS:

![Nombre Instancia](images/image13.png)

Asignamos una imagen Ubuntu Server 24.04 LTS (o 24.01 según el doc), con el tipo de instancia "t2.medium" con 2 vCPU y 4 GiB Memory:

![Imagen y Tipo de Instancia](images/image15.png)

En la configuración de red añadimos el Security Group que creamos al inicio:

![Configuración de Red](images/image7.png)

Configuramos el almacenamiento de la instancia:

![Almacenamiento](images/image11.png)

Creamos una key para poder acceder a la instancia:

![Creación de Key](images/image4.png)

Creamos una IP estática para que la IP pública del nodo en AWS no cambie al reiniciar la máquina y la asociamos a la instancia que hemos creado:

![IP Estática](images/image16.png)

## Acceso al nodo de AWS mediante la terminal

Primero cambiamos los permisos de la key a los permisos adecuados con el comando:

```bash
chmod 400 Key-zth-node-cloud.pem
```

![Permisos Key](images/image9.png)

Accedemos mediante SSH con el comando:

```bash
ssh -i Key-zth-node-cloud.pem ubuntu@ec2-98-88-186-16.compute-1.amazonaws.com
```

![Acceso SSH](images/image5.png)

## Configuración para dejar accesible para el nodo local

Configuramos el acceso de SSH al puerto 2222 ya que el puerto por defecto es muy inseguro, además añadimos el sin login por contraseña, sin root login y activamos el acceso con clave:

```bash
sudo nano /etc/ssh/sshd_config
```

Dentro del archivo, modificamos o añadimos:

```text
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

![Configuración SSH 1](images/image1.png)
![Configuración SSH 2](images/image2.png)

Reiniciamos el servicio, miramos por donde se está escuchando el SSH y accedemos desde la terminal:

```bash
sudo systemctl stop ssh.socket
sudo systemctl disable ssh.socket
sudo systemctl mask ssh.socket
sudo systemctl daemon-reload
sudo systemctl restart ssh
sudo ss -tulpn | grep ssh
```

![Reinicio Servicio](images/image17.png)

Probamos acceder con el comando:

```bash
ssh -i Key-zth-node-cloud.pem -p 2222 ubuntu@34.231.236.201
```

![Prueba Final](images/image10.png)
