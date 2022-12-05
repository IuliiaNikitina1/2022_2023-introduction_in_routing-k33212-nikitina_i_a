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

2. ///

**Схема сети**:

![схема](//)


Конфигурация **R01.BRL**:

```
/

```

Конфигурация **R01.FRT**:

```
/
```

Конфигурация **R01.MSK**:

```
/
```

Конфигурация **PC1**:

```
/
```

Конфигурация **PC2**:

```
/
```

Конфигурация **PC3**:

```
/
```

3. В качестве проверки работы сети была протестирована передача данных между ///. Для этого была использована утилита ping. Результаты проверки представлены ниже.

Ping с 

///


Ping с 

///


Ping с 

//


#### 3. Выводы:

  В ходе выполнения данной лабораторной работы ///
