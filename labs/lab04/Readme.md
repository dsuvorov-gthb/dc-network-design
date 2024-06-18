### Построение Underlay сети(BGP)

#### Underlay сеть
![Uploading image.png…]()

#### План IP-адресации


#### в таблице маршрутизации появились Loopback интерфейсы, а также ptp-сети:

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
