University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33212

Author: Nikitina Iuliia Alekseevna

Lab: Lab2

Date of create: 20.11.2022

Date of finished: ...

## Отчет по лабораторной работе №2:
### ["Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"](https://itmo-ict-faculty.github.io/introduction-in-routing/education/labs2022_2023/lab2/lab2/)

#### 1. Цель:
Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств.

#### 2. Ход работы:


1. В первую очередь, в файле формата .yaml была составлена конфигурация по развертыванию сети связи. Содержимое этого файла представлено ниже:

```
name: lab2

mgmt:
  network: statics2
  ipv4_subnet: 190.10.10.0/24

topology:
  nodes:
    R01.BRL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.2

    R01.FRT:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.3

    R01.MSK:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.4

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.5

    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.6

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.10.10.7

  links:
    - endpoints: ["R01.BRL:eth1" , "R01.MSK:eth1"]
    - endpoints: ["R01.BRL:eth2" , "R01.FRT:eth1"]
    - endpoints: ["R01.MSK:eth2" , "R01.FRT:eth2"]
    - endpoints: ["R01.BRL:eth3" , "PC3:eth1"]
    - endpoints: ["R01.FRT:eth3" , "PC2:eth1"]
    - endpoints: ["R01.MSK:eth3" , "PC1:eth1"]
 ```

2. Следом за этим была произведена настройка каждого элемента сети: трех роутеров (в Берлине, Франкфурте и Москве) и трех персональных компьютеров, находящихся в этих городах. 

Конфигурация **R01.BRL**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool1 ranges=192.168.1.10-192.168.1.254
/ip dhcp-server
add address-pool=pool1 disabled=no interface=ether4 name=dhcp1
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=160.1.0.1/30 interface=ether2 network=160.1.0.0
add address=160.1.1.1/30 interface=ether3 network=160.1.1.0
add address=192.168.1.1/24 interface=ether4 network=192.168.1.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route
add distance=1 dst-address=192.168.2.0/24 gateway=160.1.1.2
add distance=1 dst-address=192.168.3.0/24 gateway=160.1.0.2
/system identity
set name=R01.BRL
```

Конфигурация **R01.FRT**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool2 ranges=192.168.2.10-192.168.2.254
/ip dhcp-server
add address-pool=pool2 disabled=no interface=ether4 name=dhcp2
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=160.1.1.2/30 interface=ether2 network=160.1.1.0
add address=160.1.2.2/30 interface=ether3 network=160.1.2.0
add address=192.168.2.1/24 interface=ether4 network=192.168.2.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route
add distance=1 dst-address=192.168.1.0/24 gateway=160.1.1.1
add distance=1 dst-address=192.168.3.0/24 gateway=160.1.2.1
/system identity
set name=R01.FRT
```

Конфигурация **R01.MSK**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool3 ranges=192.168.3.10-192.168.3.254
/ip dhcp-server
add address-pool=pool3 disabled=no interface=ether4 name=dhcp3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=160.1.0.2/30 interface=ether2 network=160.1.0.0
add address=160.1.2.1/30 interface=ether3 network=160.1.2.0
add address=192.168.3.1/24 interface=ether4 network=192.168.3.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route
add distance=1 dst-address=192.168.1.0/24 gateway=160.1.0.1
add distance=1 dst-address=192.168.2.0/24 gateway=160.1.2.2
/system identity
set name=R01.MSK
```

Конфигурация **PC1**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether2
/ip route
add distance=1 dst-address=160.1.0.0/30 gateway=192.168.3.1
add distance=1 dst-address=160.1.2.0/30 gateway=192.168.3.1
add distance=1 dst-address=192.168.2.0/24 gateway=192.168.3.1
add distance=1 dst-address=192.168.3.0/24 gateway=192.168.3.1
/system identity
set name=PC1
```

Конфигурация **PC2**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether2
/ip route
add distance=1 dst-address=160.1.1.0/30 gateway=192.168.2.1
add distance=1 dst-address=160.1.2.0/30 gateway=192.168.2.1
add distance=1 dst-address=192.168.1.0/24 gateway=192.168.2.1
add distance=1 dst-address=192.168.3.0/24 gateway=192.168.2.1
/system identity
set name=PC2
```

Конфигурация **PC3**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether2
/ip route
add distance=1 dst-address=160.1.0.0/30 gateway=192.168.1.1
add distance=1 dst-address=160.1.1.0/30 gateway=192.168.1.1
add distance=1 dst-address=192.168.2.0/24 gateway=192.168.1.1
add distance=1 dst-address=192.168.3.0/24 gateway=192.168.1.1
/system identity
set name=PC3
```

3. В качестве проверки работы сети была протестирована передача данных между компьютерами PC1, PC2, PC3. Для этого была использована утилита ping. Результаты проверки представлены ниже.

Ping с компьютера **PC1**:

/


Ping с компьютера **PC2**:

/


Ping с компьютера **PC3**:

/


#### 3. Вывод:

...
