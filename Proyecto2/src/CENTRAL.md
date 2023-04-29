## SEDE CENTRAL

### 1. C1

```bash
enable
configure terminal
host C1

!configuramos el enlace hacia SW4
interface e0/0
no shutdown
ip address 192.177.94.1 255.255.255.0

!SERIAL
interface s1/0
no shutdown
ip address 12.0.0.2 255.255.255.252

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 12.0.0.1
ip route 10.0.0.4 255.255.255.252 12.0.0.1
ip route 10.0.0.8 255.255.255.252 12.0.0.1
ip route 11.0.0.0 255.255.255.252 12.0.0.1
ip route 11.0.0.4 255.255.255.252 12.0.0.1
ip route 12.0.0.4 255.255.255.252 12.0.0.1
ip route 192.167.94.0 255.255.255.0 12.0.0.1
ip route 192.168.94.0 255.255.255.0 12.0.0.1
ip route 192.178.94.0 255.255.255.0 192.177.94.4
do write
```

### 2. C2

```bash
enable
configure terminal
host J2

!configuramos el primer enlace entre SW5
interface e0/0
no shutdown
ip address 192.177.94.2 255.255.255.0

!SERIAL
interface s1/0
no shutdown
ip address 12.0.0.6 255.255.255.252

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 12.0.0.5
ip route 10.0.0.4 255.255.255.252 12.0.0.5
ip route 10.0.0.8 255.255.255.252 12.0.0.5
ip route 11.0.0.0 255.255.255.252 12.0.0.5
ip route 11.0.0.4 255.255.255.252 12.0.0.5
ip route 12.0.0.0 255.255.255.252 12.0.0.5
ip route 192.167.94.0 255.255.255.0 12.0.0.5
ip route 192.168.94.0 255.255.255.0 12.0.0.5
ip route 192.178.94.0 255.255.255.0 192.177.94.4

do write
```

### 3. C3

```bash
enable
configure terminal
host C3

!configuramos el primer enlace entre C1 y C2

interface Ethernet 0/0
no shutdown

interface Ethernet 0/0.14
no shutdown
encapsulation dot1q 14
ip address 192.178.94.97 255.255.255.240

interface Ethernet 0/0.24
no shutdown
encapsulation dot1q 24
ip address 192.178.94.113 255.255.255.240

interface Ethernet 0/0.34
no shutdown
encapsulation dot1q 34
ip address 192.178.94.1 255.255.255.192

interface Ethernet 0/0.44
no shutdown
encapsulation dot1q 44
ip address 192.178.94.65 255.255.255.224

interface e0/1
ip address 192.177.94.4 255.255.255.0
no shutdown

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 192.177.94.2
ip route 10.0.0.0 255.255.255.252 192.177.94.1
ip route 10.0.0.4 255.255.255.252 192.177.94.2
ip route 10.0.0.4 255.255.255.252 192.177.94.1
ip route 10.0.0.8 255.255.255.252 192.177.94.2
ip route 10.0.0.8 255.255.255.252 192.177.94.1
ip route 11.0.0.0 255.255.255.252 192.177.94.2
ip route 11.0.0.0 255.255.255.252 192.177.94.1
ip route 11.0.0.4 255.255.255.252 192.177.94.2
ip route 11.0.0.4 255.255.255.252 192.177.94.1
ip route 12.0.0.0 255.255.255.252 192.177.94.2
ip route 12.0.0.0 255.255.255.252 192.177.94.1
ip route 12.0.0.4 255.255.255.252 192.177.94.2
ip route 12.0.0.4 255.255.255.252 192.177.94.1
ip route 192.167.94.0 255.255.255.0 192.177.94.2
ip route 192.167.94.0 255.255.255.0 192.177.94.1
ip route 192.168.94.0 255.255.255.0 192.177.94.2
ip route 192.168.94.0 255.255.255.0 192.177.94.1
do write
```

### 4. SW

```bash
enable
configure terminal
host SW9

vlan 14
name RRHH
vlan 24
name CONTA
vlan 34
name VENTAS
vlan 44
name INFORMATICA
exit

interface ethernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk

!  --------- Configuracion de MODO ACCESO (VLANS)
interface ethernet 1/0
switchport mode acces
switchport acces vlan 24

interface ethernet 0/3
switchport mode acces
switchport acces vlan 44

interface ethernet 0/2
switchport mode acces
switchport acces vlan 34

interface ethernet 0/1
switchport mode acces
switchport acces vlan 14


do write
```

### 5. VPC22 CONTA

```bash
ip 192.178.94.114/28 192.178.94.113
save
```

### 6. VPC19 VENTA

```bash
ip 192.178.94.2/26 192.178.94.1
save
```

### 7. VPC20 INFORMATICA

```bash
ip 192.178.94.66/27 192.178.94.65
save
```

### 8. VPC21 RRHH

```bash
ip 192.178.94.98/28 192.178.94.97
save
```
