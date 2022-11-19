University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33212

Author: Nikitina Iuliia Alekseevna

Lab: Lab1

Date of create: 04.11.2022

Date of finished: ...

## Отчет по лабораторной работе №1:
### ["Установка ContainerLab и развертывание тестовой сети связи"](https://itmo-ict-faculty.github.io/introduction-in-routing/education/labs2022_2023/lab1/lab1/)

#### 1. Цель:
Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации.

#### 2. Ход работы:

1. После установки программ Docker и ContainerLab и клонирования репозитория 
[hellt/vrnetlab](https://github.com/hellt/vrnetlab) на компьютере был создан новый образ.

2. На базе ContainerLab была развернута сеть трехуровневого предприятия, состоящая из следующего оборудования:
одного роутера (R01.TEST), одного коммутаторов первого уровня (SW01.L3.01.TEST),
двух коммутаторв второго уровня (SW02.L3.01.TEST и SW02.L3.02.TEST), а также двух конечных устройств (PC1 и PC2).
Конфигурация файла сети представлена ниже:

```
name: lab1

mgmt:
    network: statics
    ipv4_subnet: 172.20.20.0/24

topology:
  
    nodes:
        R01.TEST:
            kind: vr-ros
            image: vrnetlab/vr-routeros:6.47.9 
            mgmt_ipv4: 172.20.20.2

        SW01.L3.01.TEST:
            kind: vr-ros
            image: vrnetlab/vr-routeros:6.47.9
            mgmt_ipv4: 172.20.20.3

        SW02.L3.01.TEST:
            kind: vr-ros
            image: vrnetlab/vr-routeros:6.47.9
            mgmt_ipv4: 172.20.20.4

        SW02.L3.02.TEST:
            kind: vr-ros
            image: vrnetlab/vr-routeros:6.47.9
            mgmt_ipv4: 172.20.20.5

        PC1:
            kind: linux
            image: ubuntu:latest
            mgmt_ipv4: 172.20.20.6
    
        PC2:
            kind: linux
            image: ubuntu:latest
            mgmt_ipv4: 172.20.20.7

    links:
        - endpoints: ["R01.TEST:eth1", "SW01.L3.01.TEST:eth1"]
        - endpoints: ["SW01.L3.01.TEST:eth2", "SW02.L3.01.TEST:eth1"]
        - endpoints: ["SW02.L3.01.TEST:eth2", "PC1:eth1"]
        - endpoints: ["SW01.L3.01.TEST:eth3", "SW02.L3.02.TEST:eth1"]
        - endpoints: ["SW02.L3.02.TEST:eth2", "PC2:eth1"]
```

3. Следующим шагом была написана конфигурация для каждого сетевого устройства.

3.1 Для роутера R01.TEST:

![R01.TEST](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab1/R01.TEST.png)

3.2 Для коммутатора SW01.L3.01.TEST:

![SW01.L3.01.TEST](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab1/SW01.L3.01.png)

3.3 Для коммутатора SW02.L3.01.TEST:

![SW02.L3.01.TEST](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab1/SW02.L3.01.png)

3.4 Для коммутатора SW02.L3.02.TEST:

![SW02.L3.02.TEST](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab1/SW02.L3.02.png)
