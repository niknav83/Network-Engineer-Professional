### Настроить DHCPv6.

## Цель:

- Собрать схему;  
   ![img_1.png](img_1.PNG)   

- Настроить DHCPv6;

## Задачи:

 - Построение сети и настройка основных параметров устройства
 - Проверка назначения адреса SLAAC из R1
 - Настройка и проверка сервера DHCPv6 без состояния на R1
 - Настройка и проверка сервера DHCPv6 с отслеживанием состояния на R1
 - Настройка и проверка реле DHCPv6 на R2



## Собираем указанную схему
![img_2.png](img_2.PNG)


## Таблица адресов
| Device  | Interface | IP Address Subnet Mask | Default Gateway |
|---------|-----------|------------------------|-----------------|
| R1      | e0/0      | 2001:db8:acad:2::1 /64 |                 | 
|         |           | fe80::1                |                 | 
|         | e0/1      | 2001:db8:acad:1::1/64  |                 | 
|         |           | fe80::1                |                 | 
| R2      | e0/0      | 2001:db8:acad:2::2/64  |                 | 
|         |           | fe80::2                |                 | 
|         | e0/1      | 2001:db8:acad:3::1 /64 |                 | 
|         |           | fe80::1                |                 | 
| PC-A    |           | DHCP                   |                 | 
| PC-B    |           | DHCP                   |                 | 

 
### [Файлы конфигураций устройст и сама работа выполненная в EVE-NG ](https://github.com/niknav83/Network-Engineer-Professional/tree/main/labs/lab03.2/configs)
В данной работе применялись следующие образы:
 - L3-ADVENTERPRISEK9-M-15.4-2T.bin
 - L2-ADVENTERPRISEK9-M-15.2-20150703.bin

# Приступаем к настрйке устройств:

<details>

<summary> Настраиваем базовые параметры для маршрутизатора R1: </summary>

```

```
</details>

<details>

<summary> Настраиваем базовые параметры для маршрутизатора R2: </summary>

```

```
</details>


<details>

<summary> Настраиваем базовые параметры для коммутатора S1: </summary>

```

```
</details>

<details>

<summary> Настраиваем базовые параметры для коммутатора S2: </summary>

```

```
</details>


<details>

<summary> Настраиваем PC-A: </summary>

```

```
</details>


<details>

<summary> Настраиваем PC-B: </summary>

```

```
</details>

<details>

<summary> Настраиваем VLAN на коммутаторе S1 </summary>

```

```

```

```
```

```
```


```

</details>


<details>

<summary> Настраиваем VLAN на коммутаторе S2 </summary>

```

```

```

```
```

```
```


```

</details>



# Приступаем к проверке устройств:

