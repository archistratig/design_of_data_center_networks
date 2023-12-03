## Проектная работа "Проектирование распределенной сети ЦОД"

### Оглавление
1. [Цель](#цель)
2. [Задачи](#задачи)
3. [Физическая схема](#физическая-схема-сети)
4. [ЦОД ТАИ](#цод-таи)
    - [Адресация для построения сети Underlay](#адресация-для-построения-сети-underlay)
    - [Адресация для NVE-интерфейсов](#адресация-для-nve-интерфейсов)
    - [Сетевые зоны](#сетевые-зоны)
    - [Настройка сети Undrelay](#настройка-сети-undrelay)
    - [Настройка сети Overlay](#настройка-сети-overlay)
5. [ЦОД ОИС](#цод-оис)
    - [Адресация для построения сети Underlay](#d0b0d0b4d180d0b5d181d0b0d186d0b8d18f-d0b4d0bbd18f-d0bfd0bed181d182d180d0bed0b5d0bdd0b8d18f-d181d0b5d182d0b8-underlay-1)
    - [Адресация для NVE-интерфейсов](#d0b0d0b4d180d0b5d181d0b0d186d0b8d18f-d0b4d0bbd18f-nve-d0b8d0bdd182d0b5d180d184d0b5d0b9d181d0bed0b2-1)
    - [Сетевые зоны](#сетевые-зоны)
    - [Настройка сети Underlay](#d0bdd0b0d181d182d180d0bed0b9d0bad0b0-d181d0b5d182d0b8-undrelay-1)
    - [Настройка сети Overlay](#d0bdd0b0d181d182d180d0bed0b9d0bad0b0-d181d0b5d182d0b8-overlay-1)
6. [Настройка Настройка Super-Spine](#Настройка-Super-Spine)
7. [Проверка работоспособности](#проверка-работоспособности)

### Цель:
- Разработка архитектуры сетей ЦОД с использованием VXLAN\EVPN с целью миграции серверного сегмента из сети с традиционной архитектурой

### Задачи:
- В связи с заменой оборудования и перехода с классической tier-3 архитектуры, была выбрана топология 5-stage Clos.
- Проектирование отказоустойчивой и масштабируемой 5-stage топологии Clos для двух ЦОД (c возможностью расширения)
- Проектирование DCI interconnection c использование двух super-spine между площадками
- Проектирование адресного пространства
- Проектирование сетевых зон и взаимодействия с удаленным ЦОД через 2 пограничных коммутатор ЦОД с использование технологии MLag.

### Физическая схема сети:
Проектируемые ЦОДы будут находиться на территории одного предприятия,  (AS 4200000002) ЦОД ТАИ и ЦОД ОИС (AS 4200000001). Соединять их планируем через 2 super-spine с AS 4200000000. Пограничные коммутаторы находятся для связи с удаленным ЦОД находятся в AS AS 4200000003

![Схема](Network_topology.jpg)

### ЦОД ТАИ

#### Адресация для построения сети Underlay
Физическим интерфейсам зададим адресацию согласно данной таблицы:
|Device|Interface|IP Address|
|:-:|:-:|:-:|
|DC1-SpineL|Ethernet1|10.3.255.0/31|
|DC1-SpineL|Ethernet2|10.3.255.2/31|
|DC1-SpineL|Ethernet3|10.3.255.4/31|
|DC1-SpineL|Ethernet4|10.3.255.121/31|
|DC1-SpineL|Ethernet5|10.3.255.133/31|
|DC1-SpineR|Ethernet1|10.3.255.14/31|
|DC1-SpineR|Ethernet2|10.3.255.16/31|
|DC1-SpineR|Ethernet3|10.3.255.18/31|
|DC1-SpineR|Ethernet4|10.3.255.123/31|
|DC1-SpineR|Ethernet5|10.3.255.135/31|
|DC1-LEAF1|Ethernet1|10.3.255.1/31|
|DC1-LEAF1|Ethernet2|10.3.255.15/31|
|DC1-LEAF2|Ethernet1|10.3.255.3/31|
|DC1-LEAF2|Ethernet2|10.3.255.17/31|
|DC1-LEAF3|Ethernet1|10.3.255.5/31|
|DC1-LEAF3|Ethernet2|10.3.255.19/31|
|Edge-LEAF1|Ethernet1|10.3.255.141/31|
|Edge-LEAF1|Ethernet2|10.3.255.145/31|
|Edge-LEAF1|Ethernet3|10.110.254.250/31|
|Edge-LEAF2|Ethernet1|10.3.255.143/31|
|Edge-LEAF2|Ethernet2|10.3.255.147/31|
|Edge-LEAF2|Ethernet3|10.110.254.252/31|

#### Адресация для Multihoming DC1-LEAF1-DC1-LEAF2
|Device|Interface|IP Address|
|:-:|:-:|:-:|
|DC1-LEAF2|Ethernet6|10.3.255.200/31|
|DC1-LEAF2|vlan4093|10.3.255.253/31|
|DC1-LEAF3|Ethernet6|10.3.255.201/31|
|DC1-LEAF3|vlan4093|10.3.255.252/31|

#### Адресация для Multihoming Edge-LEAF1-Edge-LEAF2
|Device|Interface|IP Address|
|:-:|:-:|:-:|
|Edge-LEAF1|Ethernet3|10.3.254.240/31|
|Edge-LEAF1|vlan4093|10.3.255.253/31|
|Edge-LEAF2|Ethernet3|10.3.254.241/31|
|Edge-LEAF2|vlan4093|10.3.254.252/31|

#### Адресация для NVE-интерфейсов

|Device|Interface|IP Address|
|:-:|:-:|:-:|
|DC1-LEAF1|Loopback 1|10.3.255.180/32|
|DC1-LEAF2|Loopback 1|10.3.255.181/32|
|DC1-LEAF3|Loopback 1|10.3.255.182/32|
|Edge-LEAF1|Loopback 1|10.3.254.253/32|
|Edge-LEAF2|Loopback 1|10.3.254.254/32|


#### Сетевые зоны
Зоны одинаковы на DC1-LEAF1, DC1-LEAF2, DC1-LEAF3.
VNI 29999 используется для создания L3VNI, растянут на всю Multi-POD фабрику, в дальнейшем будут добавлены VRF для технологических сетей.
|Network|Gateway / Interface VLAN IP Address|VLAN ID|VLAN Name|VRF|VNI|Description|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|10.3.10.0/24|10.3.10.254|10|10-DataStorage|NET|10010|Сеть для размещения систем хранения|
|10.3.11.0/24|10.3.11.254|11|11-ТАИ|NET|10011|Сеть для размещения узлов ТАИ|
|10.3.20.0/24|10.3.20.254|20|20-Back+Front|NET|10020|Сеть для размещения узлов с веб-серверами|
|10.3.30.0/24|10.3.30.254|30|30-QA|NET|10030|Сеть тестирования|


#### Настройка сети Undrelay
Внутри автономной системы используем протокол OSPF
#### DC1-LEAF1
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.15/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

 router ospf 1
   router-id 10.3.253.3
   max-lsa 12000 
```
#### DC1-LEAF2
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.17/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

router ospf 1
   router-id 10.3.253.4
   max-lsa 12000   
```
#### DC1-LEAF3
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.19/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

router ospf 1
   router-id 10.3.253.5
   max-lsa 12000   
```

#### Edge-LEAF1
```
interface Ethernet1
   no switchport
   ip address 10.3.255.143/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   no switchport
   ip address 10.3.255.147/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet3
   no switchport
   vrf NET
   ip address 10.110.254.252/31

router ospf 1
   router-id 10.3.253.60
   max-lsa 12000
```
#### Edge-LEAF2
```
interface Ethernet1
   no switchport
   ip address 10.3.255.143/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   no switchport
   ip address 10.3.255.147/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet3
   no switchport
   vrf NET
   ip address 10.110.254.252/31

router ospf 1
   router-id 10.3.253.60
   max-lsa 12000
```
#### Настройка сети Overlay
#### Настройка сетевых зон на DC1-LEAF1
```
vlan 10-11,20,30
vrf instance NET

interface Vlan10
   vrf NET
   ip address virtual 10.3.10.254/24

interface Vlan11
   vrf NET
   ip address virtual 10.3.11.254/24

interface Vlan20
   vrf NET
   ip address virtual 10.3.20.254/24

interface Vlan30
   vrf NET
   ip address virtual 10.3.30.254/24

interface Ethernet3
   switchport access vlan 10

interface Ethernet4
   switchport access vlan 20
```
#### Настройка EVPN на DC1-LEAF1
```
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 0.39.26
   vxlan vlan 11 vni 0.39.27
   vxlan vlan 20 vni 0.39.36
   vxlan vlan 30 vni 0.39.46
   vxlan vrf NET vni 0.117.47
   vxlan learn-restrict any

ip virtual-router mac-address 00:a1:a1:a1:a1:a1

router bgp 4200000001
   router-id 10.3.253.3
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000001
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community extended
   neighbor 10.3.253.1 peer group evpn_spine
   neighbor 10.3.253.2 peer group evpn_spine
   
   vlan 10
      rd 10.3.253.3:10010
      route-target both 4200000001:10010
      redistribute learned
   
   vlan 11
      rd 10.3.253.3:10011
      route-target both 10011:10011
      route-target both 4200000001:10011
      redistribute learned
   
   vlan 20
      rd 10.3.253.3:10020
      route-target both 10020:10020
      route-target both 4200000001:10020
      redistribute learned
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
   
   address-family ipv4
      no neighbor evpn_spine activate
   
   vrf NET
      rd 10.3.253.3:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected
```
#### Настройка сетевых зон на DC1-LEAF2
```
vlan 10-11,20,30

vrf instance NET

interface Vlan10
   vrf NET
   ip address virtual 10.3.10.254/24

interface Vlan11
   vrf NET
   ip address virtual 10.3.11.254/24

interface Vlan20
   vrf NET
   ip address virtual 10.3.20.254/24

interface Vlan30
   vrf NET
   ip address virtual 10.3.30.254/24

Mlag:
no spanning-tree vlan-id 4093
vlan 4093
   trunk group PEER-LINK
   vrf instance mlag-keep-alive

mlag configuration
   domain-id POD1-leafs
   local-interface Vlan4093
   peer-address 10.3.255.252
   peer-address heartbeat 10.3.255.201 vrf mlag-keep-alive
   peer-link Port-Channel1000
   dual-primary detection delay 10 action errdisable all-interfaces
   reload-delay mlag 60
   reload-delay non-mlag 30

interface Port-Channel1
   description MLag-link
   switchport mode trunk
   mlag 1

interface Port-Channel1000
   description Mlag-Peer
   switchport mode trunk
   switchport trunk group PEER-LINK
interface Ethernet6
   no switchport
   vrf mlag-keep-alive
   ip address 10.3.255.200/31

interface Ethernet7
   description POD1-L1-POD1-L2-MLag
   channel-group 1000 mode active

interface Ethernet8
   description POD1-L1-POD1-L2-MLag
   channel-group 1000 mode active

```
#### Настройка EVPN на DC1-LEAF2
```
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 0.39.26
   vxlan vlan 11 vni 0.39.27
   vxlan vlan 20 vni 0.39.36
   vxlan vlan 30 vni 0.39.46
   vxlan vrf NET vni 0.117.47
   vxlan learn-restrict any

ip virtual-router mac-address 00:a1:a1:a1:a1:a1

router bgp 4200000001
   router-id 10.3.253.4
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000001
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community extended
   neighbor 10.3.253.1 peer group evpn_spine
   neighbor 10.3.253.2 peer group evpn_spine
   !
   vlan 10
      rd 10.3.253.3:10010
      route-target both 4200000001:10010
      redistribute learned
   !
   vlan 11
      rd 10.3.253.3:10011
      route-target both 10011:10011
      route-target both 4200000001:10011
      redistribute learned
   !
   vlan 20
      rd 10.3.253.3:10020
      route-target both 10020:10020
      route-target both 4200000001:10020
      redistribute learned
   !
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
      graceful-restart
   !
   address-family ipv4
      no neighbor evpn_spine activate
   !
   vrf NET
      rd 10.3.253.4:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected

```
#### Настройка сетевых зон на DC1-LEAF3
```
vlan 10-11,20,30

vrf instance NET

interface Vlan10
   vrf NET
   ip address virtual 10.3.10.254/24

interface Vlan11
   vrf NET
   ip address virtual 10.3.11.254/24

interface Vlan20
   vrf NET
   ip address virtual 10.3.20.254/24

interface Vlan30
   vrf NET
   ip address virtual 10.3.30.254/24

Mlag:
no spanning-tree vlan-id 4093
vlan 4093
   trunk group PEER-LINK

vrf instance mlag-keep-alive

mlag configuration
   domain-id POD1-leafs
   local-interface Vlan4093
   peer-address 10.3.255.253
   peer-address heartbeat 10.3.255.200 vrf mlag-keep-alive
   peer-link Port-Channel1000
   dual-primary detection delay 10 action errdisable all-interfaces
   reload-delay mlag 60
   reload-delay non-mlag 30

interface Port-Channel1
   description MLag-link
   switchport mode trunk
   mlag 1

interface Port-Channel1000
   description Mlag-Peer
   switchport mode trunk
   switchport trunk group PEER-LINK
interface Ethernet6
   no switchport
   vrf mlag-keep-alive
   ip address 10.3.255.201/31

interface Ethernet7
   description POD1-L1-POD1-L2-MLag
   channel-group 1000 mode active

interface Ethernet8
   description POD1-L1-POD1-L2-MLag
   channel-group 1000 mode active

```
#### Настройка EVPN на DC1-LEAF3
```
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 0.39.126
   vxlan vlan 11 vni 0.39.127
   vxlan vlan 20 vni 0.39.136
   vxlan vlan 30 vni 0.39.146
   vxlan vrf NET vni 0.117.47
   vxlan learn-restrict any

ip virtual-router mac-address 00:a1:a1:a1:a1:a1
router bgp 4200000001
   router-id 10.3.253.5
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000001
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community extended
   neighbor 10.3.253.1 peer group evpn_spine
   neighbor 10.3.253.2 peer group evpn_spine
   !
   vlan 10
      rd 10.3.253.3:10010
      route-target both 4200000001:10010
      redistribute learned
   
   vlan 11
      rd 10.3.253.3:10011
      route-target both 4200000001:10011
      redistribute learned
   
   vlan 20
      rd 10.3.253.3:10020
      route-target both 4200000001:10020
      redistribute learned
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
   
   address-family ipv4
      no neighbor evpn_spine activate
   
   vrf NET
      rd 10.3.253.5:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected

```
#### Настройка EVPN на DC1-SpineL
```
router bgp 4200000001
   router-id 10.3.253.1
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_leaf peer group
   neighbor evpn_leaf remote-as 4200000001
   neighbor evpn_leaf bfd
   neighbor evpn_leaf route-reflector-client
   neighbor evpn_leaf send-community
   neighbor evpn_superspine peer group
   neighbor evpn_superspine remote-as 4200000000
   neighbor evpn_superspine next-hop-unchanged
   neighbor evpn_superspine update-source Loopback0
   neighbor evpn_superspine bfd
   neighbor evpn_superspine ebgp-multihop 16
   neighbor evpn_superspine send-community extended
   neighbor evpn_superspine maximum-routes 12000 warning-only
   neighbor 10.3.253.3 peer group evpn_leaf
   neighbor 10.3.253.4 peer group evpn_leaf
   neighbor 10.3.253.5 peer group evpn_leaf
   neighbor 10.3.253.50 peer group evpn_superspine
   neighbor 10.3.253.51 peer group evpn_superspine
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_leaf activate
      neighbor evpn_superspine activate
   
   address-family ipv4
      no neighbor evpn_leaf activate
      no neighbor evpn_superspine activate

router ospf 1
   router-id 10.3.253.1
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   no passive-interface Ethernet5
   max-lsa 12000
```
#### Настройка EVPN на DC1-SpineR
```
router bgp 4200000001
   router-id 10.3.253.2
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn_leaf peer group
   neighbor evpn_leaf remote-as 4200000001
   neighbor evpn_leaf bfd
   neighbor evpn_leaf route-reflector-client
   neighbor evpn_leaf send-community
   neighbor evpn_spine peer group
   neighbor evpn_superspine peer group
   neighbor evpn_superspine remote-as 4200000000
   neighbor evpn_superspine next-hop-unchanged
   neighbor evpn_superspine update-source Loopback0
   neighbor evpn_superspine bfd
   neighbor evpn_superspine ebgp-multihop 16
   neighbor evpn_superspine send-community extended
   neighbor evpn_superspine maximum-routes 12000 warning-only
   neighbor 10.3.253.3 peer group evpn_leaf
   neighbor 10.3.253.4 peer group evpn_leaf
   neighbor 10.3.253.5 peer group evpn_leaf
   neighbor 10.3.253.50 peer group evpn_superspine
   neighbor 10.3.253.51 peer group evpn_superspine
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_leaf activate
      neighbor evpn_spine activate
      neighbor evpn_superspine activate
   
   address-family ipv4
      no neighbor evpn_leaf activate
      no neighbor evpn_superspine activate

router ospf 1
   router-id 10.3.253.2
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   no passive-interface Ethernet5
   max-lsa 12000
```
### ЦОД ОИС
#### Адресация для построения сети Underlay
Физическим интерфейсам зададим адресацию согласно данной таблицы:
|Device|Interface|IP Address|
|:-:|:-:|:-:|
|DC2-SpineL|Ethernet1|10.3.255.26/31|
|DC2-SpineL|Ethernet2|10.3.255.28/31|
|DC2-SpineL|Ethernet3|10.3.255.30/31|
|DC2-SpineL|Ethernet4|10.3.255.125/31|
|DC2-SpineL|Ethernet5|10.3.255.137/31|
|DC2-SpineR|Ethernet1|10.3.255.36/31|
|DC2-SpineR|Ethernet2|10.3.255.38/31|
|DC2-SpineR|Ethernet3|10.3.255.40/31|
|DC2-SpineR|Ethernet4|10.3.255.127/31|
|DC2-SpineR|Ethernet5|10.3.255.139/31|
|DC2-LEAF1|Ethernet1|10.3.255.27/31|
|DC2-LEAF1|Ethernet2|10.3.255.37/31|
|DC2-LEAF2|Ethernet1|10.3.255.29/31|
|DC2-LEAF2|Ethernet2|10.3.255.39/31|
|DC2-LEAF3|Ethernet1|10.3.255.27/31|
|DC2-LEAF3|Ethernet2|10.3.255.41/31|

#### Адресация для NVE-интерфейсов

|Device|Interface|IP Address|
|:-:|:-:|:-:|
|DC2-LEAF1|Loopback 1|10.3.255.190/32|
|DC2-LEAF2|Loopback 1|10.3.255.191/32|
|DC2-LEAF3|Loopback 1|10.3.255.192/32|

#### Сетевые зоны на LEAF1-Amsterdam
Зоны одинаковы на DC2-LEAF1, DC2-LEAF2, DC2-LEAF3.
VNI 29999 используется для создания L3VNI, растянут на всю Multi-POD фабрику, в дальнейшем будут добавлены VRF для технологических сетей.
|Network|Gateway / Interface VLAN IP Address|VLAN ID|VLAN Name|VRF|VNI|Description|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|10.3.110.0/24|10.3.110.254|110|110-DataStorage|NET|10110|Сеть для размещения систем хранения|
|10.3.111.0/24|10.3.111.254|111|111-ТАИ|NET|10111|Сеть для размещения узлов ТАИ|
|10.3.120.0/24|10.3.120.254|120|120-Back+Front|NET|10120|Сеть для размещения узлов с веб-серверами|
|10.3.130.0/24|10.3.130.254|130|130-QA|NET|10130|Сеть тестирования|
#### Настройка сети Undrelay
Внутри автономной системы используем протокол OSPF
#### DC2-SpineL
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.26/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.28/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet3
   mtu 9120
   no switchport
   ip address 10.3.255.30/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet4
   mtu 9120
   no switchport
   ip address 10.3.255.125/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet5
   mtu 9120
   no switchport
   ip address 10.3.255.137/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

router ospf 1
   router-id 10.3.253.20
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   no passive-interface Ethernet5
   max-lsa 12000

```
#### DC2-SpineR
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.36/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.38/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet3
   mtu 9120
   no switchport
   ip address 10.3.255.40/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet4
   mtu 9120
   no switchport
   ip address 10.3.255.127/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet5
   mtu 9120
   no switchport
   ip address 10.3.255.139/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

router ospf 1
   router-id 10.3.253.21
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   no passive-interface Ethernet5
   max-lsa 12000

```
#### DC2-LEAF1
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.27/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.37/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

router ospf 1
   router-id 10.3.253.10
   max-lsa 12000

```
#### DC2-LEAF2
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.27/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.37/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
   
router ospf 1
   router-id 10.3.253.10
   max-lsa 12000
```
#### DC2-LEAF3
```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.27/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.41/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
   
router ospf 1
   router-id 10.3.253.12
   max-lsa 12000
```
#### Настройка сети Overlay
#### Настройка сетевых зон на DC2-LEAF1
```
vlan 110,111,120,130

interface Vlan110
   vrf NET
   ip address virtual 10.3.110.254/24

interface Vlan111
   vrf NET
   ip address virtual 10.3.111.254/24

interface Vlan120
   vrf NET
   ip address virtual 10.3.120.254/24

interface Vlan130
   vrf NET
   ip address virtual 10.3.130.254/24

interface Ethernet3
   switchport access vlan 110

```
#### Настройка EVPN на DC2-LEAF1
LEAF1-Ansterdam является Border Gateway, поэтому также настраивается в этом режиме

```
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 110 vni 10110
   vxlan vlan 111 vni 10111
   vxlan vlan 120 vni 10120
   vxlan vlan 130 vni 10130
   vxlan vrf NET vni 29999
   vxlan learn-restrict any

ip virtual-router mac-address 00:a2:a2:a2:a2:a2

ip routing vrf NET

router bgp 4200000002
   router-id 10.3.253.10
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000002
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community extended
   neighbor 10.3.253.20 peer group evpn_spine
   neighbor 10.3.253.21 peer group evpn_spine
   
   vlan 110
      rd 10.3.253.10:10110
      route-target both 4200000002:10110
      redistribute learned
   
   vlan 111
      rd 10.3.253.10:10111
      route-target both 4200000002:10111
      redistribute learned
   
   vlan 120
      rd 10.3.253.10:10120
      route-target both 4200000002:10120
      redistribute learned
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
   
   address-family ipv4
      no neighbor evpn_spine activate
   
   vrf NET
      rd 10.3.253.11:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected
```
#### Настройка сетевых зон на DC2-LEAF2
```
vlan 100-111,120,130

interface Ethernet3
   switchport access vlan 111

interface Vlan110
   vrf NET
   ip address virtual 10.3.110.254/24

interface Vlan111
   vrf NET
   ip address virtual 10.3.111.254/24

interface Vlan120
   vrf NET
   ip address virtual 10.3.120.254/24

interface Vlan130
   vrf NET
   ip address virtual 10.3.130.254/24
```
#### Настройка EVPN на DC-LEAF2
```
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 110 vni 10110
   vxlan vlan 111 vni 10111
   vxlan vlan 120 vni 10120
   vxlan vlan 130 vni 10130
   vxlan vrf NET vni 29999
   vxlan learn-restrict any
!
ip virtual-router mac-address 00:a2:a2:a2:a2:a2
!
ip routing
ip routing vrf NET
!
router bgp 4200000002
   router-id 10.3.253.11
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000002
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community
   neighbor 10.3.253.20 peer group evpn_spine
   neighbor 10.3.253.21 peer group evpn_spine
   !
   vlan 110
      rd 10.3.253.11:10110
      route-target both 4200000002:10110
      redistribute learned
   !
   vlan 111
      rd 10.3.253.11:10111
      route-target both 4200000002:10111
      redistribute learned
   !
   vlan 120
      rd 10.3.253.11:10120
      route-target both 4200000002:10120
      redistribute learned
   !
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
      graceful-restart
   !
   address-family ipv4
      no neighbor evpn_spine activate
   !
   vrf NET
      rd 10.3.253.11:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected
```

#### Настройка сетевых зон на DC2-LEAF3
```
vlan 100-111,120,130

interface Ethernet3
   switchport access vlan 120

interface Vlan110
   vrf NET
   ip address virtual 10.3.110.254/24

interface Vlan111
   vrf NET
   ip address virtual 10.3.111.254/24

interface Vlan120
   vrf NET
   ip address virtual 10.3.120.254/24

interface Vlan130
   vrf NET
   ip address virtual 10.3.130.254/24
```
#### Настройка EVPN на DC-LEAF3
```
ip virtual-router mac-address 00:a2:a2:a2:a2:a2

ip routing
ip routing vrf NET

router bgp 4200000002
   router-id 10.3.253.12
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn_spine peer group
   neighbor evpn_spine remote-as 4200000002
   neighbor evpn_spine update-source Loopback0
   neighbor evpn_spine bfd
   neighbor evpn_spine send-community extended
   neighbor 10.3.253.20 peer group evpn_spine
   neighbor 10.3.253.21 peer group evpn_spine
   
   vlan 110
      rd 10.3.253.12:10110
      route-target both 4200000002:10110
      redistribute learned
   
   vlan 111
      rd 10.3.253.12:10111
      route-target both 4200000002:10111
      redistribute learned
   
   vlan 120
      rd 10.3.253.12:10120
      route-target both 4200000002:10120
      redistribute learned
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_spine activate
      graceful-restart
   
   address-family ipv4
      no neighbor evpn_spine activate
   
   vrf NET
      rd 10.3.253.11:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      redistribute connected
```
#### Настройка EVPN на DC2-SpineL
```
router bgp 4200000002
   router-id 10.3.253.20
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn_leaf peer group
   neighbor evpn_leaf remote-as 4200000002
   neighbor evpn_leaf bfd
   neighbor evpn_leaf route-reflector-client
   neighbor evpn_leaf send-community extended
   neighbor evpn_spine peer group
   neighbor evpn_superspine peer group
   neighbor evpn_superspine remote-as 4200000000
   neighbor evpn_superspine next-hop-unchanged
   neighbor evpn_superspine update-source Loopback0
   neighbor evpn_superspine bfd
   neighbor evpn_superspine ebgp-multihop 16
   neighbor evpn_superspine send-community extended
   neighbor evpn_superspine maximum-routes 12000 warning-only
   neighbor 10.3.253.10 peer group evpn_leaf
   neighbor 10.3.253.11 peer group evpn_leaf
   neighbor 10.3.253.12 peer group evpn_leaf
   neighbor 10.3.253.50 peer group evpn_superspine
   neighbor 10.3.253.51 peer group evpn_superspine
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_leaf activate
      neighbor evpn_superspine activate
   
   address-family ipv4
      no neighbor evpn_leaf activate
      no neighbor evpn_superspine activate

```
#### Настройка EVPN на DC2-SpineR
```
router bgp 4200000002
   router-id 10.3.253.21
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn_leaf peer group
   neighbor evpn_leaf remote-as 4200000002
   neighbor evpn_leaf next-hop-unchanged
   neighbor evpn_leaf update-source Loopback0
   neighbor evpn_leaf bfd
   neighbor evpn_leaf route-reflector-client
   neighbor evpn_leaf send-community extended
   neighbor evpn_spine peer group
   neighbor evpn_superspine peer group
   neighbor evpn_superspine remote-as 4200000000
   neighbor evpn_superspine next-hop-unchanged
   neighbor evpn_superspine update-source Loopback0
   neighbor evpn_superspine bfd
   neighbor evpn_superspine ebgp-multihop 16
   neighbor evpn_superspine send-community extended
   neighbor evpn_superspine maximum-routes 12000 warning-only
   neighbor 10.3.253.10 peer group evpn_leaf
   neighbor 10.3.253.11 peer group evpn_leaf
   neighbor 10.3.253.12 peer group evpn_leaf
   neighbor 10.3.253.50 peer group evpn_superspine
   neighbor 10.3.253.51 peer group evpn_superspine
   !
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn_leaf activate
      neighbor evpn_superspine activate
   !
   address-family ipv4
      no neighbor evpn_leaf activate
      no neighbor evpn_superspine activate

```
### Настройка Super-Spine
### Настройка Super-SpineL

```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.120/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.122/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   mtu 9120
   no switchport
   ip address 10.3.255.124/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet4
   mtu 9120
   no switchport
   ip address 10.3.255.126/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet5
   mtu 9120
   no switchport
   ip address 10.3.255.140/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet6
  mtu 9120
   no switchport
   ip address 10.3.255.142/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Loopback0
   ip address 10.3.253.50/32
   ip ospf area 0.0.0.0

ip routing

router bgp 4200000000
   router-id 10.3.253.50
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn peer group
   neighbor evpn next-hop-unchanged
   neighbor evpn update-source Loopback0
   neighbor evpn bfd
   neighbor evpn ebgp-multihop 16
   neighbor evpn send-community extended
   neighbor evpn maximum-routes 12000 warning-only
   neighbor evpn_spine peer group
   neighbor 10.3.253.1 peer group evpn
   neighbor 10.3.253.1 remote-as 4200000001
   neighbor 10.3.253.2 peer group evpn
   neighbor 10.3.253.2 remote-as 4200000001
   neighbor 10.3.253.20 peer group evpn
   neighbor 10.3.253.20 remote-as 4200000002
   neighbor 10.3.253.21 peer group evpn
   neighbor 10.3.253.21 remote-as 4200000002
   neighbor 10.3.253.60 peer group evpn
   neighbor 10.3.253.60 remote-as 4200000003
   neighbor 10.3.253.61 peer group evpn
   neighbor 10.3.253.61 remote-as 4200000003
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn activate
      neighbor evpn_spine activate
   
   address-family ipv4
      no neighbor evpn activate

router ospf 1
   router-id 10.3.255.50
   max-lsa 12000
```

### Настройка Super-SpineR

```
interface Ethernet1
   mtu 9120
   no switchport
   ip address 10.3.255.132/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet2
   mtu 9120
   no switchport
   ip address 10.3.255.134/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet3
   mtu 9120
   no switchport
   ip address 10.3.255.136/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Ethernet4
   mtu 9120
   no switchport
   ip address 10.3.255.138/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0

interface Loopback0
   ip address 10.3.253.51/32
   ip ospf area 0.0.0.0


ip routing

router bgp 4200000000
   router-id 10.3.253.51
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn peer group
   neighbor evpn remote-as 4200000001
   neighbor evpn next-hop-unchanged
   neighbor evpn update-source Loopback0
   neighbor evpn bfd
   neighbor evpn ebgp-multihop 16
   neighbor evpn send-community extended
   neighbor evpn maximum-routes 12000 warning-only
   neighbor evpn_spine peer group
   neighbor 10.3.253.1 peer group evpn
   neighbor 10.3.253.1 remote-as 4200000001
   neighbor 10.3.253.2 peer group evpn
   neighbor 10.3.253.2 remote-as 4200000001
   neighbor 10.3.253.20 peer group evpn
   neighbor 10.3.253.20 remote-as 4200000002
   neighbor 10.3.253.21 peer group evpn
   neighbor 10.3.253.21 remote-as 4200000002
   neighbor 10.3.253.60 peer group evpn
   neighbor 10.3.253.60 remote-as 4200000003
   neighbor 10.3.253.61 peer group evpn
   neighbor 10.3.253.61 remote-as 4200000003
   
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn activate
      neighbor evpn_spine activate
   
   address-family ipv4
      no neighbor evpn activate

router ospf 1
   router-id 10.3.253.51
   max-lsa 12000
```
### Настройка пограничных коммутаторов
Полная конфигурация будет в config_files.
### Настройка Edge-LEAF1
```
router bgp 4200000003
   router-id 10.3.253.61
   timers bgp 3 9
   graceful-restart restart-time 300
   maximum-paths 8 ecmp 16
   neighbor evpn peer group
   neighbor evpn next-hop-unchanged
   neighbor evpn update-source Loopback0
   neighbor evpn bfd
   neighbor evpn ebgp-multihop 16
   neighbor evpn send-community extended
   neighbor evpn maximum-routes 12000 warning-only
   neighbor 10.3.253.50 peer group evpn
   neighbor 10.3.253.50 remote-as 4200000000
   neighbor 10.3.253.51 peer group evpn
   neighbor 10.3.253.51 remote-as 4200000000
   !
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn activate
      graceful-restart
   !
   address-family ipv4
      no neighbor evpn activate
   !
   vrf NET
      rd 10.3.253.61:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      neighbor 10.110.254.251 remote-as 65500
      redistribute connected
!
router ospf 1
   router-id 10.3.253.61
   max-lsa 12000
```
### Настройка Edge-LEAF2
```
router bgp 4200000003
   router-id 10.3.253.60
   timers bgp 3 9
   maximum-paths 8 ecmp 16
   neighbor evpn peer group
   neighbor evpn next-hop-unchanged
   neighbor evpn update-source Loopback0
   neighbor evpn bfd
   neighbor evpn ebgp-multihop 16
   neighbor evpn send-community extended
   neighbor evpn maximum-routes 12000 warning-only
   neighbor 10.3.253.50 peer group evpn
   neighbor 10.3.253.50 remote-as 4200000000
   neighbor 10.3.253.51 peer group evpn
   neighbor 10.3.253.51 remote-as 4200000000
   !
   address-family evpn
      bgp next-hop-unchanged
      neighbor evpn activate
      no graceful-restart
   !
   address-family ipv4
      no neighbor evpn activate
      no graceful-restart
   !
   vrf NET
      rd 10.3.253.60:29999
      route-target import evpn 4200000001:29999
      route-target export evpn 4200000001:29999
      neighbor 10.110.254.251 remote-as 65500
      redistribute connected
!
router ospf 1
   router-id 10.3.253.60
   max-lsa 12000
```
### Проверка работоспособности
Проверяем таблицы маршрутизации, NVE Peering и MAC-таблицы на всех LEAF-коммутаторах и IP-связаность клиентов между собой.
<details>
<summary>Проверка на DC1-LEAF1</summary>

```
DC1-LEAF1#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.3, local AS number 4200000001
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.3.253.3:10010 imet 10.3.255.180
                                 -                     -       -       0       i
 * >      RD: 10.3.253.3:10011 imet 10.3.255.180
                                 -                     -       -       0       i
 * >      RD: 10.3.253.3:10020 imet 10.3.255.180
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 *  ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 * >Ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >      RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC1-LEAF1#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.181       flood, unicast
10.3.255.190       flood, unicast
10.3.255.192       unicast       

Total number of remote VTEPS:  5

DC1-LEAF1#sh mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a1.a1a1.a1a1    STATIC      Cpu
  10    00a1.a1a1.a1a1    STATIC      Cpu
  11    00a1.a1a1.a1a1    STATIC      Cpu
  20    00a1.a1a1.a1a1    STATIC      Cpu
  31    00a1.a1a1.a1a1    STATIC      Cpu
4094    00a1.a1a1.a1a1    STATIC      Cpu
4094    5050.626e.cca7    DYNAMIC     Vx1        1       4:06:41 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:02:38 ago
4094    509b.6ee2.2e5a    DYNAMIC     Vx1        1       4:53:37 ago
4094    50b3.41ad.8914    DYNAMIC     Vx1        1       4:53:37 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:41:10 ago
Total Mac Addresses for this criterion: 11

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0

DC1-LEAF1#sh ip route vrf NET 

VRF: NET
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

Gateway of last resort is not set

 C        10.3.10.0/24 is directly connected, Vlan10
 C        10.3.11.0/24 is directly connected, Vlan11
 C        10.3.20.0/24 is directly connected, Vlan20
 B I      10.3.110.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.111.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.120.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.130.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1

```
</details>
<details>
<summary>Проверка на DC1-LEAF2</summary>

```
DC1-LEAF2#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.4, local AS number 4200000001
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.3:10011 imet 10.3.255.181
                                 -                     -       -       0       i
 * >      RD: 10.3.253.4:10010 imet 10.3.255.181
                                 -                     -       -       0       i
 * >      RD: 10.3.253.4:10011 imet 10.3.255.181
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.5 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC1-LEAF2#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.180       flood, unicast
10.3.255.190       flood, unicast
10.3.255.192       unicast       

Total number of remote VTEPS:  5
DC1-LEAF2#sh mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a1.a1a1.a1a1    STATIC      Po1000
  10    00a1.a1a1.a1a1    STATIC      Cpu
  10    509b.6ee2.2e5a    STATIC      Po1000
  11    00a1.a1a1.a1a1    STATIC      Po1000
  11    509b.6ee2.2e5a    STATIC      Po1000
  20    00a1.a1a1.a1a1    STATIC      Po1000
  20    509b.6ee2.2e5a    STATIC      Po1000
  31    00a1.a1a1.a1a1    STATIC      Po1000
4093    00a1.a1a1.a1a1    STATIC      Cpu
4094    00a1.a1a1.a1a1    STATIC      Cpu
4094    5050.626e.cca7    DYNAMIC     Vx1        1       4:07:53 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:03:50 ago
4094    5095.e7b0.085d    DYNAMIC     Vx1        1       4:54:50 ago
4094    509b.6ee2.2e5a    DYNAMIC     Vx1        2       4:54:59 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:42:22 ago
Total Mac Addresses for this criterion: 15

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
DC1-LEAF2#sh ip route vrf NET

VRF: NET
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

Gateway of last resort is not set

 C        10.3.10.0/24 is directly connected, Vlan10
 C        10.3.11.0/24 is directly connected, Vlan11
 C        10.3.20.0/24 is directly connected, Vlan20
 B I      10.3.110.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.111.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.120.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.130.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1
```
</details>
<details>
<summary>Проверка на DC1-LEAF3 </summary>

```
DC1-LEAF3(config)#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.5, local AS number 4200000001
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 *  ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 * >Ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.3:10010 imet 10.3.255.190
                                 -                     -       -       0       i
 * >      RD: 10.3.253.3:10011 imet 10.3.255.190
                                 -                     -       -       0       i
 * >      RD: 10.3.253.3:10020 imet 10.3.255.190
                                 -                     -       -       0       i
          RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
          RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
          RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
          RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
          RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
          RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       i Or-ID: 10.3.253.3 C-LST: 10.3.253.2 
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.1 
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       i Or-ID: 10.3.253.4 C-LST: 10.3.253.2 
 * >      RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 *  ec    RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       4200000000 4200000002 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC1-LEAF3(config)#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.180       unicast, flood
10.3.255.181       unicast, flood
10.3.255.192       unicast       

Total number of remote VTEPS:  5
DC1-LEAF3(config)#sh mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a1.a1a1.a1a1    STATIC      Cpu
  10    00a1.a1a1.a1a1    STATIC      Cpu
  10    50b3.41ad.8914    STATIC      Po1000
  11    00a1.a1a1.a1a1    STATIC      Cpu
  11    50b3.41ad.8914    STATIC      Po1000
  20    00a1.a1a1.a1a1    STATIC      Cpu
  20    50b3.41ad.8914    STATIC      Po1000
  31    00a1.a1a1.a1a1    STATIC      Cpu
4093    00a1.a1a1.a1a1    STATIC      Cpu
4094    00a1.a1a1.a1a1    STATIC      Cpu
4094    5050.626e.cca7    DYNAMIC     Vx1        1       4:09:22 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:05:17 ago
4094    5095.e7b0.085d    DYNAMIC     Vx1        1       4:56:19 ago
4094    50b3.41ad.8914    DYNAMIC     Vx1        2       4:56:28 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:43:49 ago
Total Mac Addresses for this criterion: 15

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
DC1-LEAF3(config)#sh ip route vrf NET 

VRF: NET
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

Gateway of last resort is not set

 C        10.3.10.0/24 is directly connected, Vlan10
 C        10.3.11.0/24 is directly connected, Vlan11
 C        10.3.20.0/24 is directly connected, Vlan20
 B I      10.3.110.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.111.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.120.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.3.130.0/24 [200/0] via VTEP 10.3.255.192 VNI 29999 router-mac 50:50:62:6e:cc:a7 local-interface Vxlan1
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1

```
</details>
<details>
<summary>Проверка на DC2-LEAF1</summary>

```
DC2-LEAF1#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.10, local AS number 4200000002
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >      RD: 10.3.253.10:10110 imet 10.3.255.190
                                 -                     -       -       0       i
 * >      RD: 10.3.253.10:10111 imet 10.3.255.190
                                 -                     -       -       0       i
 * >      RD: 10.3.253.10:10120 imet 10.3.255.190
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
          RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC2-LEAF1#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.180       unicast       
10.3.255.181       unicast       
10.3.255.191       unicast, flood
10.3.255.192       unicast, flood

Total number of remote VTEPS:  6
DC2-LEAF1#sh mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a2.a2a2.a2a2    STATIC      Cpu
 110    00a2.a2a2.a2a2    STATIC      Cpu
 111    00a2.a2a2.a2a2    STATIC      Cpu
 120    00a2.a2a2.a2a2    STATIC      Cpu
 130    00a2.a2a2.a2a2    STATIC      Cpu
 131    00a2.a2a2.a2a2    STATIC      Cpu
4094    00a2.a2a2.a2a2    STATIC      Cpu
4094    0200.00be.5fdb    DYNAMIC     Vx1        1       0:08:41 ago
4094    5050.626e.cca7    DYNAMIC     Vx1        1       0:08:41 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:08:41 ago
4094    5095.e7b0.085d    DYNAMIC     Vx1        1       0:08:41 ago
4094    50b3.41ad.8914    DYNAMIC     Vx1        1       0:08:41 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:08:41 ago
Total Mac Addresses for this criterion: 13

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
DC2-LEAF1#sh ip route vrf NET

VRF: NET
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

Gateway of last resort is not set

 B I      10.3.10.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
 B I      10.3.11.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
 B I      10.3.20.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
 C        10.3.110.0/24 is directly connected, Vlan110
 C        10.3.111.0/24 is directly connected, Vlan111
 C        10.3.120.0/24 is directly connected, Vlan120
 C        10.3.130.0/24 is directly connected, Vlan130
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1
```
</details>
<details>
<summary>Проверка на DC2-LEAF2</summary>

```
DC2-LEAF2#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.11, local AS number 4200000002
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >      RD: 10.3.253.11:10110 imet 10.3.255.191
                                 -                     -       -       0       i
 * >      RD: 10.3.253.11:10111 imet 10.3.255.191
                                 -                     -       -       0       i
 * >      RD: 10.3.253.11:10120 imet 10.3.255.191
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.12:10110 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.12:10111 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.12:10120 imet 10.3.255.192
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.192          -       100     0       i Or-ID: 10.3.253.12 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC2-LEAF2#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.180       unicast       
10.3.255.181       unicast       
10.3.255.190       unicast, flood
10.3.255.192       unicast, flood

Total number of remote VTEPS:  6
DC2-LEAF2#show mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a2.a2a2.a2a2    STATIC      Cpu
 100    00a2.a2a2.a2a2    STATIC      Cpu
 101    00a2.a2a2.a2a2    STATIC      Cpu
 102    00a2.a2a2.a2a2    STATIC      Cpu
 103    00a2.a2a2.a2a2    STATIC      Cpu
 104    00a2.a2a2.a2a2    STATIC      Cpu
 105    00a2.a2a2.a2a2    STATIC      Cpu
 106    00a2.a2a2.a2a2    STATIC      Cpu
 107    00a2.a2a2.a2a2    STATIC      Cpu
 108    00a2.a2a2.a2a2    STATIC      Cpu
 109    00a2.a2a2.a2a2    STATIC      Cpu
 110    00a2.a2a2.a2a2    STATIC      Cpu
 111    00a2.a2a2.a2a2    STATIC      Cpu
 120    00a2.a2a2.a2a2    STATIC      Cpu
 130    00a2.a2a2.a2a2    STATIC      Cpu
 131    00a2.a2a2.a2a2    STATIC      Cpu
4094    00a2.a2a2.a2a2    STATIC      Cpu
4094    5050.626e.cca7    DYNAMIC     Vx1        1       3:41:24 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:12:12 ago
4094    5095.e7b0.085d    DYNAMIC     Vx1        1       3:41:24 ago
4094    509b.6ee2.2e5a    DYNAMIC     Vx1        1       3:41:24 ago
4094    50b3.41ad.8914    DYNAMIC     Vx1        1       3:41:24 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:50:44 ago
Total Mac Addresses for this criterion: 23

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
DC2-LEAF2#sh ip route vrf NET 

VRF: NET
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

Gateway of last resort is not set

 B I      10.3.10.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 B I      10.3.11.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 B I      10.3.20.0/24 [200/0] via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 C        10.3.110.0/24 is directly connected, Vlan110
 C        10.3.111.0/24 is directly connected, Vlan111
 C        10.3.120.0/24 is directly connected, Vlan120
 C        10.3.130.0/24 is directly connected, Vlan130
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1
```
</details>
<details>
<summary>Проверка на DC2-LEAF3</summary>

```
DC2-LEAF3#sh bgp evpn 
BGP routing table information for VRF default
Router identifier 10.3.253.12, local AS number 4200000002
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.180
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10010 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:10011 imet 10.3.255.181
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10010 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10011 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:10020 imet 10.3.255.190
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10110 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10111 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.20 
 *  ec    RD: 10.3.253.10:10120 imet 10.3.255.190
                                 10.3.255.190          -       100     0       i Or-ID: 10.3.253.10 C-LST: 10.3.253.21 
 * >Ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.11:10110 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.11:10111 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.21 
 *  ec    RD: 10.3.253.11:10120 imet 10.3.255.191
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.12:10110 imet 10.3.255.192
                                 -                     -       -       0       i
 * >      RD: 10.3.253.12:10111 imet 10.3.255.192
                                 -                     -       -       0       i
 * >      RD: 10.3.253.12:10120 imet 10.3.255.192
                                 -                     -       -       0       i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.10.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.11.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.3:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.180          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.4:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.181          -       100     0       4200000000 4200000001 i
 * >Ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 *  ec    RD: 10.3.253.5:29999 ip-prefix 10.3.20.0/24
                                 10.3.255.190          -       100     0       4200000000 4200000001 i
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.110.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.111.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.120.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >      RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 -                     -       -       0       i
 *        RD: 10.3.253.11:29999 ip-prefix 10.3.130.0/24
                                 10.3.255.191          -       100     0       i Or-ID: 10.3.253.11 C-LST: 10.3.253.20 
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.2.1/32
                                 10.3.254.254          -       100     0       4200000000 4200000003 65500 i
 * >Ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.61:29999 ip-prefix 10.110.254.250/31
                                 10.3.254.254          -       100     0       4200000000 4200000003 i
 * >Ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
 *  ec    RD: 10.3.253.60:29999 ip-prefix 10.110.254.252/31
                                 10.3.254.253          -       100     0       4200000000 4200000003 i
DC2-LEAF3#sh vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP               Tunnel Type(s)
------------------ --------------
10.3.254.253       unicast       
10.3.254.254       unicast       
10.3.255.180       unicast       
10.3.255.181       unicast       
10.3.255.190       unicast, flood
10.3.255.191       unicast, flood

Total number of remote VTEPS:  6
DC2-LEAF3#sh mac address-table 
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
   1    00a2.a2a2.a2a2    STATIC      Cpu
 100    00a2.a2a2.a2a2    STATIC      Cpu
 101    00a2.a2a2.a2a2    STATIC      Cpu
 102    00a2.a2a2.a2a2    STATIC      Cpu
 103    00a2.a2a2.a2a2    STATIC      Cpu
 104    00a2.a2a2.a2a2    STATIC      Cpu
 105    00a2.a2a2.a2a2    STATIC      Cpu
 106    00a2.a2a2.a2a2    STATIC      Cpu
 107    00a2.a2a2.a2a2    STATIC      Cpu
 108    00a2.a2a2.a2a2    STATIC      Cpu
 109    00a2.a2a2.a2a2    STATIC      Cpu
 110    00a2.a2a2.a2a2    STATIC      Cpu
 111    00a2.a2a2.a2a2    STATIC      Cpu
 120    00a2.a2a2.a2a2    STATIC      Cpu
 130    00a2.a2a2.a2a2    STATIC      Cpu
 131    00a2.a2a2.a2a2    STATIC      Cpu
4094    00a2.a2a2.a2a2    STATIC      Cpu
4094    0200.00be.5fdb    DYNAMIC     Vx1        1       0:19:31 ago
4094    5057.d3af.e27f    DYNAMIC     Vx1        1       0:13:25 ago
4094    5095.e7b0.085d    DYNAMIC     Vx1        1       4:17:31 ago
4094    509b.6ee2.2e5a    DYNAMIC     Vx1        1       4:17:31 ago
4094    50b3.41ad.8914    DYNAMIC     Vx1        1       4:17:31 ago
4094    50c5.3eb0.a850    DYNAMIC     Vx1        1       0:51:57 ago
Total Mac Addresses for this criterion: 23

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
DC2-LEAF3#sh ip route vrf 
% Incomplete command
DC2-LEAF3#sh ip route vrf NET

VRF: NET
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

Gateway of last resort is not set

 B I      10.3.10.0/24 [200/0] via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 B I      10.3.11.0/24 [200/0] via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 B I      10.3.20.0/24 [200/0] via VTEP 10.3.255.180 VNI 29999 router-mac 50:95:e7:b0:08:5d local-interface Vxlan1
                               via VTEP 10.3.255.181 VNI 29999 router-mac 50:b3:41:ad:89:14 local-interface Vxlan1
                               via VTEP 10.3.255.190 VNI 29999 router-mac 50:9b:6e:e2:2e:5a local-interface Vxlan1
 C        10.3.110.0/24 is directly connected, Vlan110
 C        10.3.111.0/24 is directly connected, Vlan111
 C        10.3.120.0/24 is directly connected, Vlan120
 C        10.3.130.0/24 is directly connected, Vlan130
 B I      10.110.2.1/32 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.250/31 [200/0] via VTEP 10.3.254.254 VNI 29999 router-mac 50:57:d3:af:e2:7f local-interface Vxlan1
 B I      10.110.254.252/31 [200/0] via VTEP 10.3.254.253 VNI 29999 router-mac 50:c5:3e:b0:a8:50 local-interface Vxlan1

```
</details>
<details>
<summary>Проверка IP-связанности клиентов</summary>

```
BACK1> sh ip

NAME        : BACK1[1]
IP/MASK     : 10.3.20.1/24
GATEWAY     : 10.3.20.254
DNS         : 
MAC         : 00:50:79:66:68:3b
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

84 bytes from 10.3.10.2 icmp_seq=1 ttl=62 time=208.288 ms
84 bytes from 10.3.10.2 icmp_seq=2 ttl=62 time=94.947 ms
84 bytes from 10.3.10.2 icmp_seq=3 ttl=62 time=17.589 ms
84 bytes from 10.3.10.2 icmp_seq=4 ttl=62 time=13.799 ms
84 bytes from 10.3.10.2 icmp_seq=5 ttl=62 time=16.159 ms

BACK1> ping 10.3.11.1    

84 bytes from 10.3.11.1 icmp_seq=1 ttl=62 time=24.332 ms
84 bytes from 10.3.11.1 icmp_seq=2 ttl=62 time=13.935 ms
84 bytes from 10.3.11.1 icmp_seq=3 ttl=62 time=15.195 ms
84 bytes from 10.3.11.1 icmp_seq=4 ttl=62 time=14.954 ms
84 bytes from 10.3.11.1 icmp_seq=5 ttl=62 time=13.800 ms

BACK1> ping 10.3.111.1

84 bytes from 10.3.111.1 icmp_seq=1 ttl=62 time=28.533 ms
84 bytes from 10.3.111.1 icmp_seq=2 ttl=62 time=17.456 ms
84 bytes from 10.3.111.1 icmp_seq=3 ttl=62 time=16.395 ms
84 bytes from 10.3.111.1 icmp_seq=4 ttl=62 time=16.517 ms
84 bytes from 10.3.111.1 icmp_seq=5 ttl=62 time=15.769 ms

BACK1> ping 10.3.120.1

84 bytes from 10.3.120.1 icmp_seq=1 ttl=62 time=24.876 ms
84 bytes from 10.3.120.1 icmp_seq=2 ttl=62 time=15.355 ms
84 bytes from 10.3.120.1 icmp_seq=3 ttl=62 time=17.760 ms
84 bytes from 10.3.120.1 icmp_seq=4 ttl=62 time=15.332 ms
84 bytes from 10.3.120.1 icmp_seq=5 ttl=62 time=24.391 ms


BACK1> ping 10.110.2.1

84 bytes from 10.110.2.1 icmp_seq=1 ttl=253 time=14.528 ms
84 bytes from 10.110.2.1 icmp_seq=2 ttl=253 time=15.089 ms
84 bytes from 10.110.2.1 icmp_seq=3 ttl=253 time=11.972 ms
84 bytes from 10.110.2.1 icmp_seq=4 ttl=253 time=12.209 ms
84 bytes from 10.110.2.1 icmp_seq=5 ttl=253 time=13.955 ms


--------------------------------------------------------------------

NAS1.3> sh ip

NAME        : NAS1.3[1]
IP/MASK     : 10.3.10.2/24
GATEWAY     : 10.3.10.254
DNS         : 
MAC         : 00:50:79:66:68:3d
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

NAS1.3> ping 10.3.11.1

84 bytes from 10.3.11.1 icmp_seq=1 ttl=60 time=95.456 ms
84 bytes from 10.3.11.1 icmp_seq=2 ttl=62 time=16.320 ms
84 bytes from 10.3.11.1 icmp_seq=3 ttl=62 time=19.668 ms
84 bytes from 10.3.11.1 icmp_seq=4 ttl=62 time=17.296 ms
84 bytes from 10.3.11.1 icmp_seq=5 ttl=62 time=18.155 ms

NAS1.3> ping 10.3.20.1

84 bytes from 10.3.20.1 icmp_seq=1 ttl=62 time=17.379 ms
84 bytes from 10.3.20.1 icmp_seq=2 ttl=62 time=14.977 ms
84 bytes from 10.3.20.1 icmp_seq=3 ttl=62 time=13.454 ms
84 bytes from 10.3.20.1 icmp_seq=4 ttl=62 time=14.281 ms
84 bytes from 10.3.20.1 icmp_seq=5 ttl=62 time=15.095 ms

NAS1.3> ping 10.3.111.1

10.3.111.1 icmp_seq=1 timeout
84 bytes from 10.3.111.1 icmp_seq=2 ttl=62 time=19.471 ms
84 bytes from 10.3.111.1 icmp_seq=3 ttl=62 time=24.675 ms
84 bytes from 10.3.111.1 icmp_seq=4 ttl=62 time=17.921 ms
84 bytes from 10.3.111.1 icmp_seq=5 ttl=62 time=23.770 ms

NAS1.3> ping 10.110.2.1  

84 bytes from 10.110.2.1 icmp_seq=1 ttl=253 time=17.152 ms
84 bytes from 10.110.2.1 icmp_seq=2 ttl=253 time=15.505 ms
84 bytes from 10.110.2.1 icmp_seq=3 ttl=253 time=16.303 ms
84 bytes from 10.110.2.1 icmp_seq=4 ttl=253 time=16.077 ms
84 bytes from 10.110.2.1 icmp_seq=5 ttl=253 time=15.704 ms

----------------------------------------------------------------------

TAI1.3> sh ip

NAME        : TAI1.3[1]
IP/MASK     : 10.3.11.1/24
GATEWAY     : 10.3.11.254
DNS         : 
MAC         : 00:50:79:66:68:40
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

TAI1.3> ping 10.3.10.2

84 bytes from 10.3.10.2 icmp_seq=1 ttl=62 time=14.592 ms
84 bytes from 10.3.10.2 icmp_seq=2 ttl=62 time=13.455 ms
84 bytes from 10.3.10.2 icmp_seq=3 ttl=62 time=12.539 ms
84 bytes from 10.3.10.2 icmp_seq=4 ttl=62 time=15.197 ms
84 bytes from 10.3.10.2 icmp_seq=5 ttl=62 time=13.749 ms

TAI1.3> ping 10.3.20.1

84 bytes from 10.3.20.1 icmp_seq=1 ttl=62 time=22.421 ms
84 bytes from 10.3.20.1 icmp_seq=2 ttl=62 time=13.480 ms
84 bytes from 10.3.20.1 icmp_seq=3 ttl=62 time=14.654 ms
84 bytes from 10.3.20.1 icmp_seq=4 ttl=62 time=13.585 ms
84 bytes from 10.3.20.1 icmp_seq=5 ttl=62 time=13.671 ms

TAI1.3> ping 10.3.111.1

84 bytes from 10.3.111.1 icmp_seq=1 ttl=62 time=27.625 ms
84 bytes from 10.3.111.1 icmp_seq=2 ttl=62 time=19.858 ms
84 bytes from 10.3.111.1 icmp_seq=3 ttl=62 time=20.848 ms
84 bytes from 10.3.111.1 icmp_seq=4 ttl=62 time=19.487 ms
84 bytes from 10.3.111.1 icmp_seq=5 ttl=62 time=21.611 ms

TAI1.3> ping 10.3.120.1

84 bytes from 10.3.120.1 icmp_seq=1 ttl=62 time=23.726 ms
84 bytes from 10.3.120.1 icmp_seq=2 ttl=62 time=18.358 ms
84 bytes from 10.3.120.1 icmp_seq=3 ttl=62 time=18.509 ms
84 bytes from 10.3.120.1 icmp_seq=4 ttl=62 time=17.954 ms
84 bytes from 10.3.120.1 icmp_seq=5 ttl=62 time=20.550 ms

TAI1.3> ping 10.110.2.1

84 bytes from 10.110.2.1 icmp_seq=1 ttl=253 time=17.656 ms
84 bytes from 10.110.2.1 icmp_seq=2 ttl=253 time=14.860 ms
84 bytes from 10.110.2.1 icmp_seq=3 ttl=253 time=20.115 ms
84 bytes from 10.110.2.1 icmp_seq=4 ttl=253 time=16.097 ms
84 bytes from 10.110.2.1 icmp_seq=5 ttl=253 time=15.591 ms


------------------------------------------------------------------

TAI2> sh ip

NAME        : TAI2[1]
IP/MASK     : 10.3.111.1/24
GATEWAY     : 10.3.111.254
DNS         : 
MAC         : 00:50:79:66:68:3e
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

TAI2> ping 10.3.10.2

84 bytes from 10.3.10.2 icmp_seq=1 ttl=62 time=21.088 ms
84 bytes from 10.3.10.2 icmp_seq=2 ttl=62 time=20.044 ms
84 bytes from 10.3.10.2 icmp_seq=3 ttl=62 time=25.286 ms
84 bytes from 10.3.10.2 icmp_seq=4 ttl=62 time=20.663 ms
84 bytes from 10.3.10.2 icmp_seq=5 ttl=62 time=19.445 ms

TAI2> ping 10.3.11.1

84 bytes from 10.3.11.1 icmp_seq=1 ttl=62 time=24.897 ms
84 bytes from 10.3.11.1 icmp_seq=2 ttl=62 time=19.104 ms
84 bytes from 10.3.11.1 icmp_seq=3 ttl=62 time=18.781 ms
84 bytes from 10.3.11.1 icmp_seq=4 ttl=62 time=20.971 ms
84 bytes from 10.3.11.1 icmp_seq=5 ttl=62 time=20.300 ms

TAI2> ping 10.3.20.1

84 bytes from 10.3.20.1 icmp_seq=1 ttl=62 time=18.633 ms
84 bytes from 10.3.20.1 icmp_seq=2 ttl=62 time=15.981 ms
84 bytes from 10.3.20.1 icmp_seq=3 ttl=62 time=16.879 ms
84 bytes from 10.3.20.1 icmp_seq=4 ttl=62 time=16.762 ms
84 bytes from 10.3.20.1 icmp_seq=5 ttl=62 time=16.745 ms

TAI2> ping 10.3.120.1

84 bytes from 10.3.120.1 icmp_seq=1 ttl=62 time=18.263 ms
84 bytes from 10.3.120.1 icmp_seq=2 ttl=62 time=10.206 ms
84 bytes from 10.3.120.1 icmp_seq=3 ttl=62 time=10.679 ms
84 bytes from 10.3.120.1 icmp_seq=4 ttl=62 time=11.505 ms
84 bytes from 10.3.120.1 icmp_seq=5 ttl=62 time=10.738 ms

TAI2> ping 10.110.2.1

84 bytes from 10.110.2.1 icmp_seq=1 ttl=253 time=14.493 ms
84 bytes from 10.110.2.1 icmp_seq=2 ttl=253 time=13.593 ms
84 bytes from 10.110.2.1 icmp_seq=3 ttl=253 time=11.597 ms
84 bytes from 10.110.2.1 icmp_seq=4 ttl=253 time=12.168 ms
84 bytes from 10.110.2.1 icmp_seq=5 ttl=253 time=13.668 ms


------------------------------------------------------------------

BACK2> sh ip

NAME        : BACK2[1]
IP/MASK     : 10.3.120.1/24
GATEWAY     : 10.3.120.254
DNS         : 
MAC         : 00:50:79:66:68:3f
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

BACK2> ping 10.3.10.2

84 bytes from 10.3.10.2 icmp_seq=1 ttl=62 time=22.320 ms
84 bytes from 10.3.10.2 icmp_seq=2 ttl=62 time=20.559 ms
84 bytes from 10.3.10.2 icmp_seq=3 ttl=62 time=19.995 ms
84 bytes from 10.3.10.2 icmp_seq=4 ttl=62 time=21.013 ms
84 bytes from 10.3.10.2 icmp_seq=5 ttl=62 time=20.321 ms

BACK2> ping 10.3.11.1 

84 bytes from 10.3.11.1 icmp_seq=1 ttl=62 time=30.792 ms
84 bytes from 10.3.11.1 icmp_seq=2 ttl=62 time=19.122 ms
84 bytes from 10.3.11.1 icmp_seq=3 ttl=62 time=18.304 ms
84 bytes from 10.3.11.1 icmp_seq=4 ttl=62 time=18.024 ms
84 bytes from 10.3.11.1 icmp_seq=5 ttl=62 time=21.353 ms

BACK2> ping 10.3.20.1

84 bytes from 10.3.20.1 icmp_seq=1 ttl=62 time=24.251 ms
84 bytes from 10.3.20.1 icmp_seq=2 ttl=62 time=18.063 ms
84 bytes from 10.3.20.1 icmp_seq=3 ttl=62 time=15.661 ms
84 bytes from 10.3.20.1 icmp_seq=4 ttl=62 time=15.872 ms
84 bytes from 10.3.20.1 icmp_seq=5 ttl=62 time=16.792 ms

BACK2> ping 10.3.111.1

84 bytes from 10.3.111.1 icmp_seq=1 ttl=62 time=23.453 ms
84 bytes from 10.3.111.1 icmp_seq=2 ttl=62 time=11.291 ms
84 bytes from 10.3.111.1 icmp_seq=3 ttl=62 time=10.574 ms
84 bytes from 10.3.111.1 icmp_seq=4 ttl=62 time=10.406 ms
84 bytes from 10.3.111.1 icmp_seq=5 ttl=62 time=10.453 ms

BACK2> ping 10.110.2.1

84 bytes from 10.110.2.1 icmp_seq=1 ttl=253 time=18.073 ms
84 bytes from 10.110.2.1 icmp_seq=2 ttl=253 time=14.609 ms
84 bytes from 10.110.2.1 icmp_seq=3 ttl=253 time=13.133 ms
84 bytes from 10.110.2.1 icmp_seq=4 ttl=253 time=12.296 ms
84 bytes from 10.110.2.1 icmp_seq=5 ttl=253 time=11.643 ms


```

</details>
</br>
VTEP-коммутаторы обмениваются маршрутной информацией и строят туннели как внутри своих автономных систем, так и между ними. Клиенты в разных сетях и на разных LEAF связаны друг с другом внутри и между автономных систем, так же через пограничные коммутаторы позволяют связать LEAF ЦОД ТАИ и ЦОД ОИС c удаленным ЦОД.