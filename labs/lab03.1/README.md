### Настроить DHCPv4.

## Цель:

- Собрать схему;  
   ![img_1.png](img_1.PNG)   

- Настроить DHCPv4;

## Задачи:

- Построение сети и настройка основных параметров устройства
- Настройка и проверка двух серверов DHCPv4 на R1
- Настройка и проверка DHCP-ретранслятора на R2



## Собираем указанную схему
![img_2.png](img_2.PNG)


## Таблица адресов
| Device  | Interface | IP Address   | Subnet Mask     | Default Gateway |
|---------|-----------|--------------|-----------------|-----------------|
| R1      | e0/0      | 10.0.0.1     | 255.255.255.252 |                 |
|         | e0/1      |              |                 |                 |
|         | e0/1.100  | 192.168.1.1  | 255.255.255.192 |                 |
|         | e0/1.200  | 192.168.1.65 | 255.255.255.224 |                 |
|         | e0/1.1000 |              |                 |                 |
| R2      | e0/0      | 10.0.0.2     | 255.255.255.252 |                 |
|         | e0/1      | 192.168.1.97 | 255.255.255.240 |                 |
| S1      | VLAN 200  | 192.168.1.66 | 255.255.255.224 | 192.168.1.65    |
| S2      | VLAN 1    | 192.168.1.98 | 255.255.255.240 | 192.168.1.97    |
| PC-A    |           | DHCP         | DHCP            | DHCP            |
| PC-B    |           | DHCP         | DHCP            | DHCP            |
 

## Таблица VLAN
| VLAN |    Name      | Назначенный интерфейс |
|------|--------------|-----------------------|
| 1    |              | S2 e0/3               |
| 100  | Clients      | S1 e0/3               |
| 200  | Management   | S1 VLAN 200           |
| 999  | Parking_Lot  | S1 e0/0,e0/2          |
| 1000 | Native       |                       |


### [Файлы конфигураций устройст и сама работа выполненная в EVE-NG ](https://github.com/niknav83/Network-Engineer-Professional/tree/main/labs/lab03.1/configs)
В данной работе применялись следующие образы:
 - L3-ADVENTERPRISEK9-M-15.4-2T.bin
 - L2-ADVENTERPRISEK9-M-15.2-20150703.bin

# Приступаем к настрйке устройств:

<details>

<summary> Настраиваем базовые параметры для маршрутизатора R1: </summary>

```
Router#conf terminal 
Router(config)#hostname R1
R1(config)#no ip domain lookup 
R1(config)#enable secret class
R1(config)#line console 0
R1(config-line)#password cisco 
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4 
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption 
R1(config)#banner motd $ Authorized Users Only! $
R1(config)#exit
R1#clock set 10:22:00 06 Nov 2024
R1#wr
```
</details>

<details>

<summary> Настраиваем базовые параметры для маршрутизатора R2: </summary>

```
Router#conf terminal 
Router(config)#hostname R2
R2(config)#no ip domain lookup 
R2(config)#enable secret class
R2(config)#line console 0
R2(config-line)#password cisco 
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 4 
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption 
R2(config)#banner motd $ Authorized Users Only! $
R2(config)#exit
R2#clock set 10:22:00 06 Nov 2024
R2#wr
```
</details>


<details>

<summary> Настраиваем базовые параметры для коммутатора S1: </summary>

```
Switch#conf terminal 
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)# enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)# line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd $ Authorized Users Only! $
S1(config)#interface range ethernet 0/0, ethernet 0/2
S1(config-if-range)#shutdown
S1(config)#exit
S1#clock set 10:22:00 06 Nov 2024
S1#wr

```
</details>

<details>

<summary> Настраиваем базовые параметры для коммутатора S2: </summary>

```
Switch#conf terminal 
Switch(config)#hostname S2
S2(config)#no ip domain-lookup
S2(config)# enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)# line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd $ Authorized Users Only! $
S2(config)#interface range ethernet 0/0, ethernet 0/2
S2(config-if-range)#shutdown
S2(config)#exit
S2#clock set 10:22:00 06 Nov 2024
S2#wr
```
</details>


<details>

<summary> Настраиваем маршрутизацию между VLAN на R1 </summary>

```
R1#conf terminal
R1(config)#interface ethernet 0/1
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface ethernet 0/1.100
R1(config-subif)#description Client Network
R1(config-subif)#encapsulation dot1q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit
R1(config)#interface e0/1.200
R1(config-subif)#encapsulation dot1q 200
R1(config-subif)#description Management Network
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit
R1(config)#interface e0/1.1000
R1(config-subif)#encapsulation dot1q 1000 native
R1(config-subif)#description Native VLAN
R1(config-subif)#exit
R1(config)#interface e0/0
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
R1(config)#exit
R1#wr
```

</details>


<details>

<summary> Настраиваем маршрутизацию между VLAN на R2 </summary>

```
R2#conf terminal
R2(config)#interface e0/1
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#interface e0/0
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#exit
R2#wr
```

</details>


<details>

<summary> Настраиваем VLAN на коммутаторе S1 </summary>

```
S1#conf terminal
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#do wr
```
```
S1(config)#interface vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.65
S1(config)#do wr
```
```
S1(config)#interface range ethernet 0/0, ethernet 0/2
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
S1(config)#do wr
```
```
S1(config)# interface fe0/3
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 100
```
```
S1(config)#interface e0/1
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk encapsulation dot1q
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#exit
S1(config)#do wr
```

</details>


<details>

<summary> Настраиваем VLAN на коммутаторе S2 </summary>

```
S2#conf terminal
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.1.97
S2(config)#do wr
```
```
S2(config)#interface range ethernet 0/0, ethernet 0/2
S2(config-if-range)# switchport mode access
S2(config-if-range)# shutdown
S2(config-if-range)# exit
S2(config)#do wr
```

</details>


<details>

<summary> Настраиваем два сервера DHCPv4 на R1 </summary>

```
R1#conf terminal
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp pool R1_Client_LAN
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#exit
R1(config)#exit
R1#wr
```

</details>


<details>

<summary> Настраиваем DHCP Relay на R2 </summary>

```
R2(config)# interface e0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#no shut
R2(config-if)#end
R2#wr
```

</details>



<details>

<summary> Настраиваем PC-A: </summary>

```
VPCS> set pcname PC-A

PC-A> dhcp
DORA IP 192.168.1.6/26 GW 192.168.1.1

PC-A>
PC-A> save
Saving startup configuration to startup.vpc
.  done

PC-A>
```
</details>


<details>

<summary> Настраиваем PC-B: </summary>

```
VPCS> set pcname PC-B

PC-B> dhcp
DORA IP 192.168.1.102/28 GW 192.168.1.97

PC-B> save
Saving startup configuration to startup.vpc
.  done

PC-B>
```
</details>


# Приступаем к проверке устройств:

## Проверяем настройки VLAN на коммутаторе S1

<details>

<summary> show vlan </summary>

```
S1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active
100  Clients                          active    Et0/3
200  Management                       active
999  Parking_Lot                      active    Et0/0, Et0/2
1000 Native                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
100  enet  100100     1500  -      -      -        -    -        0      0
200  enet  100200     1500  -      -      -        -    -        0      0
999  enet  100999     1500  -      -      -        -    -        0      0
1000 enet  101000     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
1004 fdnet 101004     1500  -      -      -        ieee -        0      0
1005 trnet 101005     1500  -      -      -        ibm  -        0      0

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

```
</details>


<details>

<summary> show interfaces status </summary>

```
S1#show interfaces status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0                        disabled     999          auto   auto unknown
Et0/1                        connected    trunk        auto   auto unknown
Et0/2                        disabled     999          auto   auto unknown
Et0/3                        connected    100          auto   auto unknown
```
</details>


<details>

<summary> show ip interface brief </summary>

```
S1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  administratively down down
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  up                    up
Vlan200                192.168.1.66    YES NVRAM  up                    up
```
</details>

<details>

<summary> show interfaces trunk </summary>

```
S1#show interfaces trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/1       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/1       100,200,1000

Port        Vlans allowed and active in management domain
Et0/1       100,200,1000

Port        Vlans in spanning tree forwarding state and not pruned
Et0/1       100,200,1000
```
</details>



## Проверяем настройки VLAN на коммутаторе S2

<details>

<summary> show vlan </summary>

```
S2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
1004 fdnet 101004     1500  -      -      -        ieee -        0      0
1005 trnet 101005     1500  -      -      -        ibm  -        0      0

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

```
</details>


<details>

<summary> show interfaces status </summary>

```
S2#show interfaces status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0                        disabled     1            auto   auto unknown
Et0/1                        connected    1            auto   auto unknown
Et0/2                        disabled     1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
```
</details>


<details>

<summary> show ip interface brief </summary>

```
S2#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  administratively down down
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  up                    up
Vlan1                  192.168.1.98    YES manual up                    up
```
</details>


## Проверяем настройки на маршрутизаторе R1

<details>

<summary> show ip interface brief </summary>

```
R1#show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.1        YES manual up                    up
Ethernet0/1                unassigned      YES unset  up                    up
Ethernet0/1.100            192.168.1.1     YES manual up                    up
Ethernet0/1.200            192.168.1.65    YES manual up                    up
Ethernet0/1.1000           unassigned      YES unset  up                    up
Ethernet0/2                unassigned      YES unset  administratively down down
Ethernet0/3                unassigned      YES unset  administratively down down

```
</details>

<details>

<summary> show  ip route </summary>

```
R1#show  ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.2
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.1/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 4 subnets, 3 masks
C        192.168.1.0/26 is directly connected, Ethernet0/1.100
L        192.168.1.1/32 is directly connected, Ethernet0/1.100
C        192.168.1.64/27 is directly connected, Ethernet0/1.200
L        192.168.1.65/32 is directly connected, Ethernet0/1.200
```
</details>


<details>

<summary> show ip dhcp pool </summary>

```
R1#show ip dhcp pool

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 62
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.7          192.168.1.1      - 192.168.1.62      1

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 14
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.103        192.168.1.97     - 192.168.1.110     1
```
</details>

<details>

<summary> show ip dhcp binding </summary>

```
R1#show ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.03       Nov 12 2024 05:56 PM    Automatic
192.168.1.102       0100.5079.6668.04       Nov 12 2024 05:57 PM    Automatic

```
</details>


## Проверяем настройки на маршрутизаторе R2

<details>

<summary> show ip interface brief </summary>

```
R2#show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.2        YES manual up                    up
Ethernet0/1                192.168.1.97    YES manual up                    up
Ethernet0/2                unassigned      YES unset  administratively down down
Ethernet0/3                unassigned      YES unset  administratively down down

```
</details>

<details>

<summary> show  ip route </summary>

```
R2#show  ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.1
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.2/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.96/28 is directly connected, Ethernet0/1
L        192.168.1.97/32 is directly connected, Ethernet0/1

```
</details>


## Проверяем работу сети с PC-A

<details>

<summary> Отправьте эхо-запрос с PC-A на шлюз по умолчанию </summary>

```
PC-A> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=0.474 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=1.073 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=0.880 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=0.669 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=1.045 ms

PC-A>
```
</details>

<details>

<summary> Отправьте эхо-запрос с PC-A на PC-B </summary>

```
PC-A> ping 192.168.1.97

84 bytes from 192.168.1.97 icmp_seq=1 ttl=254 time=0.987 ms
84 bytes from 192.168.1.97 icmp_seq=2 ttl=254 time=1.491 ms
84 bytes from 192.168.1.97 icmp_seq=3 ttl=254 time=1.520 ms
84 bytes from 192.168.1.97 icmp_seq=4 ttl=254 time=1.503 ms
84 bytes from 192.168.1.97 icmp_seq=5 ttl=254 time=1.611 ms

PC-A>
```
</details>


## Проверяем работу сети с PC-B

<details>

<summary> Отправьте эхо-запрос с PC-B на шлюз по умолчанию </summary>

```
PC-B> ping 192.168.1.97

84 bytes from 192.168.1.97 icmp_seq=1 ttl=255 time=0.719 ms
84 bytes from 192.168.1.97 icmp_seq=2 ttl=255 time=0.837 ms
84 bytes from 192.168.1.97 icmp_seq=3 ttl=255 time=1.218 ms
84 bytes from 192.168.1.97 icmp_seq=4 ttl=255 time=1.015 ms
84 bytes from 192.168.1.97 icmp_seq=5 ttl=255 time=0.961 ms


```
</details>

<details>

<summary> Отправьте эхо-запрос с PC-B на PC-A </summary>

```
PC-B> ping 192.168.1.6

84 bytes from 192.168.1.6 icmp_seq=1 ttl=62 time=8.200 ms
84 bytes from 192.168.1.6 icmp_seq=2 ttl=62 time=2.197 ms
84 bytes from 192.168.1.6 icmp_seq=3 ttl=62 time=2.192 ms
84 bytes from 192.168.1.6 icmp_seq=4 ttl=62 time=1.876 ms
84 bytes from 192.168.1.6 icmp_seq=5 ttl=62 time=1.909 ms

PC-B>
```
</details>

