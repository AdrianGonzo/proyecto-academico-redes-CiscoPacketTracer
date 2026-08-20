# Diseño de Red — Caso de Estudio UCB

Proyecto académico desarrollado para la materia **Redes de Computadoras II (SIS-233)** de la Universidad Católica Boliviana "San Pablo".

El proyecto consiste en el diseño y configuración de una topología de red para la Universidad Católica Boliviana (UCB), comprendiendo una **sede central y una sucursal**. La implementación fue realizada mediante **Cisco Packet Tracer**.

## Descripción

La topología contempla una sede central con redundancia de routers mediante **HSRP**, además de **EtherChannel** para disponibilidad de las conexiones. En la sede central se encuentran un servidor HTTP con la página de la Universidad y un servidor DNS.

La sucursal cuenta con una infraestructura de switches con redundancia mediante **EtherChannel** y cuatro VLANs:

- DERECHO
- SISTEMAS
- CAJEROS
- GERENCIA

La comunicación entre los routers se realiza mediante **OSPF**, mientras que la salida a Internet se implementa mediante **NAT Sobrecargado** y una **Access List**.

## Objetivos

### Objetivo general

Aplicar los conocimientos en configuración de dispositivos y direccionamiento en la topología dada para la conexión correcta en toda la red.

### Objetivos específicos

- Configurar routers y switches con servicios y seguridad básica.
- Asignar direcciones IP, incluyendo gateway y DNS, a los dispositivos finales mediante DHCP.
- Crear subredes VLANs para dispositivos de la red de la sucursal.
- Utilizar protocolos de enrutamiento para la conexión entre routers e Internet con OSPF, NAT Sobrecargado y Access List.
- Implementar redundancia con HSRP y EtherChannel para la disponibilidad de la red.

## Topología de Red

La red está dividida en dos partes principales:

### Sede Central

La sede central cuenta con:

- Redundancia de routers mediante HSRP.
- Router de borde para la conexión a Internet.
- Conexión con la sucursal.
- Servidor HTTP.
- Servidor DNS.

### Sucursal

La sucursal cuenta con:

- Tres switches.
- Un servidor VTP.
- Redundancia mediante EtherChannel.
- Cuatro VLANs:
  - VLAN 10 — DERECHO
  - VLAN 20 — SISTEMAS
  - VLAN 30 — CAJEROS
  - VLAN 40 — GERENCIA

La sucursal se conecta con la sede central mediante el router **R_Sucursal**, que se comunica con el router **R_Borde**.

## Plan de Direccionamiento IP

La red utiliza como red base:

`192.168.0.0/16`

| Red | Dirección de red | Primera IP | Última IP | Broadcast | Máscara |
|---|---|---|---|---|---|
| VLAN 30 | 192.168.0.0/23 | 192.168.0.1 | 192.168.1.254 | 192.168.1.255 | 255.255.254.0 |
| VLAN 40 | 192.168.2.0/23 | 192.168.2.1 | 192.168.3.254 | 192.168.3.255 | 255.255.254.0 |
| VLAN 10 | 192.168.4.0/25 | 192.168.4.1 | 192.168.4.126 | 192.168.4.127 | 255.255.255.128 |
| VLAN 20 | 192.168.4.128/27 | 192.168.4.129 | 192.168.4.158 | 192.168.4.159 | 255.255.255.224 |
| CENTRAL | 192.168.4.160/27 | 192.168.4.161 | 192.168.4.190 | 192.168.4.191 | 255.255.255.224 |
| ENLACE 1 | 192.168.4.192/30 | 192.168.4.193 | 192.168.4.194 | 192.168.4.195 | 255.255.255.252 |
| ENLACE 2 | 192.168.4.196/30 | 192.168.4.197 | 192.168.4.198 | 192.168.4.199 | 255.255.255.252 |
| ENLACE 3 | 192.168.4.200/30 | 192.168.4.201 | 192.168.4.202 | 192.168.4.203 | 255.255.255.252 |
| ENLACE 4 | 192.168.4.204/30 | 192.168.4.205 | 192.168.4.206 | 192.168.4.207 | 255.255.255.252 |
| ENLACE ISP | 181.115.149.8/30 | 181.115.149.9 | 181.115.149.10 | 181.115.149.11 | 255.255.255.252 |

## VLANs

La sucursal está dividida en cuatro VLANs:

| VLAN | Nombre |
|---|---|
| 10 | DERECHO |
| 20 | SISTEMAS |
| 30 | CAJEROS |
| 40 | GERENCIA |

Las VLANs se encuentran distribuidas en los switches de la sucursal.

## Configuración de Red

El proyecto contempla la configuración de:

- Routers.
- Switches.
- Subinterfaces.
- Direccionamiento IP.
- DHCP.
- Enrutamiento dinámico mediante OSPF.
- HSRP.
- NAT Sobrecargado.
- Access Lists.
- EtherChannel.
- VLANs.

## Servicios

### DHCP

Se utiliza DHCP para la asignación de direcciones IP a los dispositivos finales, incluyendo gateway y DNS.

### DNS

Se configuró un servidor DNS para la resolución del nombre:

`www.ucb.bo`

### HTTP

Se configuró un servidor HTTP con la página de la Universidad.

## Seguridad

Se configuró seguridad básica en los dispositivos mediante contraseñas para:

- Enable.
- Línea de consola.
- Línea VTY.

También se configuró un banner de advertencia para el acceso a la terminal:

`SOLO PERSONAL AUTORIZADO`

Se utilizaron **Access Lists** para establecer condiciones de acceso entre las VLANs.

## Pruebas y Validación

Para comprobar el funcionamiento de la red se realizaron pruebas de:

### Conectividad

Se realizaron pruebas de ping entre dispositivos de diferentes redes, incluyendo:

- PC4 (VLAN 10 DERECHO) → PC0 (SEDE CENTRAL).
- PC9 (VLAN 20 SISTEMAS) → PC2 (SEDE CENTRAL).

### Resolución DNS

Se comprobó la resolución:

`192.168.4.162 → www.ucb.bo`

### Acceso HTTP

Se comprobó el acceso al sitio web desde una PC perteneciente a la **VLAN 30 CAJEROS**.

## Problemas Encontrados

Durante la implementación se presentaron diferentes dificultades.

### EtherChannel y HSRP

Se presentaron errores en la configuración de EtherChannel y HSRP que impedían su funcionamiento correcto. Se revisó la configuración de los dispositivos para identificar y corregir las fallas.

### NAT

Los dispositivos no podían conectarse al ISP. El problema se produjo debido a que en OSPF se utilizó `redistribute static` en lugar de `default-information originate`.

Se corrigió la configuración utilizando la opción correspondiente.

### DHCP

Los dispositivos no recibían direcciones IP mediante DHCP debido a errores en la configuración de `ip helper-address`.

Se revisó y corrigió la configuración correspondiente.

## Conclusiones

Mediante el desarrollo del proyecto se aplicaron conocimientos de configuración y seguridad básica en dispositivos de red, direccionamiento mediante DHCP y división de la red mediante VLANs.

También se implementó redundancia mediante **HSRP** en routers y **EtherChannel** en switches, además de enrutamiento dinámico mediante **OSPF**, NAT Sobrecargado para la conexión hacia el ISP y Access Lists para controlar las conexiones entre redes.

El proyecto permitió trabajar con la configuración de switches y routers, así como con mecanismos de redundancia, enrutamiento dinámico, NAT y control de acceso.

## Herramienta Utilizada

**Cisco Packet Tracer v8.2.2**

Ejercicio por Edson Peredo Mancilla

Resuelto por Adrian Gonzales Ferreira

Universidad Católica Boliviana "San Pablo"  
Redes de Computadoras II — SIS-233
