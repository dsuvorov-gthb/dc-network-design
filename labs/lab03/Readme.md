### Построение Underlay сети(ISIS)

#### Underlay сеть
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/76fcc847-cc21-4ba6-9826-840d1f712d00)

#### План IP-адресации
![image](https://github.com/dsuvorov-gthb/dc-network-design/assets/169836298/0a38b7d8-58de-409b-b057-7f2ad6f1bf7f)

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
   isis enable 100
   isis network point-to-point
!
interface Ethernet2
   description Leaf-2 | Eth1
   no switchport
   ip address 10.2.1.2/31
   isis enable 100
   isis network point-to-point
!
interface Ethernet3
   description Leaf-3 | Eth1
   no switchport
   ip address 10.2.1.4/31
   isis enable 100
   isis network point-to-point
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
   isis enable 100
!
interface Loopback2
   description Overlay
   ip address 10.1.1.0/32
!
interface Management1
!
ip routing
!
router isis 100
   net 49.0010.0000.0001.0000.00
   !
   address-family ipv4 unicast
!
end
Spine-1#
```

</details>

<details>
<summary> Доступность Lo1 Spine-2: </summary>

```
Spine-1#ping 10.0.2.0
PING 10.0.2.0 (10.0.2.0) 72(100) bytes of data.
80 bytes from 10.0.2.0: icmp_seq=1 ttl=63 time=31.2 ms
80 bytes from 10.0.2.0: icmp_seq=2 ttl=63 time=25.7 ms
80 bytes from 10.0.2.0: icmp_seq=3 ttl=63 time=24.0 ms
80 bytes from 10.0.2.0: icmp_seq=4 ttl=63 time=19.6 ms
80 bytes from 10.0.2.0: icmp_seq=5 ttl=63 time=18.8 ms

--- 10.0.2.0 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 83ms
rtt min/avg/max/mdev = 18.832/23.907/31.247/4.503 ms, pipe 3, ipg/ewma 20.847/27.278 ms
Spine-1#
```

</details>

<details>
<summary> Доступность Lo1 Leaf-1: </summary>

```
Spine-1#ping 10.0.1.1
PING 10.0.1.1 (10.0.1.1) 72(100) bytes of data.
80 bytes from 10.0.1.1: icmp_seq=1 ttl=64 time=13.7 ms
80 bytes from 10.0.1.1: icmp_seq=2 ttl=64 time=11.3 ms
80 bytes from 10.0.1.1: icmp_seq=3 ttl=64 time=9.44 ms
80 bytes from 10.0.1.1: icmp_seq=4 ttl=64 time=9.29 ms
80 bytes from 10.0.1.1: icmp_seq=5 ttl=64 time=8.56 ms

--- 10.0.1.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 60ms
rtt min/avg/max/mdev = 8.564/10.487/13.778/1.885 ms, ipg/ewma 15.146/12.018 ms
Spine-1#
```

</details>

<details>
<summary> Доступность Lo1 Leaf-2: </summary>

```
Spine-1#ping 10.0.1.2
PING 10.0.1.2 (10.0.1.2) 72(100) bytes of data.
80 bytes from 10.0.1.2: icmp_seq=1 ttl=64 time=10.9 ms
80 bytes from 10.0.1.2: icmp_seq=2 ttl=64 time=9.01 ms
80 bytes from 10.0.1.2: icmp_seq=3 ttl=64 time=7.65 ms
80 bytes from 10.0.1.2: icmp_seq=4 ttl=64 time=17.0 ms
80 bytes from 10.0.1.2: icmp_seq=5 ttl=64 time=8.59 ms

--- 10.0.1.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 59ms
rtt min/avg/max/mdev = 7.654/10.655/17.089/3.391 ms, ipg/ewma 14.757/10.839 ms
Spine-1#
```

</details>

<details>
<summary> Доступность Lo1 Leaf-3: </summary>

```
Spine-1#ping 10.0.1.3
PING 10.0.1.3 (10.0.1.3) 72(100) bytes of data.
80 bytes from 10.0.1.3: icmp_seq=1 ttl=64 time=11.1 ms
80 bytes from 10.0.1.3: icmp_seq=2 ttl=64 time=7.01 ms
80 bytes from 10.0.1.3: icmp_seq=3 ttl=64 time=6.28 ms
80 bytes from 10.0.1.3: icmp_seq=4 ttl=64 time=11.6 ms
80 bytes from 10.0.1.3: icmp_seq=5 ttl=64 time=7.26 ms

--- 10.0.1.3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 48ms
rtt min/avg/max/mdev = 6.289/8.670/11.686/2.254 ms, ipg/ewma 12.231/9.883 ms
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
   isis enable 100
   isis network point-to-point
!
interface Ethernet2
   description Leaf-2 | Eth2
   no switchport
   ip address 10.2.2.2/31
   isis enable 100
   isis network point-to-point
!
interface Ethernet3
   description Leaf-3 | Eth2
   no switchport
   ip address 10.2.2.4/31
   isis enable 100
   isis network point-to-point
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
   isis enable 100
!
interface Loopback2
   description Overlay
   ip address 10.1.2.0/32
!
interface Management1
!
ip routing
!
router isis 100
   net 49.0010.0000.0002.0000.00
   !
   address-family ipv4 unicast
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
interface Ethernet1
   description Spine-1 | Eth1
   no switchport
   ip address 10.2.1.1/31
   isis enable 100
   isis network point-to-point
!
interface Ethernet2
   description Spine-2 | Eth1
   no switchport
   ip address 10.2.2.1/31
   isis enable 100
   isis network point-to-point
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
   isis enable 100
!
interface Loopback2
   description Overlay
   ip address 10.1.1.1/32
!
interface Management1
!
ip routing
!
router isis 100
   net 49.0010.0000.0001.0001.00
   !
   address-family ipv4 unicast
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
interface Ethernet1
   description Spine-1 | Eth2
   no switchport
   ip address 10.2.1.3/31
   isis enable 100
   isis network point-to-point
!
interface Ethernet2
   description Spine-2 | Eth2
   no switchport
   ip address 10.2.2.3/31
   isis enable 100
   isis network point-to-point
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
   isis enable 100
!
interface Loopback2
   description Overlay
   ip address 10.1.1.2/32
!
interface Management1
!
ip routing
!
router isis 100
   net 49.0010.0000.0001.0002.00
   !
   address-family ipv4 unicast
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
   isis enable 100
   isis network point-to-point
!
interface Ethernet2
   description Spine-2 | Eth3
   no switchport
   ip address 10.2.2.5/31
   isis enable 100
   isis network point-to-point
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
   isis enable 100
!
interface Loopback2
   description Overlay
   ip address 10.1.1.3/32
!
interface Management1
!
ip routing
!
router isis 100
   net 49.0010.0000.0001.0003.00
   !
   address-family ipv4 unicast
!
end
Leaf-3#
```

</details>
