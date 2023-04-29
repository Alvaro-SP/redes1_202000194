## SEDE JUTIAPA

### 12. J1

```bash
enable
configure terminal
host J1

! ---------SERIAL
interface s1/0
ip address 11.0.0.6 255.255.255.252
no shutdown

!configuramos el primer enlace entre SW5 y J1
interface e0/0
ip address 192.167.94.2 255.255.255.0
no shutdown

! ---------HSRP usamos la version 2 de
standby version 2

!definimos su id de grupo HSRP y la dirección ip virtual del gateway
standby 21 ip  192.167.94.1

!también le definimos su prioridad
standby 21 priority 109

!configuramos el preempt, que sirve para que recupere la prioridad una se recupere la comunicación
standby 21 preempt

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 11.0.0.5
ip route 10.0.0.4 255.255.255.252 11.0.0.5
ip route 10.0.0.8 255.255.255.252 11.0.0.5
ip route 11.0.0.4 255.255.255.252 11.0.0.5
ip route 12.0.0.0 255.255.255.252 11.0.0.5
ip route 12.0.0.4 255.255.255.252 11.0.0.5
ip route 192.168.94.0 255.255.255.0 192.167.94.4
ip route 192.177.94.0 255.255.255.0 11.0.0.5
ip route 192.178.94.0 255.255.255.0 11.0.0.5

do write
```

### 13. J2

```bash
enable
configure terminal
host J2

!SERIAL
interface s1/1
ip address 11.0.0.2 255.255.255.252
no shutdown

!configuramos el primer enlace entre SW5
interface e0/0
ip address 192.167.94.3 255.255.255.0
no shutdown

standby version 2
standby 21 ip 192.167.94.1

exit
!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 11.0.0.1
ip route 10.0.0.4 255.255.255.252 11.0.0.1
ip route 10.0.0.8 255.255.255.252 11.0.0.1
ip route 11.0.0.0 255.255.255.252 11.0.0.1
ip route 12.0.0.0 255.255.255.252 11.0.0.1
ip route 12.0.0.4 255.255.255.252 11.0.0.1
ip route 192.168.94.0 255.255.255.0 192.167.94.4
ip route 192.177.94.0 255.255.255.0 11.0.0.1
ip route 192.178.94.0 255.255.255.0 11.0.0.1

do write
```

### 14. ESW1

```bash
enable
configure terminal
host ESW1
! --------- Configuracion de VLAN porque es VTP RAIZ
vlan 14
name RRHH
vlan 24
name CONTA
vlan 34
name VENTAS
vlan 44
name INFORMATICA

!  --------- Configuracion de IP
interface e0/0
switchport trunk encapsulation dot1q
switchport mode trunk

interface e0/1
switchport trunk encapsulation dot1q
switchport mode trunk

interface e0/2
no switchport
no shutdown
ip address 192.167.94.4 255.255.255.0

! --------- Configuramos interfaces virtuales para puerta de cada VLAN
interface vlan 14
no shutdown
ip address 192.168.94.49 255.255.255.240

interface vlan 24
no shutdown
ip address 192.168.94.65 255.255.255.248

interface vlan 34
no shutdown
ip address 192.168.94.1 255.255.255.224

interface vlan 44
no shutdown
ip address 192.168.94.33 255.255.255.240

exit
!  --------- Configuracion de VTP SERVER
vtp version 2
vtp mode server
vtp domain 202000194
vtp password usac

!  --------- Configuracion de STP SWITCH RAIZ RPVST
spanning-tree mode rapid-pvst

!configuramos las rutas estáticas
ip route 10.0.0.0 255.255.255.252 192.167.94.2
ip route 10.0.0.0 255.255.255.252 192.167.94.3
ip route 10.0.0.4 255.255.255.252 192.167.94.2
ip route 10.0.0.4 255.255.255.252 192.167.94.3
ip route 10.0.0.8 255.255.255.252 192.167.94.2
ip route 10.0.0.8 255.255.255.252 192.167.94.3
ip route 11.0.0.0 255.255.255.252 192.167.94.2
ip route 11.0.0.0 255.255.255.252 192.167.94.3
ip route 11.0.0.4 255.255.255.252 192.167.94.2
ip route 11.0.0.4 255.255.255.252 192.167.94.3
ip route 12.0.0.0 255.255.255.252 192.167.94.2
ip route 12.0.0.0 255.255.255.252 192.167.94.3
ip route 12.0.0.4 255.255.255.252 192.167.94.2
ip route 12.0.0.4 255.255.255.252 192.167.94.3
ip route 192.177.94.0 255.255.255.0 192.167.94.2
ip route 192.177.94.0 255.255.255.0 192.167.94.3
ip route 192.178.94.0 255.255.255.0 192.167.94.2
ip route 192.178.94.0 255.255.255.0 192.167.94.3
!************ VALICAR SI LA 178 NECESITA ROUTEARSE
do write
```

### 15. SW2

```bash
enable
configure terminal
host SW2

! --------- Configuracion de VTP CLIENTE
vtp mode client
vtp domain 202000194
vtp password usac

!  --------- Configuracion de MODO ACCESO (VLANS)
interface ethernet 0/1
switchport mode acces
switchport acces vlan 14

interface ethernet 0/2
switchport mode acces
switchport acces vlan 44

interface ethernet 0/3
switchport mode acces
switchport acces vlan 34

!  --------- Configuracion DE MODO TRONCAL
interface ethernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk

interface ethernet 1/0
switchport trunk encapsulation dot1q
switchport mode trunk
exit

do write
```

### 16. SW3

```bash
enable
configure terminal
host SW3

! --------- Configuracion de VTP CLIENTE
vtp mode client
vtp domain 202000194
vtp password usac

!  --------- Configuracion de MODO ACCESO (VLANS)
interface ethernet 0/1
switchport mode acces
switchport acces vlan 34

interface ethernet 0/2
switchport mode acces
switchport acces vlan 24

interface ethernet 0/3
switchport mode acces
switchport acces vlan 24

!  --------- Configuracion DE MODO TRONCAL
interface ethernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk

interface ethernet 1/0
switchport trunk encapsulation dot1q
switchport mode trunk
exit

do write
```

### 17. VPC14

```C
ip 192.168.94.2/27 192.168.94.1
save
```

### 20. VPC7

```C
ip 192.168.94.3/27 192.168.94.1
save
```

### 19. VPC6

```C
ip 192.168.94.34/28 192.168.94.33
save
```

### 18. VPC8

```C
ip 192.168.94.50/28 192.168.94.49
save
```

### 21. VPC5

```C
ip 192.168.94.66/29 192.168.94.65
save
```

### 22. VPC15

```C
ip 192.168.94.67/29 192.168.94.65
save
```
