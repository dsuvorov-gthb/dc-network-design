### Построение Underlay сети(BGP)

#### Underlay сеть
![Uploading image.png…]()

#### План IP-адресации
|Device | Interface | IP Address | Subnet Mask|
|--- | --- | --- | ---|
|Spine-1 | Lo1 | 10.0.1.0 | 255.255.255.255 (/32)|
| | Lo2 | 10.1.1.0 | 255.255.255.255 (/32)|
| | Eth1 | 10.2.1.0 | 255.255.255.254 (/31)|
| | Eth2 | 10.2.1.2 | 255.255.255.254 (/31)|
| | Eth3 | 10.2.1.4 | 255.255.255.254 (/31)|
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

#### в таблице маршрутизации появились Loopback интерфейсы (/32), ptp-сети (/31) и клиентские сети (/29):

```
Leaf-3#sh ip bgp | begin Network
         Network                Next Hop            Metric  LocPref Weight  Path
 * >     10.0.1.0/32            10.2.1.4              0       100     0       i
 * >     10.0.1.1/32            10.2.1.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.1.0
 *       10.0.1.1/32            10.2.2.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.2.0
 * >     10.0.1.2/32            10.2.1.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.1.0
 *       10.0.1.2/32            10.2.2.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.2.0
 * >     10.0.1.3/32            -                     0       0       -       i
 * >     10.0.2.0/32            10.2.2.4              0       100     0       i
 * >     10.2.1.0/31            10.2.1.4              0       100     0       i
 *       10.2.1.0/31            10.2.2.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.2.0
 * >     10.2.1.2/31            10.2.1.4              0       100     0       i
 *       10.2.1.2/31            10.2.2.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.2.0
 * >     10.2.1.4/31            -                     1       0       -       i
 *       10.2.1.4/31            10.2.1.4              0       100     0       i
 * >     10.2.2.0/31            10.2.1.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.1.0
 *       10.2.2.0/31            10.2.2.4              0       100     0       i
 * >     10.2.2.2/31            10.2.1.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.1.0
 *       10.2.2.2/31            10.2.2.4              0       100     0       i
 * >     10.2.2.4/31            -                     1       0       -       i
 *       10.2.2.4/31            10.2.2.4              0       100     0       i
 * >     10.4.0.0/29            10.2.1.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.1.0
 *       10.4.0.0/29            10.2.2.4              0       100     0       i Or-ID: 10.0.1.1 C-LST: 10.0.2.0
 * >     10.4.0.8/29            10.2.1.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.1.0
 *       10.4.0.8/29            10.2.2.4              0       100     0       i Or-ID: 10.0.1.2 C-LST: 10.0.2.0
 * >     10.4.0.16/29           -                     1       0       -       i
 * >     10.4.0.24/29           -                     1       0       -       i
Leaf-3#
```

#### доступность Client-2, Client-3 и Client-4 c Client-1:

```
Client-1> sh ip

NAME        : Client-1[1]
IP/MASK     : 10.4.0.2/29
GATEWAY     : 10.4.0.1
DNS         :
MAC         : 00:50:79:66:68:06
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

Client-1> ping 10.4.0.10

84 bytes from 10.4.0.10 icmp_seq=1 ttl=61 time=30.609 ms
84 bytes from 10.4.0.10 icmp_seq=2 ttl=61 time=34.330 ms
84 bytes from 10.4.0.10 icmp_seq=3 ttl=61 time=31.648 ms
84 bytes from 10.4.0.10 icmp_seq=4 ttl=61 time=24.619 ms
84 bytes from 10.4.0.10 icmp_seq=5 ttl=61 time=26.153 ms

Client-1> ping 10.4.0.18

84 bytes from 10.4.0.18 icmp_seq=1 ttl=61 time=75.320 ms
84 bytes from 10.4.0.18 icmp_seq=2 ttl=61 time=38.635 ms
84 bytes from 10.4.0.18 icmp_seq=3 ttl=61 time=31.496 ms
84 bytes from 10.4.0.18 icmp_seq=4 ttl=61 time=27.537 ms
84 bytes from 10.4.0.18 icmp_seq=5 ttl=61 time=27.105 ms

Client-1> ping 10.4.0.26

84 bytes from 10.4.0.26 icmp_seq=1 ttl=61 time=42.967 ms
84 bytes from 10.4.0.26 icmp_seq=2 ttl=61 time=86.047 ms
84 bytes from 10.4.0.26 icmp_seq=3 ttl=61 time=29.440 ms
84 bytes from 10.4.0.26 icmp_seq=4 ttl=61 time=27.602 ms
84 bytes from 10.4.0.26 icmp_seq=5 ttl=61 time=26.974 ms

Client-1>
```

#### Настройки оборудования:

<details>
<summary> Spine-1 (conf) </summary>
  
```
Spine-1#sh running-config
! Command: show running-config
! device: Spine-1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Spine-1
!
spanning-tree mode mstp
!
interface Ethernet1
   description Leaf-1 | Eth1
   no switchport
   ip address 10.2.1.0/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet2
   description Leaf-2 | Eth1
   no switchport
   ip address 10.2.1.2/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet3
   description Leaf-3 | Eth1
   no switchport
   ip address 10.2.1.4/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   description Underlay
   ip address 10.0.1.0/32
!
interface Loopback2
   description Overlay
   ip address 10.1.1.0/32
!
interface Management1
!
ip routing
!
router bgp 64500
   router-id 10.0.1.0
   neighbor 10.2.1.1 remote-as 64500
   neighbor 10.2.1.1 next-hop-self
   neighbor 10.2.1.1 bfd
   neighbor 10.2.1.1 description Leaf-1
   neighbor 10.2.1.1 route-reflector-client
   neighbor 10.2.1.3 remote-as 64500
   neighbor 10.2.1.3 next-hop-self
   neighbor 10.2.1.3 bfd
   neighbor 10.2.1.3 description Leaf-2
   neighbor 10.2.1.3 route-reflector-client
   neighbor 10.2.1.5 remote-as 64500
   neighbor 10.2.1.5 next-hop-self
   neighbor 10.2.1.5 bfd
   neighbor 10.2.1.5 description Leaf-3
   neighbor 10.2.1.5 route-reflector-client
   network 10.0.1.0/32
   network 10.2.1.0/31
   network 10.2.1.2/31
   network 10.2.1.4/31
   network 10.4.0.0/14
!
end
Spine-1#

```

</details>



<details>
<summary> Spine-2 (conf) </summary>
  
```
Spine-2#sh running-config
! Command: show running-config
! device: Spine-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Spine-2
!
spanning-tree mode mstp
!
interface Ethernet1
   description Leaf-1 | Eth2
   no switchport
   ip address 10.2.2.0/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet2
   description Leaf-2 | Eth2
   no switchport
   ip address 10.2.2.2/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet3
   description Leaf-3 | Eth2
   no switchport
   ip address 10.2.2.4/31
   bfd interval 200 min-rx 200 multiplier 3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   description Underlay
   ip address 10.0.2.0/32
!
interface Loopback2
   description Overlay
   ip address 10.1.2.0/32
!
interface Management1
!
ip routing
!
router bgp 64500
   router-id 10.0.2.0
   neighbor 10.2.2.1 remote-as 64500
   neighbor 10.2.2.1 next-hop-self
   neighbor 10.2.2.1 bfd
   neighbor 10.2.2.1 description Leaf-1
   neighbor 10.2.2.1 route-reflector-client
   neighbor 10.2.2.3 remote-as 64500
   neighbor 10.2.2.3 next-hop-self
   neighbor 10.2.2.3 bfd
   neighbor 10.2.2.3 description Leaf-2
   neighbor 10.2.2.3 route-reflector-client
   neighbor 10.2.2.5 remote-as 64500
   neighbor 10.2.2.5 next-hop-self
   neighbor 10.2.2.5 bfd
   neighbor 10.2.2.5 description Leaf-3
   neighbor 10.2.2.5 route-reflector-client
   network 10.0.2.0/32
   network 10.2.2.0/31
   network 10.2.2.2/31
   network 10.2.2.4/31
!
end
Spine-2#

```

</details>

