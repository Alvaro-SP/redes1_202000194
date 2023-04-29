## CORE

### 9. CENTRAL

```bash
enable
configure terminal
host CENTRAL

!configuramos SERIALES
interface s1/0
ip address 10.0.0.5 255.255.255.252
no shutdown

interface s1/1
ip address 12.0.0.1 255.255.255.252
no shutdown

interface s1/2
ip address 12.0.0.5 255.255.255.252
no shutdown

interface s1/3
ip address 10.0.0.1 255.255.255.252
no shutdown

exit

!configuramos las rutas estáticas

ip route 10.0.0.0 255.255.255.252 10.0.0.2
ip route 10.0.0.0 255.255.255.252 10.0.0.6
ip route 11.0.0.0 255.255.255.252 10.0.0.2
ip route 11.0.0.0 255.255.255.252 10.0.0.6
ip route 11.0.0.4 255.255.255.252 10.0.0.2
ip route 11.0.0.4 255.255.255.252 10.0.0.6
ip route 192.167.94.0 255.255.255.0 10.0.0.2
ip route 192.167.94.0 255.255.255.0 10.0.0.6
ip route 192.168.94.0 255.255.255.0 10.0.0.2
ip route 192.168.94.0 255.255.255.0 10.0.0.6

ip route 192.177.94.0 255.255.255.0 12.0.0.2
ip route 192.177.94.0 255.255.255.0 12.0.0.6
ip route 192.178.94.0 255.255.255.0 12.0.0.2
ip route 192.178.94.0 255.255.255.0 12.0.0.6
do write
```

### 10. ESCUINTLA

```bash
enable
configure terminal
host ESCUINTLA

!configuramos SERIALES
interface s1/0
ip address 10.0.0.2 255.255.255.252
no shutdown

interface s1/1
ip address 10.0.0.9 255.255.255.252
no shutdown

exit
!configuramos las rutas estáticas

ip route 10.0.0.4 255.255.255.252 10.0.0.1
ip route 10.0.0.4 255.255.255.252 10.0.0.10
ip route 11.0.0.0 255.255.255.252 10.0.0.10
ip route 11.0.0.4 255.255.255.252 10.0.0.10
ip route 12.0.0.0 255.255.255.252 10.0.0.1
ip route 12.0.0.4 255.255.255.252 10.0.0.1
ip route 192.167.94.0 255.255.255.0 10.0.0.10
ip route 192.168.94.0 255.255.255.0 10.0.0.10
ip route 192.177.94.0 255.255.255.0 10.0.0.1
ip route 192.178.94.0 255.255.255.0 10.0.0.1
do write
```

### 11. JUTIAPA

```bash
enable
configure terminal
host JUTIAPA

!configuramos SERIALES
interface s1/0
ip address 11.0.0.5 255.255.255.252
no shutdown

interface s1/1
ip address 11.0.0.1 255.255.255.252
no shutdown

interface s1/2
ip address 10.0.0.6 255.255.255.252
no shutdown

interface s1/3
ip address 10.0.0.10 255.255.255.252
no shutdown

exit
!configuramos las rutas estáticas
ip route 10.0.0.8 255.255.255.252 10.0.0.9
ip route 10.0.0.8 255.255.255.252 10.0.0.5
ip route 12.0.0.0 255.255.255.252 10.0.0.9
ip route 12.0.0.0 255.255.255.252 10.0.0.5
ip route 12.0.0.4 255.255.255.252 10.0.0.9
ip route 12.0.0.4 255.255.255.252 10.0.0.5

ip route 192.167.94.0 255.255.255.0 11.0.0.2
ip route 192.167.94.0 255.255.255.0 11.0.0.6
ip route 192.168.94.0 255.255.255.0 11.0.0.2
ip route 192.168.94.0 255.255.255.0 11.0.0.6

ip route 192.177.94.0 255.255.255.0 10.0.0.9
ip route 192.177.94.0 255.255.255.0 10.0.0.5
ip route 192.178.94.0 255.255.255.0 10.0.0.9
ip route 192.178.94.0 255.255.255.0 10.0.0.5

do write
```
