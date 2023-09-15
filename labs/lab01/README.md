### Проектирование адресного пространства

### Цели
- Часть 1: Собрать схему CLOS;
- Часть 2: Распределить адресное пространство;

### Таблица адресов

|Device|Interface|IP Address|Subnet Mask|
|---|---|---|---|
SpineL|Ethernet 1/1|10.1.0.1|255.255.255.252|
Leaf1|Ethernet 1/1|10.1.0.2|255.255.255.252|
SpineL|Ethernet 1/2|10.1.0.5|255.255.255.252|
Leaf2|Ethernet 1/1|10.1.0.6|255.255.255.252|
SpineL|Ethernet 1/3|10.1.0.9|255.255.255.252|
Leaf3|Ethernet 1/1|10.1.0.10|255.255.255.252|
SpineR|Ethernet 1/1|10.2.0.1|255.255.255.252|
Leaf1|Ethernet 1/2|10.2.0.2|255.255.255.252|
SpineR|Ethernet 1/2|10.2.0.5|255.255.255.252|
Leaf2|Ethernet 1/2|10.2.0.6|255.255.255.252|
SpineR|Ethernet 1/3|10.2.0.9|255.255.255.252|
Leaf3|Ethernet 1/2|10.2.0.10|255.255.255.252|
VPC1|Ethernet 1/4|172.16.1.1|255.255.255.0|
VPC2|Ethernet 1/4|172.16.2.1|255.255.255.0|
VPC3|Ethernet 1/4|172.16.3.1|255.255.255.0|
VPC4|Ethernet 1/5|172.16.4.1|255.255.255.0|

### Физическая схема сети


![Схема](physical_topology.jpg)

#### <u>Настройки, выполненные на оборудовании, для задействованных интерфейсов:</u>

SpineL:
```
interface Ethernet1/1
  no switchport
  ip address 10.1.0.1/30
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.1.0.5/30
  no shutdown

interface Ethernet1/3
  no switchport
  ip address 10.1.0.9/30
  no shutdown
  ```
  SpineR:
  ```
interface Ethernet1/1
   no switchport
  ip address 10.2.0.1/30
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.0.5/30
  no shutdown

interface Ethernet1/3
  no switchport
  ip address 10.2.0.9/30
  no shutdown
```
LEAF1:
```
interface Ethernet1/1
  no switchport
  ip address 10.1.0.2/30
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.0.2/30
  no shutdown

interface Ethernet1/7
  no switchport
  ip address 172.16.1.1/24
  no shutdown
  ```
  LEAF2:
  ```
interface Ethernet1/1
  no switchport
  ip address 10.1.0.6/30
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.0.6/30
  no shutdown

interface Ethernet1/7
  no switchport
  ip address 172.16.2.1/24
  no shutdown
  ```
  LEAF3:
  ```
interface Ethernet1/1
  no switchport
  ip address 10.1.0.10/30
  no shutdown

interface Ethernet1/2
  no switchport
  ip address 10.2.0.10/30
  no shutdown

interface Ethernet1/4
  no switchport
  ip address 172.16.3.1/24
  no shutdown

interface Ethernet1/5
  no switchport
  ip address 172.16.4.1/24
  no shutdown
