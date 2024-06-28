## VxLAN. L2 VNI

### Цель:
Настроить Overlay на основе VxLAN EVPN для L2 связанности между клиентами

## Схема сети
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/d59a519b-06c7-4ba6-ac13-ecb056459a13)

Проверяем наличие Vlanif 10 и 20 на Leaf-1:
```
Leaf-1#sh ip int brief
                                                                        Address
Interface       IP Address           Status     Protocol         MTU    Owner
--------------- -------------------- ---------- ------------ ---------- -------
Ethernet1       10.2.1.1/31          up         up              9214
Ethernet2       10.2.2.1/31          up         up              9214
Ethernet8       10.4.0.1/29          up         up              1500
Loopback1       10.0.1.1/32          up         up             65535
Loopback2       10.1.1.1/32          up         up             65535
Management1     unassigned           up         up              1500
Vlan10          192.168.10.11/24     up         up              1500
Vlan20          192.168.20.11/24     up         up              1500
```

Проверяем наличие MAC-адресации со стороны Leaf-2 и Leaf-3:
```
Leaf-1#sh vxlan address-table
          Vxlan Mac Address Table
----------------------------------------------------------------------

VLAN  Mac Address     Type      Prt  VTEP             Moves   Last Move
----  -----------     ----      ---  ----             -----   ---------
  10  5000.00d5.5dc0  DYNAMIC   Vx1  10.1.1.2         1       0:00:14 ago
  20  5000.0015.f4e8  DYNAMIC   Vx1  10.1.1.3         1       0:00:06 ago
Total Remote Mac Addresses for this criterion: 2
Leaf-1#
```

Проверяем связанность до vlan if 10 на Leaf-2 - 192.168.10.12:
```
Leaf-1#ping 192.168.10.12
PING 192.168.10.12 (192.168.10.12) 72(100) bytes of data.
80 bytes from 192.168.10.12: icmp_seq=1 ttl=64 time=200 ms
80 bytes from 192.168.10.12: icmp_seq=2 ttl=64 time=198 ms
80 bytes from 192.168.10.12: icmp_seq=3 ttl=64 time=210 ms
80 bytes from 192.168.10.12: icmp_seq=4 ttl=64 time=214 ms
80 bytes from 192.168.10.12: icmp_seq=5 ttl=64 time=216 ms

--- 192.168.10.12 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 49ms
rtt min/avg/max/mdev = 198.547/208.122/216.388/7.253 ms, pipe 5, ipg/ewma 12.344/204.839 ms
```
Проверяем связанность до vlan if 20 на Leaf-3 - 192.168.20.12:
```
Leaf-1#ping 192.168.20.12
PING 192.168.20.12 (192.168.20.12) 72(100) bytes of data.
80 bytes from 192.168.20.12: icmp_seq=1 ttl=64 time=133 ms
80 bytes from 192.168.20.12: icmp_seq=2 ttl=64 time=131 ms
80 bytes from 192.168.20.12: icmp_seq=3 ttl=64 time=138 ms
80 bytes from 192.168.20.12: icmp_seq=4 ttl=64 time=137 ms
80 bytes from 192.168.20.12: icmp_seq=5 ttl=64 time=141 ms

--- 192.168.20.12 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 49ms
rtt min/avg/max/mdev = 131.681/136.421/141.035/3.382 ms, pipe 5, ipg/ewma 12.408/135.235 ms
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
   mtu 9214
   no switchport
   ip address 10.2.1.0/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf-2 | Eth1
   mtu 9214
   no switchport
   ip address 10.2.1.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf-3 | Eth1
   mtu 9214
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
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router bgp 65000
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY remote-as 64500
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback2
   neighbor EVPN-OVERLAY ebgp-multihop 4
   neighbor EVPN-OVERLAY send-community
   neighbor 10.1.1.1 peer group EVPN-OVERLAY
   neighbor 10.1.1.2 peer group EVPN-OVERLAY
   neighbor 10.1.1.3 peer group EVPN-OVERLAY
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
!
router ospf 1
   router-id 10.0.1.0
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   network 0.0.0.0/0 area 0.0.0.0
   max-lsa 12000
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
   mtu 9214
   no switchport
   ip address 10.2.2.0/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Leaf-2 | Eth2
   mtu 9214
   no switchport
   ip address 10.2.2.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description Leaf-3 | Eth2
   mtu 9214
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
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router bgp 65000
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY remote-as 64500
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback2
   neighbor EVPN-OVERLAY ebgp-multihop 4
   neighbor EVPN-OVERLAY send-community
   neighbor 10.1.1.1 peer group EVPN-OVERLAY
   neighbor 10.1.1.2 peer group EVPN-OVERLAY
   neighbor 10.1.1.3 peer group EVPN-OVERLAY
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
!
router ospf 1
   router-id 10.0.2.0
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   network 0.0.0.0/0 area 0.0.0.0
   max-lsa 12000
!
end
Spine-2#

```

</details>

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
vlan 10
   name VNI10
!
vlan 20
   name VNI20
!
interface Ethernet1
   description Spine-1 | Eth1
   mtu 9214
   no switchport
   ip address 10.2.1.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth1
   mtu 9214
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
   description VXLAN_CLIENT_1
   switchport access vlan 10
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   ip address 192.168.10.11/24
!
interface Vlan20
   ip address 192.168.20.11/24
!
interface Vxlan1
   description Tunnel_to_VXLAN
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 10 vni 10
   vxlan vlan 20 vni 20
   vxlan flood vtep 10.1.1.1 10.1.1.2 10.1.1.3
!
ip routing
!
router bgp 64500
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY remote-as 65000
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback2
   neighbor EVPN-OVERLAY allowas-in 1
   neighbor EVPN-OVERLAY ebgp-multihop 4
   neighbor EVPN-OVERLAY send-community
   neighbor EVPN-OVERLAY maximum-routes 12000
   neighbor 10.1.1.0 peer group EVPN-OVERLAY
   neighbor 10.1.2.0 peer group EVPN-OVERLAY
   !
   vlan 10
      rd 10:10
      route-target both 10:10
      redistribute learned
   !
   vlan 20
      rd 20:20
      route-target both 20:20
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
!
router ospf 1
   router-id 10.0.1.1
   auto-cost reference-bandwidth 10000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
   maximum-paths 12
!
end
Leaf-1#

```

</details>

<details>
<summary> Leaf-2 (conf) </summary>
  
```
Leaf-2#sh running-config
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
vlan 10
   name VNI1010
!
interface Ethernet1
   description Spine-1 | Eth2
   mtu 9214
   no switchport
   ip address 10.2.1.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth2
   mtu 9214
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
   description VXLAN_CLIENT_2
   switchport access vlan 10
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   ip address 192.168.10.12/24
!
interface Vxlan1
   description Tunnel_to_VXLAN
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 10 vni 10
   vxlan flood vtep 10.1.1.1 10.1.1.2 10.1.1.3
!
ip routing
!
router bgp 64500
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY remote-as 65000
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback2
   neighbor EVPN-OVERLAY allowas-in 1
   neighbor EVPN-OVERLAY ebgp-multihop 4
   neighbor EVPN-OVERLAY send-community
   neighbor EVPN-OVERLAY maximum-routes 12000
   neighbor 10.1.1.0 peer group EVPN-OVERLAY
   neighbor 10.1.2.0 peer group EVPN-OVERLAY
   !
   vlan 10
      rd 10:10
      route-target both 10:10
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
!
router ospf 1
   router-id 10.0.1.2
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
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
vlan 20
   name VNI20
!
interface Ethernet1
   description Spine-1 | Eth3
   mtu 9214
   no switchport
   ip address 10.2.1.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description Spine-2 | Eth3
   mtu 9214
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan20
   ip address 192.168.20.12/24
!
interface Vxlan1
   description Tunnel_to_VXLAN
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 10 vni 10
   vxlan vlan 20 vni 20
   vxlan flood vtep 10.1.1.1 10.1.1.2 10.1.1.3
!
ip routing
!
router bgp 64500
   neighbor EVPN-OVERLAY peer group
   neighbor EVPN-OVERLAY remote-as 65000
   neighbor EVPN-OVERLAY next-hop-unchanged
   neighbor EVPN-OVERLAY update-source Loopback2
   neighbor EVPN-OVERLAY allowas-in 1
   neighbor EVPN-OVERLAY ebgp-multihop 4
   neighbor EVPN-OVERLAY send-community
   neighbor EVPN-OVERLAY maximum-routes 12000
   neighbor 10.1.1.0 peer group EVPN-OVERLAY
   neighbor 10.1.2.0 peer group EVPN-OVERLAY
   !
   vlan 10
      rd 10:10
      route-target both 10:10
      redistribute learned
   !
   vlan 20
      rd 20:20
      route-target both 20:20
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY activate
!
router ospf 1
   router-id 10.0.1.3
   auto-cost reference-bandwidth 10000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
   maximum-paths 12
!
end
Leaf-3#

```

</details>
