### Построение Underlay сети(OSPF)

#### Собрана Underlay сеть
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/950bcc47-d8c8-47ff-aaf3-651cd0e11b72)

Настройки Underlay сети:

<details>
<summary> Spine-1 (conf) </summary>
  
```
Spine-1#sh run
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
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf-2 | Eth1
   no switchport
   ip address 10.2.1.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf-3 | Eth1
   no switchport
   ip address 10.2.1.4/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
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
   ip ospf area 0.0.0.0
!
interface Loopback2
   description Overlay
   ip address 10.1.1.0/32
!
interface Management1
!
ip routing
!
router ospf 100
   router-id 10.0.1.0
   network 10.0.1.0/32 area 0.0.0.0
   network 10.2.1.0/31 area 0.0.0.0
   network 10.2.1.2/31 area 0.0.0.0
   network 10.2.1.4/31 area 0.0.0.0
   max-lsa 20000 40 ignore-time 10 ignore-count 4 reset-time 20
!
end
```

</details>

Spine-1 ospf detail

```
Spine-1#sh ip route ospf detail

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

 O        10.0.1.1/32 [110/20] via 10.2.1.1, Ethernet1 Leaf-1 | Eth1
 O        10.0.1.2/32 [110/20] via 10.2.1.3, Ethernet2 Leaf-2 | Eth1
 O        10.0.1.3/32 [110/20] via 10.2.1.5, Ethernet3 Leaf-3 | Eth1
 O        10.0.2.0/32 [110/30] via 10.2.1.1, Ethernet1 Leaf-1 | Eth1
                               via 10.2.1.3, Ethernet2 Leaf-2 | Eth1
                               via 10.2.1.5, Ethernet3 Leaf-3 | Eth1
 O        10.2.2.0/31 [110/20] via 10.2.1.1, Ethernet1 Leaf-1 | Eth1
 O        10.2.2.2/31 [110/20] via 10.2.1.3, Ethernet2 Leaf-2 | Eth1
 O        10.2.2.4/31 [110/20] via 10.2.1.5, Ethernet3 Leaf-3 | Eth1
 O        10.4.0.0/29 [110/20] via 10.2.1.1, Ethernet1 Leaf-1 | Eth1
 O        10.4.0.8/29 [110/20] via 10.2.1.3, Ethernet2 Leaf-2 | Eth1
 O        10.4.0.16/29 [110/20] via 10.2.1.5, Ethernet3 Leaf-3 | Eth1
 O        10.4.0.24/29 [110/20] via 10.2.1.5, Ethernet3 Leaf-3 | Eth1

Spine-1#
```

<details>
<summary> Spine-2 (conf) </summary>

```
Spine-2#sh run
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
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf-2 | Eth2
   no switchport
   ip address 10.2.2.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf-3 | Eth2
   no switchport
   ip address 10.2.2.4/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
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
   ip ospf area 0.0.0.0
!
interface Loopback2
   description Overlay
   ip address 10.1.2.0/32
!
interface Management1
!
ip routing
!
router ospf 100
   router-id 10.2.2.0
   network 10.0.2.0/32 area 0.0.0.0
   network 10.2.2.0/31 area 0.0.0.0
   network 10.2.2.2/31 area 0.0.0.0
   network 10.2.2.4/31 area 0.0.0.0
   max-lsa 20000 40 ignore-time 10 ignore-count 4 reset-time 20
!
end
Spine-2#
```

</details>

Spine-2 ospf detail

```
Spine-2#sh ip route ospf det

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

 O        10.0.1.0/32 [110/30] via 10.2.2.1, Ethernet1 Leaf-1 | Eth2
                               via 10.2.2.3, Ethernet2 Leaf-2 | Eth2
                               via 10.2.2.5, Ethernet3 Leaf-3 | Eth2
 O        10.0.1.1/32 [110/20] via 10.2.2.1, Ethernet1 Leaf-1 | Eth2
 O        10.0.1.2/32 [110/20] via 10.2.2.3, Ethernet2 Leaf-2 | Eth2
 O        10.0.1.3/32 [110/20] via 10.2.2.5, Ethernet3 Leaf-3 | Eth2
 O        10.2.1.0/31 [110/20] via 10.2.2.1, Ethernet1 Leaf-1 | Eth2
 O        10.2.1.2/31 [110/20] via 10.2.2.3, Ethernet2 Leaf-2 | Eth2
 O        10.2.1.4/31 [110/20] via 10.2.2.5, Ethernet3 Leaf-3 | Eth2
 O        10.4.0.0/29 [110/20] via 10.2.2.1, Ethernet1 Leaf-1 | Eth2
 O        10.4.0.8/29 [110/20] via 10.2.2.3, Ethernet2 Leaf-2 | Eth2
 O        10.4.0.16/29 [110/20] via 10.2.2.5, Ethernet3 Leaf-3 | Eth2
 O        10.4.0.24/29 [110/20] via 10.2.2.5, Ethernet3 Leaf-3 | Eth2

Spine-2#
```

<details>
<summary> Leaf-1 (conf) </summary>

```
Leaf-1#sh running-config
! Command: show running-config
! device: Leaf-1 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Leaf-1
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine-1 | Eth1
   no switchport
   ip address 10.2.1.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth1
   no switchport
   ip address 10.2.2.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
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
   description Client-1 | eth0
   no switchport
   ip address 10.4.0.1/29
!
interface Loopback1
   description Underlay
   ip address 10.0.1.1/32
   ip ospf area 0.0.0.0
!
interface Loopback2
   description Overlay
   ip address 10.1.1.1/32
!
interface Management1
!
ip routing
!
router ospf 100
   router-id 10.0.1.1
   network 10.0.1.1/32 area 0.0.0.0
   network 10.2.1.0/31 area 0.0.0.0
   network 10.2.2.0/31 area 0.0.0.0
   network 10.4.0.0/29 area 0.0.0.0
   max-lsa 20000 40 ignore-time 10 ignore-count 4 reset-time 20
!
end
Leaf-1#

```

</details>


<details open>
<summary> Leaf-2 (conf) </summary>

```
Leaf-2#sh run
! Command: show running-config
! device: Leaf-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Leaf-2
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine-1 | Eth2
   no switchport
   ip address 10.2.1.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth2
   no switchport
   ip address 10.2.2.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
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
   description Client-2 | Eth0
   no switchport
   ip address 10.4.0.9/29
!
interface Loopback1
   description Underlay
   ip address 10.0.1.2/32
   ip ospf area 0.0.0.0
!
interface Loopback2
   description Overlay
   ip address 10.1.1.2/32
!
interface Management1
!
ip routing
!
router ospf 100
   router-id 10.0.1.2
   network 10.0.1.2/32 area 0.0.0.0
   network 10.2.1.2/31 area 0.0.0.0
   network 10.2.2.4/31 area 0.0.0.0
   network 10.4.0.8/29 area 0.0.0.0
   max-lsa 20000 40 ignore-time 10 ignore-count 4 reset-time 20
!
end
Leaf-2#
```

</details>
  
<details>
<summary> Leaf-3 (conf) </summary>

```
Leaf-3#sh running-config
! Command: show running-config
! device: Leaf-3 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname Leaf-3
!
spanning-tree mode mstp
!
interface Ethernet1
   description Spine-1 | Eth3
   no switchport
   ip address 10.2.1.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth3
   no switchport
   ip address 10.2.2.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
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
   ip ospf area 0.0.0.0
!
interface Loopback2
   description Overlay
   ip address 10.1.1.3/32
!
interface Management1
!
ip routing
!
router ospf 100
   router-id 10.0.1.3
   network 10.0.1.3/32 area 0.0.0.0
   network 10.2.1.4/32 area 0.0.0.0
   network 10.2.2.4/32 area 0.0.0.0
   network 10.4.0.16/29 area 0.0.0.0
   network 10.4.0.24/29 area 0.0.0.0
   max-lsa 20000 40 ignore-time 10 ignore-count 4 reset-time 20
!
end
Leaf-3#
```

</details>

