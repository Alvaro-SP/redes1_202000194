<p>UNIVERSIDAD DE SAN CARLOS DE GUATEMALA</p>
<p>FACULTAD DE INGENIERIA</p>
<p>ESCUELA DE CIENCIAS Y SISTEMAS</p>
<p>REDES DE COMPUTADORAS 1</p>
<p>PRIMER SEMESTRE 2023</p>

---

---

---

---

<div align="center">

<a href="https://git.io/typing-svg" ><img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&size=35&pause=1000&color=2BF715&width=435&lines=PROYECTO+%232" alt="Typing SVG" /></a>

</div>

---

---

---

---

<div align="center">

| Nombre                      |  Carnet   |
| --------------------------- | :-------: |
| Alvaro Emmanuel Socop Pérez | 202000194 |

</div>

---

---

---

# <a name="nothing"></a>MANUAL TECNICO

> “Programa desarrollado en PNETLAB.”

## <a name="intro" ></a>ÍNDICE

| Topico                            | Link          |
| --------------------------------- | ------------- |
| Introducción                      | [Ir](#intro)  |
| Información del sistema           | [Ir](#inf)    |
| Capturas de pantalla de Wireshark | [Ir](#ob)     |
| Configuracion de VPC's            | [Ir](#tech)   |
| Interfaz del programa             | [Ir](#inter)  |
| Conclusiones                      | [Ir](#Conclu) |

## <a name="intro" ></a>INTRODUCCIÓN

En el mundo actual, la conectividad es fundamental para el éxito de cualquier empresa. La capacidad de interconectar distintas sedes y departamentos de forma eficiente y segura se ha convertido en una prioridad para muchas organizaciones. En este contexto, se nos ha encomendado el proyecto de interconectar la sede de Jutiapa de una reconocida empresa de venta de línea blanca y electrodomésticos con su central en la ciudad capital. Para lograrlo, deberemos poner en práctica todo el conocimiento adquirido en proyectos anteriores y utilizar herramientas como PNETLab y Wireshark. En particular, tendremos que diseñar una topología de red que incluya la creación de VLANs, el enrutamiento entre ellas, el uso de VLSM y FLSM, así como la configuración de protocolos de enrutamiento estático. Nuestra meta es demostrar la capacidad de implementar una solución de red eficiente y segura que satisfaga las necesidades de nuestro cliente.

Para la red de la sede de Jutiapa, deberemos asignar direcciones de red usando VLSM y configurar interfaces virtuales para la puerta de acceso predeterminada de cada VLAN en el ESW1. También se nos solicita el uso de VTP para la propagación de las VLAN y de RPVST para la prevención de bucles de red. Además, deberemos asignar IPs a las VPCS para permitir la comunicación dentro de cada VLAN y establecer una redundancia de primer salto utilizando HSRP con IPs virtuales para los routers J1 y J2.

Por otro lado, en el Core de la red se utilizará FLSM para la asignación de direcciones de red y se empleará el protocolo de enrutamiento estático para la comunicación entre dispositivos.

# 1. Resumen de direcciones IP y VLAN

- donde se justifique la elección de máscara de subred empleada para los distintos requerimientos.

# 2. Capturas de la implementación de las topologías.

## Sede Jutiapa

![Captura de pantalla principal](assets/sedejutiapa.png)

## Core

![Captura de pantalla principal](assets/core.png)

## Sede Central

![Captura de pantalla principal](assets/sedecentral.png)

## Completo

![Captura de pantalla principal](assets/DASH.png)

# 3. Detalle de los comandos usados

# 1 Configuraciónes de: routers, switches y VPCs

## R1

[ir a la configuracion de R1](#7-R1)

## R2

[ir a la configuracion de R2](#5-R2)

## R5

[ir a la configuracion de R5](#9-R5)

## SW7

[ir a la configuracion de SW7](#4-SW7)

## VPC11

[ir a la configuracion de VPC11](#1-VPC11)

# 2 Resumen de los comandos usados:

### Creación de ruta estática

Basicamente lo que se hace es tomar todas las rutas a las que puede llegar los paquetes y usar :

`ip route [destino] [mascara] [salida]`

lo cual permite que los paquetes que lleguen a la red destino puedan ser enviados a la salida que se le indique.

```
!configuramos las rutas estáticas para los paquetes que lleguen a la red destino

ip route 10.0.0.0 255.255.255.252 142.168.2.2

ip route 142.178.1.0 255.255.255.248 142.168.2.2
ip route 142.178.2.0 255.255.255.248 142.168.2.2
ip route 142.178.0.0 255.255.255.0 142.168.2.2

ip route 142.168.1.0 255.255.255.248 142.168.2.2
ip route 142.168.2.0 255.255.255.248 142.168.2.2
```

### creación de PortChannel con PAGP y LACP

- PAGP:

para esta configuracion un switch esta en modo automatico y el otro en modo desirable, y se usa el canal de grupo 1 para la comunicacion.

```
!  --------- Configuracion

interface Port-channel 1
description conexion a SW7

interface range e0/0-1
channel-group 1 mode desirable
no shutdown
```

```
interface range e0/2-3
channel-group 1 mode auto
no shutdown
```

- LACP:

basicamente para esta configuracion se elige un switch en modo activo y el otro en modo pasivo y se usa el canal de grupo 2 para la comunicacion.

```
interface Port-channel 2
description conexion a SW10 con LACP
exit
interface range e0/2-3
channel-group 2 mode active
end
```

```
interface Port-channel 2
description conexion a SW9 con LACP
exit
interface range e0/1-0
channel-group 2 mode passive
```

### creación de IP virtual con HSRP y GLBP

- HSRP:

HSRP permite a un grupo de routers compartir la misma dirección IP virtual como puerta de enlace predeterminada para los dispositivos en la red, proporcionando redundancia y aumentando la disponibilidad de la red. Los routers en el grupo HSRP se comunican entre sí para determinar el router activo, que es responsable de reenviar el tráfico de red hacia la dirección IP virtual. Si el router activo falla, uno de los routers en espera del grupo HSRP asume el papel de router activo automáticamente.

```

!usamos la version 2 de HSRP
standby version 2

!definimos su id de grupo HSRP y la dirección ip virtual del gateway
standby 21 ip  142.168.0.1

!también le definimos su prioridad
standby 21 priority 109

!configuramos el preempt, que sirve para que recupere la prioridad una se recupere la comunicación
standby 21 preempt
```

```
standby version 2
standby 21 ip 142.168.0.1
```

- GLBP:

Al igual que HSRP, GLBP permite a un grupo de routers compartir la misma dirección IP virtual como puerta de enlace predeterminada para los dispositivos en la red, proporcionando redundancia y aumentando la disponibilidad de la red.

```
! CONFIGURACION DE GLBP
glbp 7 ip 142.178.0.1
glbp 7 preempt
glbp 7 priority 150
glbp 7 load-balancing round-robin

```

```
glbp 7 ip 142.178.0.1
glbp 7 load-balancing round-robin
```

# 3 Comandos empleados para la verificación .

correcto funcionamiento de los protocolos empleados para la realización de la práctica.

- Verificando la configuración de LACP SW9 y SW10

```
show etherchannel summary
show lacp neighbor
```

- Verificando la configuración de PAGP SW7 y SW8

```
show etherchannel summary
show pagp neighbor
```

- Verificando la configuración de HSRP SW7 y SW8

```c
show standby brief
```

- Verificando la configuración de GLBP SW9 y SW10

```c
show glbp brief
```

- verificando la configuración de las rutas de interfaces

```c
show ip route
show running-config | section ip route
```

# Conclusiones

El objetivo de esta prueba fue demostrar la capacidad de PnetLab para simular una red de área local (LAN) y permitir la configuración de las VPCs y la comunicación entre ellas. Se logró demostrar que las VPCs podían comunicarse entre sí

Después de realizar la simulación de la topología de red para la Academia Técnica de Formación Empresarial - TECAP, se puede concluir que la implementación de una red redundante es esencial para garantizar la continuidad de la comunicación entre los dos sitios, y también para aumentar la disponibilidad de los servicios de red. Además, se recomienda utilizar una arquitectura de red jerárquica que permita la segmentación de la red en varias subredes y facilite la gestión de la red en su conjunto. También se debe tener en cuenta la seguridad de la red, implementando medidas de protección adecuadas, como firewalls y autenticación de usuarios. En general, una buena planificación de la topología de red es crucial para garantizar una comunicación eficiente y segura entre los sitios y para la satisfacción de los usuarios de la red.

utilizando PnetLab. Como resultado, se concluye que PnetLab es una herramienta útil para la configuración y prueba de redes de área local.

# Anexos

### CONFIGURACIONES DE LOS HOSTS

## SEDE CENTRAL

### 1. C1

```bash

```

### 2. C2

### 3. C3

### 4. SW

### 5. VPC22

### 6. VPC19

### 7. VPC20

### 8. VPC21

## CORE

### 9. CENTRAL

### 10. ESCUINTLA

### 11. JUTIAPA

## SEDE JUTIAPA

### 12. J1

### 13. J2

### 14. ESW1

### 15. SW2

### 16. SW3

### 17. VPC14

### 18. VPC8

### 19. VPC6

### 20. VPC7

### 21. VPC5

### 22. VPC15

### 1. VPC11

```C
ip 142.168.0.4/24 142.168.0.1
save
```

[regresar](#1-configuraciónes-de-routers-switches-y-vpcs)

### 2. VPC12

```C
ip 142.178.0.4 142.178.0.1
save
```

## PORT CHANNEL

### 3. SW8

```bash
enable
configure terminal
host SW8

!  --------- Configuracion

interface Port-channel 1
description conexion a SW7

interface range e0/0-1
channel-group 1 mode desirable
no shutdown
do write
```

### 4. SW7

```bash
enable
configure terminal
host SW7

!  --------- Configuracion

interface range e0/2-3
channel-group 1 mode auto
no shutdown


do write
```

[regresar](#1-configuraciónes-de-routers-switches-y-vpcs)

### 5. R2

```bash
enable
configure terminal
host R2

!configuramos el primer enlace entre routers
interface e0/0
ip address 142.168.1.1 255.255.255.248
no shutdown

!configuramos la primera interfaz
interface e0/1
ip address 142.168.0.2 255.255.255.0
no shutdown

!usamos la version 2 de HSRP
standby version 2

!definimos su id de grupo HSRP y la dirección ip virtual del gateway
standby 21 ip  142.168.0.1

!también le definimos su prioridad
standby 21 priority 109

!configuramos el preempt, que sirve para que recupere la prioridad una se recupere la comunicación
standby 21 preempt

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 142.168.1.2

ip route 142.178.1.0 255.255.255.248 142.168.1.2
ip route 142.178.2.0 255.255.255.248 142.168.1.2
ip route 142.178.0.0 255.255.255.0 142.168.1.2

ip route 142.168.1.0 255.255.255.248 142.168.1.2
ip route 142.168.2.0 255.255.255.248 142.168.1.2

do write
```

[regresar](#1-configuraciónes-de-routers-switches-y-vpcs)

### 6. R3

```bash
enable
configure terminal
host R3

!configuramos el primer enlace entre routers
interface e0/0
ip address 142.168.2.1 255.255.255.248
no shutdown

!configuramos la primera interfaz
interface e0/1
ip address 142.168.0.3 255.255.255.0
no shutdown


standby version 2
standby 21 ip 142.168.0.1

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 142.168.2.2

ip route 142.178.1.0 255.255.255.248 142.168.2.2
ip route 142.178.2.0 255.255.255.248 142.168.2.2
ip route 142.178.0.0 255.255.255.0 142.168.2.2

ip route 142.168.1.0 255.255.255.248 142.168.2.2
ip route 142.168.2.0 255.255.255.248 142.168.2.2

do write
```

### 7. R1

```bash
enable
configure terminal
host R1

!configuramos ROUTERS
interface e0/0
ip address 142.168.1.2 255.255.255.248
no shutdown

interface e0/1
ip address 142.168.2.2 255.255.255.248
no shutdown

!configuracion interfaz serial
interface s1/0
ip address 10.0.0.1 255.255.255.252
no shutdown
exit
!configuramos las rutas estaticas
ip route 10.0.0.0 255.255.255.252 10.0.0.2
ip route 142.178.1.0 255.255.255.248 10.0.0.2
ip route 142.178.2.0 255.255.255.248 10.0.0.2
ip route 142.178.0.0 255.255.255.0 10.0.0.2
ip route 142.168.1.0 255.255.255.248 142.168.1.1
ip route 142.168.2.0 255.255.255.248 142.168.2.1
ip route 142.168.0.0 255.255.255.0 142.168.1.1

do write
```

[regresar](#1-configuraciónes-de-routers-switches-y-vpcs)

### 8. R4

```bash
enable
configure terminal
host R4

!configuramos ROUTERS
interface e0/0
ip address 142.178.1.1 255.255.255.248
no shutdown

interface e0/1
ip address 142.178.2.1 255.255.255.248
no shutdown

!configuracion interfaz serial****************************
interface s1/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 10.0.0.1

ip route 142.178.1.0 255.255.255.248 142.178.1.2
ip route 142.178.2.0 255.255.255.248 142.178.2.2
ip route 142.178.0.0 255.255.255.0 142.178.1.2

ip route 142.168.1.0 255.255.255.248 10.0.0.1
ip route 142.168.2.0 255.255.255.248 10.0.0.1
ip route 142.168.0.0 255.255.255.0 10.0.0.1

do write
```

### 9. R5

```bash
enable
configure terminal
host R5

!configuramos ROUTERS
interface e0/0
ip address 142.178.1.2 255.255.255.248
no shutdown

interface e0/1
ip address 142.178.0.2 255.255.255.0
no shutdown

! CONFIGURACION DE GLBP
glbp 7 ip 142.178.0.1
glbp 7 preempt
glbp 7 priority 150
glbp 7 load-balancing round-robin

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 142.178.1.1

ip route 142.178.1.0 255.255.255.248 142.178.1.1
ip route 142.178.2.0 255.255.255.248 142.178.1.1

ip route 142.168.0.0 255.255.255.0 142.178.1.1
ip route 142.168.1.0 255.255.255.248 142.178.1.1
ip route 142.168.2.0 255.255.255.248 142.178.1.1

do write
```

[regresar](#1-configuraciónes-de-routers-switches-y-vpcs)

### 10. R6

```bash
enable
configure terminal
host R6

!configuramos ROUTERS
interface e0/0
ip address 142.178.2.2 255.255.255.248
no shutdown

interface e0/1
ip address 142.178.0.3 255.255.255.0
no shutdown


glbp 7 ip 142.178.0.1
glbp 7 load-balancing round-robin

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 142.178.2.1

ip route 142.178.1.0 255.255.255.248 142.178.2.1
ip route 142.178.2.0 255.255.255.248 142.178.2.1

ip route 142.168.0.0 255.255.255.0 142.178.2.1
ip route 142.168.1.0 255.255.255.248 142.178.2.1
ip route 142.168.2.0 255.255.255.248 142.178.2.1

do write
```

### 12. SW9

```bash
enable
configure terminal
host SW9

!  --------- Configuracion

interface Port-channel 2
description conexion a SW10 con LACP
exit
interface range e0/2-3
channel-group 2 mode active

do write
```

### 13. SW10

```bash
enable
configure terminal
host SW10
!  --------- Configuracion
interface Port-channel 2
description conexion a SW9 con LACP
exit
interface range e0/0-1
channel-group 2 mode passive
do write
```
