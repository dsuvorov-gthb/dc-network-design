## Домашнее задание

### Проектирование адресного пространства

### Цель:
1. Собрать схему CLOS;
2. Распределить адресное пространство.


### 1. Схема CLOS:
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/1a9bd622-1c3e-4223-801e-92338e6886cf)

### 2. Распределение адресного пространства:
2.1 Распределние адресного пространства для 4-ех ЦОД-ов:
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/135dd6b7-0688-48b4-bc54-3543a48bf50e)

2.2 Распределние адресного пространства для лабораторной схемы:
|Device | Interface | IP Address | Subnet Mask|
|--- | --- | --- | ---|
|Spine-1 | Lo1 | 10.0.1.0 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.1.0 | 255.255.255.255 (/32)|
| | Lo2 | 10.2.1.0 | 255.255.255.254 (/31)|
| | Lo2 | 10.2.1.2 | 255.255.255.254 (/31)|
| | Lo2 | 10.2.1.4 | 255.255.255.254 (/31)|
|Spine-2 | Lo1 | 10.0.2.0 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.2.0 | 255.255.255.255 (/32)|
| | Eth1 | 10.2.2.0 | 255.255.255.254 (/31)|
| | Eth2 | 10.2.2.2 | 255.255.255.254 (/31)|
| | Eth3 | 10.2.2.4 | 255.255.255.254 (/31)|
|Leaf-1 | Lo1 | 10.0.1.1 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.1.1 | 255.255.255.255 (/32)|
| | Eth1 | 10.2.1.1 | 255.255.255.254 (/31)|
| | Eth2 | 10.2.2.1 | 255.255.255.254 (/31)|
| | Eth8 | 10.4.0.1 | 255.255.255.248 (/29)|
|Leaf-2 | Lo1 | 10.0.1.2 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.1.2 | 255.255.255.255 (/32)|
| | Eth1 | 10.2.1.3 | 255.255.255.254 (/31)|
| | Eth2 | 10.2.2.3 | 255.255.255.254 (/31)|
| | Eth8 | 10.4.0.9 | 255.255.255.248 (/29)|
|Leaf-3 | Lo1 | 10.0.1.3 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.1.3 | 255.255.255.255 (/32)|
| | Eth1 | 10.2.1.5 | 255.255.255.254 (/31)|
| | Eth2 | 10.2.2.5 | 255.255.255.254 (/31)|
| | Eth7 | 10.4.0.17 | 255.255.255.248 (/29)|
| | Eth8 | 10.4.0.25 | 255.255.255.248 (/29)|
|Client-1 | eth0 | 10.4.0.2 | 255.255.255.248 (/29)|
|Client-2 | eth0 | 10.4.0.10 | 255.255.255.248 (/29)|
|Client-3 | eth0 | 10.4.0.18 | 255.255.255.248 (/29)|
|Client-4 | eth0 | 10.4.0.26 | 255.255.255.248 (/29)|

## Конфиги оборудования:
### Spine-1

```
hostname Spine-1
!
interface Ethernet1
   description Leaf-1 | Eth1
   no switchport
   ip address 10.2.1.0/31
!
interface Ethernet2
   description Leaf-2 | Eth1
   no switchport
   ip address 10.2.1.2/31
!
interface Ethernet3
   description Leaf-3 | Eth1
   no switchport
   ip address 10.2.1.4/31
!
interface Loopback1
   description Underlay
   ip address 10.0.1.0/32
!
interface Loopback2
   description Overlay
   ip address 10.1.1.0/32
!
end

Spine-1#sh lldp neighbors
Last table change time   : 5:37:25 ago
Number of table inserts  : 3
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port          Neighbor Device ID       Neighbor Port ID    TTL
---------- ------------------------ ---------------------- ---
Et1           Leaf-1                   Ethernet1           120
Et2           Leaf-2                   Ethernet1           120
Et3           Leaf-3                   Ethernet1           120
```

### Spine-2
```
hostname Spine-2
!
interface Ethernet1
   description Leaf-1 | Eth2
   no switchport
   ip address 10.2.2.0/31
!
interface Ethernet2
   description Leaf-2 | Eth2
   no switchport
   ip address 10.2.2.2/31
!
interface Ethernet3
   description Leaf-3 | Eth2
   no switchport
   ip address 10.2.2.4/31
!
interface Loopback1
   description Underlay
   ip address 10.0.2.0/32
!
interface Loopback2
   description Overlay
   ip address 10.1.2.0/32
!
end

Spine-2#sh lldp neighbors
Last table change time   : 5:40:20 ago
Number of table inserts  : 3
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port          Neighbor Device ID       Neighbor Port ID    TTL
---------- ------------------------ ---------------------- ---
Et1           Leaf-1                   Ethernet2           120
Et2           Leaf-2                   Ethernet2           120
Et3           Leaf-3                   Ethernet2           120
```

### Leaf-1
```
hostname Leaf-1
!
interface Ethernet1
   description Spine-1 | Eth1
   no switchport
   ip address 10.2.1.1/31
!
interface Ethernet2
   description Spine-2 | Eth1
   no switchport
   ip address 10.2.2.1/31
!
interface Ethernet8
   description Client-1 | eth0
   no switchport
   ip address 10.4.0.1/29
!
interface Loopback1
   description Underlay
   ip address 10.0.1.1/32
!
interface Loopback2
   description Overlay
   ip address 10.1.1.1/32

Leaf-1#sh lldp neighbors
Last table change time   : 5:43:35 ago
Number of table inserts  : 2
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port          Neighbor Device ID       Neighbor Port ID    TTL
---------- ------------------------ ---------------------- ---
Et1           Spine-1                  Ethernet1           120
Et2           Spine-2                  Ethernet1           120
```
##### Связанность с "Client-1":
```
Leaf-1#ping 10.4.0.2
PING 10.4.0.2 (10.4.0.2) 72(100) bytes of data.
80 bytes from 10.4.0.2: icmp_seq=1 ttl=64 time=9.28 ms
80 bytes from 10.4.0.2: icmp_seq=2 ttl=64 time=7.94 ms
80 bytes from 10.4.0.2: icmp_seq=3 ttl=64 time=6.57 ms
80 bytes from 10.4.0.2: icmp_seq=4 ttl=64 time=7.85 ms
80 bytes from 10.4.0.2: icmp_seq=5 ttl=64 time=4.95 ms

--- 10.4.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 47ms
rtt min/avg/max/mdev = 4.950/7.321/9.287/1.468 ms, ipg/ewma 11.777/8.216 ms
```
### Leaf-2
```
hostname Leaf-2
!
interface Ethernet1
   description Spine-1 | Eth2
   no switchport
   ip address 10.2.1.3/31
!
interface Ethernet2
   description Spine-2 | Eth2
   no switchport
   ip address 10.2.2.3/31
!
interface Ethernet8
   description Client-2 | Eth0
   no switchport
   ip address 10.4.0.9/29
!
interface Loopback1
   description Underlay
   ip address 10.0.1.2/32
!
interface Loopback2
   description Overlay
   ip address 10.1.1.2/32

Leaf-2#sh lldp neighbors
Last table change time   : 5:48:42 ago
Number of table inserts  : 2
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port          Neighbor Device ID       Neighbor Port ID    TTL
---------- ------------------------ ---------------------- ---
Et1           Spine-1                  Ethernet2           120
Et2           Spine-2                  Ethernet2           120
```
##### Связанность с "Client-2":
```
Leaf-2#ping 10.4.0.10
PING 10.4.0.10 (10.4.0.10) 72(100) bytes of data.
80 bytes from 10.4.0.10: icmp_seq=1 ttl=64 time=61.4 ms
80 bytes from 10.4.0.10: icmp_seq=2 ttl=64 time=54.8 ms
80 bytes from 10.4.0.10: icmp_seq=3 ttl=64 time=52.8 ms
80 bytes from 10.4.0.10: icmp_seq=4 ttl=64 time=51.4 ms
80 bytes from 10.4.0.10: icmp_seq=5 ttl=64 time=46.2 ms

--- 10.4.0.10 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 46ms
rtt min/avg/max/mdev = 46.240/53.357/61.468/4.950 ms, pipe 5, ipg/ewma 11.675/57.083 ms
```
### Leaf-3
```
hostname Leaf-3
!
interface Ethernet1
   description Spine-1 | Eth3
   no switchport
   ip address 10.2.1.5/31
!
interface Ethernet2
   description Spine-2 | Eth3
   no switchport
   ip address 10.2.2.5/31
!
interface Ethernet7
   description Clinet-4 | Eth0
   no switchport
   ip address 10.4.0.25/29
!
interface Ethernet8
   description Client-3 | Eth0
   no switchport
   ip address 10.4.0.17/29
!
interface Loopback1
   description Underlay
   ip address 10.0.1.3/32
!
interface Loopback2
   description Overlay
   ip address 10.1.1.3/32
!

Leaf-3#sh lldp neighbors
Last table change time   : 5:50:54 ago
Number of table inserts  : 2
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port          Neighbor Device ID       Neighbor Port ID    TTL
---------- ------------------------ ---------------------- ---
Et1           Spine-1                  Ethernet3           120
Et2           Spine-2                  Ethernet3           120
```
##### Связанность с "Client-3":
```
Leaf-3#ping 10.4.0.18
PING 10.4.0.18 (10.4.0.18) 72(100) bytes of data.
80 bytes from 10.4.0.18: icmp_seq=1 ttl=64 time=36.1 ms
80 bytes from 10.4.0.18: icmp_seq=2 ttl=64 time=30.5 ms
80 bytes from 10.4.0.18: icmp_seq=3 ttl=64 time=29.8 ms
80 bytes from 10.4.0.18: icmp_seq=4 ttl=64 time=4.46 ms
80 bytes from 10.4.0.18: icmp_seq=5 ttl=64 time=4.17 ms

--- 10.4.0.18 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 96ms
rtt min/avg/max/mdev = 4.178/21.045/36.192/13.832 ms, pipe 3, ipg/ewma 24.094/27.640 ms
```
##### Связанность с "Client-4":
```
Leaf-3#ping 10.4.0.26
PING 10.4.0.26 (10.4.0.26) 72(100) bytes of data.
80 bytes from 10.4.0.26: icmp_seq=1 ttl=64 time=5.30 ms
80 bytes from 10.4.0.26: icmp_seq=2 ttl=64 time=5.53 ms
80 bytes from 10.4.0.26: icmp_seq=3 ttl=64 time=6.62 ms
80 bytes from 10.4.0.26: icmp_seq=4 ttl=64 time=4.97 ms
80 bytes from 10.4.0.26: icmp_seq=5 ttl=64 time=7.58 ms

--- 10.4.0.26 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 35ms
rtt min/avg/max/mdev = 4.977/6.006/7.589/0.965 ms, ipg/ewma 8.754/5.701 ms
Leaf-3#
```
