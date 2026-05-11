# Guía de Instalación y Configuración de WireGuard

Esta documentación detalla el proceso de instalación y configuración de WireGuard para establecer una Red Privada Virtual (VPN) punto a punto, permitiendo una comunicación segura entre un servidor local (Nodo A) y otros nodos (como AWS).

## Índice
1. [Instalación de WireGuard](#1-instalación-de-wireguard)
2. [Generación de Llaves](#2-generación-de-llaves)
3. [Configuración del Servidor (wg0.conf)](#3-configuración-del-servidor-wg0conf)
4. [Configuración del Peer (Nodo B)](#4-configuración-del-peer-nodo-b)
5. [Gestión del Servicio](#5-gestión-del-servicio)

---

## 1. Instalación de WireGuard

Para comenzar, instalamos las herramientas necesarias de WireGuard en el sistema:

```bash
sudo apt update
sudo apt install wireguard -y
```

![Instalación de WireGuard](images/image3.png)

## 2. Generación de Llaves

WireGuard utiliza criptografía de llave pública. Es necesario generar una llave privada y una pública para el servidor:

```bash
# Generar llave privada y pública
wg genkey | tee privatekey | wg pubkey > publickey
```

![Generación de llaves](images/image1.png)

## 3. Configuración del Servidor (wg0.conf)

El archivo de configuración principal se encuentra en `/etc/wireguard/wg0.conf`.

### Parámetros de Interfaz:
- **Address:** `10.8.0.1/24`. Define la IP privada del servidor dentro de la VPN.
- **ListenPort:** `51820`. Puerto UDP estándar para el tráficos de WireGuard.
- **PostUp / PostDown:** Utiliza comandos de `iptables` para habilitar el reenvío de tráficos (forwarding) y el enmascaramiento de red (MASQUERADE), permitiendo que el tráficos de la VPN salga hacia internet a través de la interfaz física (ej. `eth0` o `enp1s0`).

```ini
[Interface]
PrivateKey = <TU_LLAVE_PRIVADA_AQUÍ>
Address = 10.8.0.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = 1RR5wGRRVP75uxtv8AUCihM5WRcMX22viqUbOSHE2mc=
AllowedIPs = 10.8.0.2/32
Endpoint = 34.231.236.201:51820
PersistentKeepalive = 25
```

![Configuración del servidor](images/image2.png)


