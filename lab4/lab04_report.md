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

В первой части лабораторной работы была собрана и настроена IP/MPLS сеть связи для компании "RogaIKopita Games". В сети были настроены интерфейсы на роутерах (R01.NY, R01.LND, R01.SPB, R01.SVL, R01.HKI и R01.LBN), а также устанoвлено соединение с конечными устройствами (PC1, PC2, PC3). Помимо этого, на роутерах были настроены протоколы OSPF и MPLS и iBGP с route reflector кластером.

Схема сети связи с подробной информацией об адресах сетей и всеми интерфейсами представлена на схема ниже:

![схема](https://github.com/IuliiaNikitina1/2022_2023-introduction_in_routing-k33212-nikitina_i_a/blob/main/lab4/lab4.drawio.png)

1. Первым делом в файле формата .yaml была составлена конфигурация по развертыванию сети связи. Содержимое этого файла представлено ниже:

```
name: lab4

mgmt:
  network: statics4
  ipv4_subnet: 190.20.20.0/24

topology:
  
  nodes:
    R01.NY: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9 
      mgmt_ipv4: 190.20.20.12

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.13

    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.14

    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.15

    R01.LBN:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.16
    
    R01.SVL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.17

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.18
    
    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.19

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 190.20.20.20

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

2. Следом за развертыванием сети была произведена настройка каждого роутера. Текст всех конфигураций представлен ниже:

* R01.NY:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default redistribute-connected=yes router-id=1.1.1.1
/routing ospf instance
set [ find default=yes ] router-id=1.1.1.1
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=1.1.1.1 interface=Lo network=1.1.1.1
add address=160.10.1.1/30 interface=ether2 network=160.10.1.0
add address=192.168.2.1/30 interface=ether3 network=192.168.2.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=65530:100 import-route-targets=65530:100 interfaces=\
    ether3 route-distinguisher=65530:100 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether3
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=\
    2.2.2.2 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.NY
```

* R01.LND:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=2.2.2.2
/routing ospf instance
set [ find default=yes ] router-id=2.2.2.2
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=2.2.2.2 interface=Lo network=2.2.2.2
add address=160.10.1.2/30 interface=ether2 network=160.10.1.0
add address=160.10.2.1/30 interface=ether3 network=160.10.2.0
add address=160.10.3.1/30 interface=ether4 network=160.10.3.0
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=5.5.5.5 remote-as=65530 \
    route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=3.3.3.3 remote-as=65530 \
    route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=1.1.1.1 remote-as=65530 \
    update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.LND
```

* R01.HKI:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=3.3.3.3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=3.3.3.3 interface=Lo network=3.3.3.3
add address=160.10.2.2/30 interface=ether2 network=160.10.2.0
add address=160.10.5.1/30 interface=ether3 network=160.10.5.0
add address=160.10.4.1/30 interface=ether4 network=160.10.4.0
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=\
    2.2.2.2 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=\
    5.5.5.5 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=\
    4.4.4.4 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.HKI
```

* R01.SPB:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default router-id=4.4.4.4
/routing ospf instance
set [ find default=yes ] router-id=4.4.4.4
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=4.4.4.4 interface=Lo network=4.4.4.4
add address=160.10.5.2/30 interface=ether2 network=160.10.5.0
add address=192.168.1.1/30 interface=ether3 network=192.168.1.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=65530:100 import-route-targets=65530:100 interfaces=ether3 \
    route-distinguisher=65530:100 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=\
    3.3.3.3 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.SPB
```

* R01.LBN:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=5.5.5.5
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=5.5.5.5 interface=Lo network=5.5.5.5
add address=160.10.3.2/30 interface=ether2 network=160.10.3.0
add address=160.10.6.1/30 interface=ether4 network=160.10.6.0
add address=160.10.4.2/30 interface=ether3 network=160.10.4.0
/ip dhcp-client
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=\
    2.2.2.2 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer2 remote-address=\
    3.3.3.3 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer3 remote-address=\
    6.6.6.6 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.LBN
```

* R01.SVL:

```
/interface bridge
add name=Lo
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance
set default redistribute-connected=yes 
/routing ospf instance
set [ find default=yes ] router-id=6.6.6.6
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=6.6.6.6 interface=Lo network=6.6.6.6
add address=160.10.6.2/30 interface=ether2 network=160.10.6.0
add address=192.168.3.1/30 interface=ether3 network=192.168.3.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route vrf
add export-route-targets=65530:100 import-route-targets=65530:100 interfaces=\
    ether3 route-distinguisher=65530:100 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp peer
add address-families=ip,l2vpn,l2vpn-cisco,vpnv4 name=peer1 remote-address=\
    5.5.5.5 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity
set name=R01.SVL
```


* Часть 2. Настройка VPLS

//


#### 3. Выводы:

  .
