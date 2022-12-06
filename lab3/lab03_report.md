University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33212

Author: Nikitina Iuliia Alekseevna

Lab: Lab3

Date of create: 05.12.2022

Date of finished: ...


## Отчет по лабораторной работе №3:
### ["Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS"](https://itmo-ict-faculty.github.io/introduction-in-routing/education/labs2022_2023/lab3/lab3/)

#### 1. Цель:
Изучить протоколы OSPF и MPLS, механизмы организации EoMPLS.


#### 2. Ход работы:

1. В первую очередь, в файле формата .yaml была составлена конфигурация по развертыванию сети связи. Содержимое этого файла представлено ниже:

```
name: lab3

mgmt:
  network: statics3
  ipv4_subnet: 172.10.10.0/24

topology:
  
  nodes:
    R01.NY: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9 
      mgmt_ipv4: 172.10.10.2

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.10.10.3

    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.10.10.4

    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.10.10.5

    R01.MSK:
      kind: vr-ros
      image: ubuntu:latest
      mgmt_ipv4: 172.10.10.6
    
    R01.LBN:
      kind: vr-ros
      image: ubuntu:latest
      mgmt_ipv4: 172.10.10.7

    SGI-Prism:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.10.10.8

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.10.10.9

  links:
    - endpoints: ["SGI-Prism:eth1","R01.NY:eth1"]
    - endpoints: ["R01.NY:eth2","R01.LND:eth1"]
    - endpoints: ["R01.LND:eth2","R01.HKI:eth1"]
    - endpoints: ["R01.NY:eth3","R01.LBN:eth1"]
    - endpoints: ["R01.LBN:eth2","R01.HKI:eth2"]
    - endpoints: ["R01.LBN:eth3","R01.MSK:eth1"]
    - endpoints: ["R01.MSK:eth2","R01.SPB:eth1"]
    - endpoints: ["R01.HKI:eth3","R01.SPB:eth2"]
    - endpoints: ["R01.SPB:eth3","PC1:eth1"]
 ```

2. Следом за этим была выполнена конфигурация роутеров в шести разных городах (R01.NY, R01.LND, R01.LBN, R01.HKI, R01.MSK, R01.SPB), а также конечных устройств (SGI-Prism и RC1). 

**Схема сети**:

![схема](//)

Конфигурация включала в себя:

* настройку IP адресов на интерфейсах;

* настройку OSPF и MPLS;

* настройку EoMPLS;

* назначение адресации на контейнеры, связанные между собой EoMPLS.


Конфигурация **R01.NY**:

```
/interface bridge
add name=EoMPLS_B
add name=Lo0
/interface vpls
add cisco-style=yes cisco-style-id=100 disabled=no l2mtu=1500 mac-address=\
    02:9F:31:44:50:A4 name=EoMPLS remote-peer=4.4.4.4
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=1.1.1.1
/interface bridge port
add bridge=EoMPLS_B interface=ether2
add bridge=EoMPLS_B interface=EoMPLS
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.1.1/30 interface=ether3 network=172.20.1.0
add address=172.20.2.1/30 interface=ether4 network=172.20.2.0
add address=1.1.1.1 interface=Lo0 network=1.1.1.1
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=1.1.1.1
/mpls ldp interface
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity
set name=R01.NY

```

Конфигурация **R01.LND**:

```
/interface bridge
add name=Lo0
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.1.2/30 interface=ether2 network=172.20.1.0
add address=172.20.3.1/30 interface=ether3 network=172.20.3.0
add address=2.2.2.2 interface=Lo0 network=2.2.2.2
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity
set name=R01.LND
```

Конфигурация **R01.HKI**:

```
/interface bridge
add name=Lo0
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=3.3.3.3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.3.2/30 interface=ether2 network=172.20.3.0
add address=172.20.4.1/30 interface=ether3 network=172.20.4.0
add address=172.20.5.1/30 interface=ether4 network=172.20.5.0
add address=3.3.3.3 interface=Lo0 network=3.3.3.3
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity
set name=R01.HKI
```

Конфигурация **R01.SPB**:

```
/interface bridge
add name=EoMPLS_B
add name=Lo0
/interface vpls
add cisco-style=yes cisco-style-id=100 disabled=no l2mtu=1500 mac-address=02:A3:EA:0D:77:AF \
    name=EoMPLS remote-peer=1.1.1.1
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=4.4.4.4
/interface bridge port
add bridge=EoMPLS_B interface=ether4
add bridge=EoMPLS_B interface=EoMPLS
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.5.2/30 interface=ether2 network=172.20.5.0
add address=172.20.6.1/30 interface=ether3 network=172.20.6.0
add address=4.4.4.4 interface=Lo0 network=4.4.4.4
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity
set name=R01.SPB
```

Конфигурация **R01.MSK**:

```
/interface bridge
add name=Lo0
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=5.5.5.5
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.7.1/30 interface=ether2 network=172.20.7.0
add address=172.20.6.2/30 interface=ether3 network=172.20.6.0
add address=5.5.5.5 interface=Lo0 network=5.5.5.5
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity
set name=R01.MSK
```

Конфигурация **R01.LBN**:

```
/interface bridge
add name=Lo0
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=6.6.6.6
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=172.20.2.2/30 interface=ether2 network=172.20.2.0
add address=172.20.4.2/30 interface=ether3 network=172.20.4.0
add address=172.20.7.2/30 interface=ether4 network=172.20.7.0
add address=6.6.6.6 interface=Lo0 network=6.6.6.6
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity
set name=R01.LBN
```

Конфигурация **SGI-Prism**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=160.0.0.1/24 interface=ether2 network=160.0.0.0
/ip dhcp-client
add disabled=no interface=ether1
/system identity
set name=SGI-Prism
```

Конфигурация **PC1**:

```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=160.0.0.2/24 interface=ether2 network=160.0.0.0
/ip dhcp-client
add disabled=no interface=ether1
/system identity
set name=PC1
```

3. Готовые **таблицы MPLS** маршрутов представлены на скринах ниже. Они были получены с помощью команды:

```
mpls forwarding-table print
```

* **R01.NY**:

![R01.NY](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/NY.png)

* **R01.LND**:

![R01.LND](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/LND.png)

* **R01.HKI**:

![R01.HKI](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/HKI.png)

* **R01.SPB**:

![R01.SPB](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/SPB.png)

* **R01.MSK**:

![R01.MSK](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/MSK.png)


* **R01.LBN**:

![R01.LBN](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/LBN.png)


4. В качестве проверки работы сети была протестирована передача данных между устройствами SGI-Prism и PC1. Для этого была использована утилита ping. Результаты проверки представлены ниже.

* Ping с **SGI-Prism** на PC1: 

![SGI](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/sgi.png)


* Ping с **PC1** на SGI-prism:

![PC1](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab3/images/PC1.png)



#### 3. Выводы:

  В ходе выполнения данной лабораторной работы была успешно собрана схема связи IP/MPLS сети компании "RogaIKopita Games". На шести роутерах (R01.NY, R01.LND, R01.HKI, R01.SPB, R01.MSK, R01.LBN) были настроены  EOPF и MPLS, а также EoMPLS. На контейнерах, связанных между собой EoMPLS, была назначена адресация. На всех интерфейсах были указаны IP-адреса. В результате удалось установить соединение между устройствами SGI-Prism и PC1, расположенными в удаленных регионах.
  
  
  
