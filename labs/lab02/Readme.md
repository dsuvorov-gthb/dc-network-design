### Построение Underlay сети(OSPF)

#### Собрана Underlay сеть
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/950bcc47-d8c8-47ff-aaf3-651cd0e11b72)

Настройки Underlay сети:

<details>
<summary> Spine-1 </summary>
  
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




