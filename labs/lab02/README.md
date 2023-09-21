## OSPF

### Цели:
- настроить OSPF в Underlay-сети, для IP-связанности между всеми устройствами NXOS

### Описание выполнения лабораторной работы:
- запустить процесс OSPF UNDERLAY на каждом L3-коммутаторе
- назначить Router ID каждому устройству
- все интерфейсы, кроме клиентских, L3-коммутаторов располагаются в Backbone Area
- все интерфейсы, участвующие в OSPF, должны быть переведены в режим point-to-point

Была изменена адресация: использована маска /31.

### Физическая схема сети:
![Схема](Network_tolology_with_ospf.jpg)

#### <u>Таблица интерфейсов и адресации, участвующих в OSPF:</u>

|Device|Interface|IP Address|Subnet Mask|
|:-:|:-:|:-:|:-:|
SpineL|Ethernet 1/1|10.1.0.0|255.255.255.254|
Leaf1|Ethernet 1/1|10.1.0.1|255.255.255.254|
SpineL|Ethernet 1/2|10.1.0.2|255.255.255.254|
Leaf2|Ethernet 1/1|10.1.0.3|255.255.255.254|
SpineL|Ethernet 1/3|10.1.0.4|255.255.255.254|
Leaf3|Ethernet 1/1|10.1.0.5|255.255.255.254|
SpineR|Ethernet 1/1|10.2.0.0|255.255.255.254|
Leaf1|Ethernet 1/2|10.2.0.1|255.255.255.254|
SpineR|Ethernet 1/2|10.2.0.2|255.255.255.254|
Leaf2|Ethernet 1/2|10.2.0.3|255.255.255.254|
SpineR|Ethernet 1/3|10.2.0.4|255.255.255.254|
Leaf3|Ethernet 1/2|10.2.0.5|255.255.255.254|
LEAF1|Ethernet 1/4|172.16.1.1|255.255.255.0|
LEAF2|Ethernet 1/4|172.16.2.1|255.255.255.0|
LEAF3|Ethernet 1/4|172.16.3.1|255.255.255.0|
LEAF3|Ethernet 1/5|172.16.4.1|255.255.255.0|

#### <u>Сети, анонсируемые в OSPF:</u>

|Device|Router ID|Network|Subnet Mask|
|:-:|:-:|:-:|:-:|
|SPINE1|1.1.1.1|10.0.0.0|255.255.255.252|
|SPINE1|1.1.1.1|10.0.0.8|255.255.255.252|
|SPINE1|1.1.1.1|10.0.0.16|255.255.255.252|
|SPINE2|2.2.2.2|10.0.0.0|255.255.255.252|
|SPINE2|2.2.2.2|10.0.0.8|255.255.255.252|
|SPINE2|2.2.2.2|10.0.0.16|255.255.255.252|
|LEAF1|3.3.3.3|10.0.0.0|255.255.255.252|
|LEAF1|3.3.3.3|10.0.0.4|255.255.255.252|
|LEAF2|4.4.4.4|10.0.0.8|255.255.255.252|
|LEAF2|4.4.4.4|10.0.0.12|255.255.255.252|
|LEAF3|5.5.5.5|10.0.0.16|255.255.255.252|
|LEAF3|5.5.5.5|10.0.0.20|255.255.255.252|

### Необходимые настройки на оборудовании:

#### <u>Настройка SpineL:</u>
```
feature ospf
router ospf UNDERLAY
  router-id 1.1.1.1
  passive-interface default

interface loopback1
  ip address 1.1.1.1/32

interface Ethernet1/1
  ip address 10.1.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/2
  ip address 10.1.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/3
  ip address 10.1.0.4/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

```
#### <u>Настройка SpineR:</u>
```
feature ospf
router ospf UNDERLAY
  router-id 2.2.2.2

interface loopback1
  ip address 2.2.2.2/32
  passive-interface default

interface Ethernet1/1
  ip address 10.2.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/2
  ip address 10.2.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/3
  ip address 10.2.0.0/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown
```
#### <u>Настройка LEAF1:</u>
```
feature ospf
router ospf UNDERLAY
  router-id 3.3.3.3
  passive-interface default

interface loopback1
  ip address 3.3.3.3/32
  passive-interface default

interface Ethernet1/1
  ip address 10.1.0.1/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/2
  ip address 10.2.0.1/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown
```
#### <u>Настройка LEAF2:</u>
```
feature ospf
router ospf UNDERLAY
  router-id 4.4.4.4
  passive-interface default

interface loopback1
  ip address 4.4.4.4/32
  passive-interface default  

interface Ethernet1/1
  ip address 10.1.0.3/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/2
  ip address 10.2.0.3/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown
```
#### <u>Настройка LEAF3:</u>
```
feature ospf
router ospf UNDERLAY
  router-id 5.5.5.5
  passive-interface default

interface loopback1
  ip address 5.5.5.5/32
  passive-interface default

interface Ethernet1/1
  ip address 10.1.0.5/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown

interface Ethernet1/2
  ip address 10.2.0.5/31
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf Underlay area 0.0.0.0
  no shutdown
```
### Проверка работоспособности OSPF в сети. Проверяем соседство, таблицу маршрутизации и LSDB:

<details>
<summary>Проверка на SpineL</summary>

```
SpineL# sh ip ospf neighbors 
 OSPF Process ID Underlay VRF default
 Total number of neighbors: 3
 Neighbor ID     Pri State            Up Time  Address         Interface
 3.3.3.3           1 FULL/ -          00:04:45 10.1.0.1        Eth1/1 
 4.4.4.4           1 FULL/ -          00:04:31 10.1.0.3        Eth1/2 
 5.5.5.5           1 FULL/ -          00:04:30 10.1.0.5        Eth1/3

SpineL# sh ip route 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

1.1.1.1/32, ubest/mbest: 2/0, attached
    *via 1.1.1.1, Lo1, [0/0], 00:12:41, local
    *via 1.1.1.1, Lo1, [0/0], 00:12:41, direct
10.1.0.0/31, ubest/mbest: 1/0, attached
    *via 10.1.0.0, Eth1/1, [0/0], 00:07:56, direct
10.1.0.0/32, ubest/mbest: 1/0, attached
    *via 10.1.0.0, Eth1/1, [0/0], 00:07:56, local
10.1.0.2/31, ubest/mbest: 1/0, attached
    *via 10.1.0.2, Eth1/2, [0/0], 00:07:50, direct
10.1.0.2/32, ubest/mbest: 1/0, attached
    *via 10.1.0.2, Eth1/2, [0/0], 00:07:50, local
10.1.0.4/31, ubest/mbest: 1/0, attached
    *via 10.1.0.4, Eth1/3, [0/0], 00:07:43, direct
10.1.0.4/32, ubest/mbest: 1/0, attached
    *via 10.1.0.4, Eth1/3, [0/0], 00:07:43, local
10.2.0.0/31, ubest/mbest: 1/0
    *via 10.1.0.1, Eth1/1, [110/80], 00:07:44, ospf-Underlay, intra
10.2.0.2/31, ubest/mbest: 1/0
    *via 10.1.0.3, Eth1/2, [110/80], 00:07:36, ospf-Underlay, intra
10.2.0.4/31, ubest/mbest: 1/0
    *via 10.1.0.5, Eth1/3, [110/80], 00:07:31, ospf-Underlay, intra


SpineL# sh ip ospf database 
        OSPF Router with ID (1.1.1.1) (Process ID Underlay VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
1.1.1.1         1.1.1.1         627        0x80000007 0x6925   6   
2.2.2.2         2.2.2.2         764        0x80000009 0x0a74   6   
3.3.3.3         3.3.3.3         647        0x8000000b 0x32f7   4   
4.4.4.4         4.4.4.4         633        0x80000005 0xe23d   4   
5.5.5.5         5.5.5.5         632        0x80000005 0x8788   4 
```
</details>
<details>
<summary>Проверка на SpineR</summary>

```
SpineR# sh ip ospf neighbors 
 OSPF Process ID Underlay VRF default
 Total number of neighbors: 3
 Neighbor ID     Pri State            Up Time  Address         Interface
 3.3.3.3           1 FULL/ -          00:16:01 10.2.0.1        Eth1/1 
 4.4.4.4           1 FULL/ -          00:30:51 10.2.0.3        Eth1/2 
 5.5.5.5           1 FULL/ -          00:08:41 10.2.0.5        Eth1/3 

SpineR# sh ip route 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

2.2.2.2/32, ubest/mbest: 2/0, attached
    *via 2.2.2.2, Lo1, [0/0], 01:20:16, local
    *via 2.2.2.2, Lo1, [0/0], 01:20:16, direct
10.1.0.0/31, ubest/mbest: 1/0
    *via 10.2.0.1, Eth1/1, [110/80], 00:21:26, ospf-Underlay, intra
10.1.0.2/31, ubest/mbest: 1/0
    *via 10.2.0.3, Eth1/2, [110/80], 00:36:16, ospf-Underlay, intra
10.1.0.4/31, ubest/mbest: 1/0
    *via 10.2.0.5, Eth1/3, [110/80], 00:14:05, ospf-Underlay, intra
10.2.0.0/31, ubest/mbest: 1/0, attached
    *via 10.2.0.0, Eth1/1, [0/0], 01:14:51, direct
10.2.0.0/32, ubest/mbest: 1/0, attached
    *via 10.2.0.0, Eth1/1, [0/0], 01:14:51, local
10.2.0.2/31, ubest/mbest: 1/0, attached
    *via 10.2.0.2, Eth1/2, [0/0], 01:14:47, direct
10.2.0.2/32, ubest/mbest: 1/0, attached
    *via 10.2.0.2, Eth1/2, [0/0], 01:14:47, local
10.2.0.4/31, ubest/mbest: 1/0, attached
    *via 10.2.0.4, Eth1/3, [0/0], 01:14:41, direct
10.2.0.4/32, ubest/mbest: 1/0, attached
    *via 10.2.0.4, Eth1/3, [0/0], 01:14:41, local

	
SpineR# sh ip ospf database 
        OSPF Router with ID (2.2.2.2) (Process ID Underlay VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
1.1.1.1         1.1.1.1         746        0x80000007 0x6925   6   
2.2.2.2         2.2.2.2         879        0x80000009 0x0a74   6   
3.3.3.3         3.3.3.3         763        0x8000000b 0x32f7   4   
4.4.4.4         4.4.4.4         750        0x80000005 0xe23d   4   
5.5.5.5         5.5.5.5         749        0x80000005 0x8788   4   
```
</details>
<details>
<summary>Проверка на LEAF1</summary>

```
LEAF1# show ip ospf neighbors 
 OSPF Process ID Underlay VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 1.1.1.1           1 FULL/ -          00:13:14 10.1.0.0        Eth1/1 
 2.2.2.2           1 FULL/ -          00:22:31 10.2.0.0        Eth1/2

LEAF1# sh ip route 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

3.3.3.3/32, ubest/mbest: 2/0, attached
    *via 3.3.3.3, Lo1, [0/0], 00:26:59, local
    *via 3.3.3.3, Lo1, [0/0], 00:26:59, direct
10.1.0.0/31, ubest/mbest: 1/0, attached
    *via 10.1.0.1, Eth1/1, [0/0], 00:23:19, direct
10.1.0.1/32, ubest/mbest: 1/0, attached
    *via 10.1.0.1, Eth1/1, [0/0], 00:23:19, local
10.1.0.2/31, ubest/mbest: 1/0
    *via 10.1.0.0, Eth1/1, [110/80], 00:13:37, ospf-Underlay, intra
10.1.0.4/31, ubest/mbest: 1/0
    *via 10.1.0.0, Eth1/1, [110/80], 00:13:35, ospf-Underlay, intra
10.2.0.0/31, ubest/mbest: 1/0, attached
    *via 10.2.0.1, Eth1/2, [0/0], 00:23:09, direct
10.2.0.1/32, ubest/mbest: 1/0, attached
    *via 10.2.0.1, Eth1/2, [0/0], 00:23:09, local
10.2.0.2/31, ubest/mbest: 1/0
    *via 10.2.0.0, Eth1/2, [110/80], 00:22:53, ospf-Underlay, intra
10.2.0.4/31, ubest/mbest: 1/0
    *via 10.2.0.0, Eth1/2, [110/80], 00:22:53, ospf-Underlay, intra
172.16.1.0/24, ubest/mbest: 1/0, attached
    *via 172.16.1.1, Eth1/4, [0/0], 00:11:22, direct
172.16.1.1/32, ubest/mbest: 1/0, attached
    *via 172.16.1.1, Eth1/4, [0/0], 00:11:22, local
	
LEAF1# sh ip ospf database 
        OSPF Router with ID (3.3.3.3) (Process ID Underlay VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
1.1.1.1         1.1.1.1         823        0x80000007 0x6925   6   
2.2.2.2         2.2.2.2         959        0x80000009 0x0a74   6   
3.3.3.3         3.3.3.3         841        0x8000000b 0x32f7   4   
4.4.4.4         4.4.4.4         829        0x80000005 0xe23d   4   
5.5.5.5         5.5.5.5         828        0x80000005 0x8788   4 
```
</details>
<details>
<summary>Проверка на LEAF2</summary>

```
LEAF2# sh ip ospf neighbors 
 OSPF Process ID Underlay VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 1.1.1.1           1 FULL/ -          00:14:10 10.1.0.2        Eth1/1 
 2.2.2.2           1 FULL/ -          00:38:31 10.2.0.2        Eth1/2


LEAF2# sh ip route 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

4.4.4.4/32, ubest/mbest: 2/0, attached
    *via 4.4.4.4, Lo1, [0/0], 00:43:01, local
    *via 4.4.4.4, Lo1, [0/0], 00:43:01, direct
10.1.0.0/31, ubest/mbest: 1/0
    *via 10.1.0.2, Eth1/1, [110/80], 00:14:25, ospf-Underlay, intra
10.1.0.2/31, ubest/mbest: 1/0, attached
    *via 10.1.0.3, Eth1/1, [0/0], 00:39:00, direct
10.1.0.3/32, ubest/mbest: 1/0, attached
    *via 10.1.0.3, Eth1/1, [0/0], 00:39:00, local
10.1.0.4/31, ubest/mbest: 1/0
    *via 10.1.0.2, Eth1/1, [110/80], 00:14:25, ospf-Underlay, intra
10.2.0.0/31, ubest/mbest: 1/0
    *via 10.2.0.2, Eth1/2, [110/80], 00:38:40, ospf-Underlay, intra
10.2.0.2/31, ubest/mbest: 1/0, attached
    *via 10.2.0.3, Eth1/2, [0/0], 00:38:55, direct
10.2.0.3/32, ubest/mbest: 1/0, attached
    *via 10.2.0.3, Eth1/2, [0/0], 00:38:55, local
10.2.0.4/31, ubest/mbest: 1/0
    *via 10.2.0.2, Eth1/2, [110/80], 00:38:40, ospf-Underlay, intra
172.16.2.0/24, ubest/mbest: 1/0, attached
    *via 172.16.2.1, Eth1/4, [0/0], 00:11:46, direct
172.16.2.1/32, ubest/mbest: 1/0, attached
    *via 172.16.2.1, Eth1/4, [0/0], 00:11:46, local
	
LEAF2# sh ip ospf database 
        OSPF Router with ID (4.4.4.4) (Process ID Underlay VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
1.1.1.1         1.1.1.1         898        0x80000007 0x6925   6   
2.2.2.2         2.2.2.2         1034       0x80000009 0x0a74   6   
3.3.3.3         3.3.3.3         918        0x8000000b 0x32f7   4   
4.4.4.4         4.4.4.4         902        0x80000005 0xe23d   4   
5.5.5.5         5.5.5.5         903        0x80000005 0x8788   4 
```
</details>
<details>
<summary>Проверка на LEAF3</summary>

```
LEAF3# sh ip ospf neighbors 
 OSPF Process ID Underlay VRF default
 Total number of neighbors: 2
 Neighbor ID     Pri State            Up Time  Address         Interface
 1.1.1.1           1 FULL/ -          00:15:29 10.1.0.4        Eth1/1 
 2.2.2.2           1 FULL/ -          00:17:40 10.2.0.4        Eth1/2

LEAF3# sh ip route 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

5.5.5.5/32, ubest/mbest: 2/0, attached
    *via 5.5.5.5, Lo1, [0/0], 00:35:09, local
    *via 5.5.5.5, Lo1, [0/0], 00:35:09, direct
10.1.0.0/31, ubest/mbest: 1/0
    *via 10.1.0.4, Eth1/1, [110/80], 00:15:58, ospf-Underlay, intra
10.1.0.2/31, ubest/mbest: 1/0
    *via 10.1.0.4, Eth1/1, [110/80], 00:15:58, ospf-Underlay, intra
10.1.0.4/31, ubest/mbest: 1/0, attached
    *via 10.1.0.5, Eth1/1, [0/0], 00:18:28, direct
10.1.0.5/32, ubest/mbest: 1/0, attached
    *via 10.1.0.5, Eth1/1, [0/0], 00:18:28, local
10.2.0.0/31, ubest/mbest: 1/0
    *via 10.2.0.4, Eth1/2, [110/80], 00:18:07, ospf-Underlay, intra
10.2.0.2/31, ubest/mbest: 1/0
    *via 10.2.0.4, Eth1/2, [110/80], 00:18:07, ospf-Underlay, intra
10.2.0.4/31, ubest/mbest: 1/0, attached
    *via 10.2.0.5, Eth1/2, [0/0], 00:18:22, direct
10.2.0.5/32, ubest/mbest: 1/0, attached
    *via 10.2.0.5, Eth1/2, [0/0], 00:18:22, local
172.16.3.0/24, ubest/mbest: 1/0, attached
    *via 172.16.3.1, Eth1/4, [0/0], 00:00:06, direct
172.16.3.1/32, ubest/mbest: 1/0, attached
    *via 172.16.3.1, Eth1/4, [0/0], 00:00:06, local
172.16.4.0/24, ubest/mbest: 1/0, attached
    *via 172.16.4.1, Eth1/5, [0/0], 00:17:59, direct
172.16.4.1/32, ubest/mbest: 1/0, attached
    *via 172.16.4.1, Eth1/5, [0/0], 00:17:59, local
	
	
LEAF3# sh ip ospf database 
        OSPF Router with ID (5.5.5.5) (Process ID Underlay VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
1.1.1.1         1.1.1.1         999        0x80000007 0x6925   6   
2.2.2.2         2.2.2.2         1134       0x80000009 0x0a74   6   
3.3.3.3         3.3.3.3         1019       0x8000000b 0x32f7   4   
4.4.4.4         4.4.4.4         1005       0x80000005 0xe23d   4   
5.5.5.5         5.5.5.5         1002       0x80000005 0x8788   4 
```
</details>

ICMP-проверка:

<details>
<summary>Проверка на SpineL</summary>

```
SpineL# ping 10.2.0.0
PING 10.2.0.0 (10.2.0.0): 56 data bytes
64 bytes from 10.2.0.0: icmp_seq=0 ttl=253 time=13.206 ms
64 bytes from 10.2.0.0: icmp_seq=1 ttl=253 time=6.231 ms
64 bytes from 10.2.0.0: icmp_seq=2 ttl=253 time=6.722 ms
64 bytes from 10.2.0.0: icmp_seq=3 ttl=253 time=4.102 ms
64 bytes from 10.2.0.0: icmp_seq=4 ttl=253 time=4.668 ms

--- 10.2.0.0 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.102/6.985/13.206 ms
SpineL# ping 10.2.0.1
PING 10.2.0.1 (10.2.0.1): 56 data bytes
64 bytes from 10.2.0.1: icmp_seq=0 ttl=254 time=4.217 ms
64 bytes from 10.2.0.1: icmp_seq=1 ttl=254 time=3.434 ms
64 bytes from 10.2.0.1: icmp_seq=2 ttl=254 time=2.812 ms
64 bytes from 10.2.0.1: icmp_seq=3 ttl=254 time=2.661 ms
64 bytes from 10.2.0.1: icmp_seq=4 ttl=254 time=2.281 ms

--- 10.2.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.281/3.08/4.217 ms

SpineL# ping 10.2.0.2
PING 10.2.0.2 (10.2.0.2): 56 data bytes
64 bytes from 10.2.0.2: icmp_seq=0 ttl=253 time=9.515 ms
64 bytes from 10.2.0.2: icmp_seq=1 ttl=253 time=5.821 ms
64 bytes from 10.2.0.2: icmp_seq=2 ttl=253 time=6.096 ms
64 bytes from 10.2.0.2: icmp_seq=3 ttl=253 time=4.952 ms
64 bytes from 10.2.0.2: icmp_seq=4 ttl=253 time=5.837 ms

--- 10.2.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.952/6.444/9.515 ms

SpineL# ping 10.2.0.3
PING 10.2.0.3 (10.2.0.3): 56 data bytes
64 bytes from 10.2.0.3: icmp_seq=0 ttl=254 time=3.712 ms
64 bytes from 10.2.0.3: icmp_seq=1 ttl=254 time=4.416 ms
64 bytes from 10.2.0.3: icmp_seq=2 ttl=254 time=4.101 ms
64 bytes from 10.2.0.3: icmp_seq=3 ttl=254 time=3.397 ms
64 bytes from 10.2.0.3: icmp_seq=4 ttl=254 time=3.635 ms

--- 10.2.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.397/3.852/4.416 ms

SpineL# ping 10.2.0.4
PING 10.2.0.4 (10.2.0.4): 56 data bytes
64 bytes from 10.2.0.4: icmp_seq=0 ttl=253 time=48.942 ms
64 bytes from 10.2.0.4: icmp_seq=1 ttl=253 time=7.014 ms
64 bytes from 10.2.0.4: icmp_seq=2 ttl=253 time=6.022 ms
64 bytes from 10.2.0.4: icmp_seq=3 ttl=253 time=5.822 ms
64 bytes from 10.2.0.4: icmp_seq=4 ttl=253 time=5.289 ms

--- 10.2.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 5.289/14.617/48.942 ms

SpineL# ping 10.2.0.5
PING 10.2.0.5 (10.2.0.5): 56 data bytes
64 bytes from 10.2.0.5: icmp_seq=0 ttl=254 time=3.71 ms
64 bytes from 10.2.0.5: icmp_seq=1 ttl=254 time=2.731 ms
64 bytes from 10.2.0.5: icmp_seq=2 ttl=254 time=2.484 ms
64 bytes from 10.2.0.5: icmp_seq=3 ttl=254 time=2.946 ms
64 bytes from 10.2.0.5: icmp_seq=4 ttl=254 time=5.962 ms

--- 10.2.0.5 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.484/3.566/5.962 ms
```

</details>

<details>
<summary>Проверка на SpineR</summary>

```
SpineR# ping 10.1.0.0
PING 10.1.0.0 (10.1.0.0): 56 data bytes
64 bytes from 10.1.0.0: icmp_seq=0 ttl=253 time=15.665 ms
64 bytes from 10.1.0.0: icmp_seq=1 ttl=253 time=7.562 ms
64 bytes from 10.1.0.0: icmp_seq=2 ttl=253 time=9.206 ms
64 bytes from 10.1.0.0: icmp_seq=3 ttl=253 time=5.455 ms
64 bytes from 10.1.0.0: icmp_seq=4 ttl=253 time=4.157 ms

--- 10.1.0.0 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.157/8.408/15.665 ms
SpineR# ping 10.1.0.1
PING 10.1.0.1 (10.1.0.1): 56 data bytes
64 bytes from 10.1.0.1: icmp_seq=0 ttl=254 time=3.242 ms
64 bytes from 10.1.0.1: icmp_seq=1 ttl=254 time=2.885 ms
64 bytes from 10.1.0.1: icmp_seq=2 ttl=254 time=3.114 ms
64 bytes from 10.1.0.1: icmp_seq=3 ttl=254 time=2.264 ms
64 bytes from 10.1.0.1: icmp_seq=4 ttl=254 time=2.366 ms

--- 10.1.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.264/2.774/3.242 ms
SpineR# ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=253 time=7.269 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=253 time=5.222 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=253 time=4.964 ms
64 bytes from 10.1.0.2: icmp_seq=3 ttl=253 time=5.462 ms
64 bytes from 10.1.0.2: icmp_seq=4 ttl=253 time=4.826 ms

--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 4.826/5.548/7.269 ms

SpineR# ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3): 56 data bytes
64 bytes from 10.1.0.3: icmp_seq=0 ttl=254 time=4.306 ms
64 bytes from 10.1.0.3: icmp_seq=1 ttl=254 time=4.018 ms
64 bytes from 10.1.0.3: icmp_seq=2 ttl=254 time=4.407 ms
64 bytes from 10.1.0.3: icmp_seq=3 ttl=254 time=3.479 ms
64 bytes from 10.1.0.3: icmp_seq=4 ttl=254 time=3.608 ms

--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.479/3.963/4.407 ms

SpineR# ping 10.1.0.4
PING 10.1.0.4 (10.1.0.4): 56 data bytes
64 bytes from 10.1.0.4: icmp_seq=0 ttl=253 time=7.263 ms
64 bytes from 10.1.0.4: icmp_seq=1 ttl=253 time=4.217 ms
64 bytes from 10.1.0.4: icmp_seq=2 ttl=253 time=4.12 ms
64 bytes from 10.1.0.4: icmp_seq=3 ttl=253 time=3.966 ms
64 bytes from 10.1.0.4: icmp_seq=4 ttl=253 time=4.404 ms

--- 10.1.0.4 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.966/4.793/7.263 ms
SpineR# ping 10.1.0.5
PING 10.1.0.5 (10.1.0.5): 56 data bytes
64 bytes from 10.1.0.5: icmp_seq=0 ttl=254 time=3.796 ms
64 bytes from 10.1.0.5: icmp_seq=1 ttl=254 time=3.816 ms
64 bytes from 10.1.0.5: icmp_seq=2 ttl=254 time=2.861 ms
64 bytes from 10.1.0.5: icmp_seq=3 ttl=254 time=2.632 ms
64 bytes from 10.1.0.5: icmp_seq=4 ttl=254 time=2.488 ms

--- 10.1.0.5 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.488/3.118/3.816 ms

```
</details>
Как видно из выводов команд, L3-коммутаторы установили соседство и обмениваются маршрутной информацией. Также прилагаются полные конфигурационные файлы всего оборудования сети.