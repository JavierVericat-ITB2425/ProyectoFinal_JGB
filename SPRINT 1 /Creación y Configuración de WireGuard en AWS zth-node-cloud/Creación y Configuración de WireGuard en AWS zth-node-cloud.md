# Creación y Configuración de WireGuard en AWS zth-node-cloud

## Índice
- [Instalación de WireGuard](#instalación-de-wireguard)
- [Configuración de WireGuard](#configuración-de-wireguard)
- [Creación y Configuración del Túnel](#creación-y-configuración-del-túnel)
- [Puesta en marcha y Verificación](#puesta-en-marcha-y-verificación)
- [Pruebas de Rendimiento (iperf3)](#pruebas-de-rendimiento-iperf3)

## Instalación de WireGuard

Instalamos el paquete de WireGuard:

```bash
sudo apt install wireguard -y
```

![Instalación WireGuard](images/image4.png)

## Configuración de WireGuard

### Creamos claves:

```bash
wg genkey | sudo tee /etc/wireguard/privatekey | wg pubkey | sudo tee /etc/wireguard/publickey
sudo chmod 600 /etc/wireguard/privatekey
```

![Creación de Claves](images/image5.png)

Ahora miramos la clave pública y privada con un cat:

```bash
sudo cat /etc/wireguard/publickey
sudo cat /etc/wireguard/privatekey
```

![Ver Claves](images/image6.png)

## Creación y Configuración del Túnel

Creamos el archivo de configuración de WireGuard:

```bash
sudo nano /etc/wireguard/wg0.conf
```

Y añadimos lo siguiente dentro del archivo de configuración:

```text
[Interface]
Address = 10.8.0.2/24
PrivateKey = MPuujRpK/DxxrvQwWIou30G6H0dsm3l3XISjV+bHwkA=
ListenPort = 51820

[Peer]
PublicKey = T2LyzJoDVs8ie3+99UtJMvNvOedNepzkcSl4Xb5DFEg=
AllowedIPs = 10.8.0.1/32
Endpoint = 79.153.202.41:51820
PersistentKeepalive = 25
```

![Configuración wg0.conf](images/image2.png)

Primero añadimos los permisos correctos a los archivos para proteger los archivos sensibles de WireGuard:

```bash
sudo chmod 600 /etc/wireguard/privatekey && sudo chmod 600 /etc/wireguard/wg0.conf
```

![Permisos Archivos](images/image3.png)

## Puesta en marcha y Verificación

Levantamos la interfaz VPN:

```bash
sudo wg-quick up wg0
```

![Levantar Interfaz](images/image9.png)

Comprobamos que la interfaz está levantada y la IP asignada:

```bash
sudo wg && ip a show wg0
```

![Verificar Interfaz](images/image10.png)

Por último hacemos un ping al nodo local:

```bash
ping 10.8.0.1
```

![Ping al Nodo Local](images/image1.png)

Y desde el nodo local hacemos un ping al nodo en cloud:

![Ping desde Nodo Local](images/image7.png)

## Pruebas de Rendimiento (iperf3)

Los resultados de iperf3 confirman que el túnel no solo está levantado, sino que tiene un rendimiento sólido de unos 60 Mbps.

**En AWS (Cliente):**
```bash
iperf3 -c 10.8.0.1
```

**En Local (Servidor):**
```bash
iperf3 -s
```

![Resultados iperf3](images/image8.png)
