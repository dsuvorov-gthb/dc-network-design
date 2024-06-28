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
