### Настроить OSPF.

## Цель:

- Собрать схему;  
   ![img_1.png](img_1.PNG)   

- Настроить OSPF офисе Москва
- Разделить сеть на зоны
- Настроить фильтрацию между зонами

## Задачи:

- Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
- Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.
- Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.
- Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.
- Настройка для IPv6 повторяет логику IPv4.