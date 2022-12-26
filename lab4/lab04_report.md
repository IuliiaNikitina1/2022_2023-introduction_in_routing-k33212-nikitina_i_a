University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33212

Author: Nikitina Iuliia Alekseevna

Lab: Lab4

Date of create: 08.12.2022

Date of finished: ...

## Отчет по лабораторной работе №4:
### ["Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"](https://itmo-ict-faculty.github.io/introduction-in-routing/education/labs2022_2023/lab4/lab4/)

#### 1. Цель:

  Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.


#### 2. Ход работы:

* Часть 1. Настройка L3VPN

В первой части лабораторной работы была собрана и настроена IP/MPLS сеть связи для компании "RogaIKopita Games". В частности в сети были настроены все интерфейсы на шести роутерах R01.NY, 01.LND, R01.SPB, R01.SVL, R01.HKI и R01.MSK, а также устанавлено соединение с конечныыми устройствами (PC1, PC2, PC3). Помимо этого, были настроены OSPF с MPLS и iBGP с route reflector кластером.

Схема сети связи с подробной информацией об адресах сетей и всеии интерфейсами представлена на схема ниже:

[схема]!(https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab4/lab4.drawio.png)

1. В первую очередь, в файле формата .yaml была составлена конфигурация по развертыванию сети связи. Содержимое этого файла представлено ниже:

```
name: lab4

mgmt:
  network: statics4
  ipv4_subnet: 160.20.20.0/24

topology:
  
  nodes:
    R01.NY: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9 
      mgmt_ipv4: 160.20.20.2

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.3

    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.4

    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.5

    R01.LBN:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.6
    
    R01.SVL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.7

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.8
    
    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.9

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 160.20.20.10

  links:
    - endpoints: ["R01.NY:eth1","R01.LND:eht1"]
    - endpoints: ["R01.LND:eth2","R01.HKI:eth1"]
    - endpoints: ["R01.LND:eth3","R01.LBN:eth1"]
    - endpoints: ["R01.HKI:eth2","R01.SPB:eth1"]
    - endpoints: ["R01.HKI:eth3","R01.LBN:eth2"]
    - endpoints: ["R01.LBN:eth3","R01.SVL:eth1"]
    - endpoints: ["PC1:eth1","R01.SPB:eth2"]
    - endpoints: ["PC2:eth1","R01.NY:eth2"]
    - endpoints: ["PC3:eth1","R01.SVL:eth2"]
```

2. ///


* Часть 2. Настройка VPLS

//


#### 3. Выводы:

  .
