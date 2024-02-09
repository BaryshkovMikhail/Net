# Настройки маршрутизатора и коммутаторов

## Вход 
enable

## Вход в настройки конфигурации
conf t (configure terminal)

## Задать имя маршрутизатора или коммутатору (нужно для SSH)
hostname ИМЯ

## Задать доменное имя для маршрутизатора или коммутатору (нужно для SSH)
ip domain-name ИМЯ

## Наставиваем пользователя
username ИМЯ password ПАРОЛЬ (В этом случае пароль не зашифрован, чтоб зашифровать, надо включить опцию)
service password-encryption (Шифрование пароля если заведен через password)
no service password-encryption (Отменяет шифрование паролей для новых пользователей. Старые остануться зашифрованные)

username ИМЯ secret ПАРОЛЬ (сразу за шифрован пароль)

## Показать список пользователей
do sh run

## Разрешаем авторизацию на виртуальном терминале — VTY

line vty 0 15
login local

## Обязательно устанавливаем пароль для привилегированного режима:
enable secret пароль
или заводим пользователя с привелегированными правами\
username ИМЯ privilage 15 secret ПАРОЛЬ

## Для SSH Генерируем ключ
crypto key generate rsa
How many bits in the modulus [512]: до 2048

## Настраиваем IP-адрес, если он не был настроен ранее:
- для маршрутизаторов
interface gig0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
- для коммутаторов
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown

## Настройка вертуального интерфэйса на маршрутизаторе
interface loopback 0
no shut
ip address 1.1.1.1 255.255.255.255

## Просмотр настроенных IP на маршрутизаторе
do sh ip int bri

## ПОказать версию SSH
show ip ssh (Если версия 1.99 то и обе версии сразу. Если надо включить только 2)
ip ssh vervion 2
do wr (СОХРАНЕНИЕ)

## Выбор протакола обмена.
line vty 0 15
transport input ssh 

## Настройкао консольного порта
line console 0
Login local // задаем локальный пароль

## Создание VLAN
Войти в настройки conf t
- vlan  и номер 
- name имя vlan // задавем имя 
- show vlan
- -show vlan br
  
## настройка портов и привяка в Vlan
- int f0/1 // Выбираем порт для конфигурации
- switchport mode access // режим доступности порта
- switchport access vl 10  // добавляем потр к нудному vlan
  
## Перевод в транковый порт(trunk)
- int f0/4 выбираем порт
- switchport mode trunk
- switchport trunk all vl 10,20,30 //  укалываем какие vlan передавать через это порт
- switchport trunk all vl add 40 // добавить к существующим
- witchport trunk all vl remove 40 // Убрать.
-  show int trunk // посмотреть какие порты vlan пердаються

## Настройка статического маршрута на маршрутизаторе
- ip ruote 192.168.1.0 255.255.255.0(целевая сеть) 10.1.1.254 (следующий щлюз) 10 (административная дистанция)
- show ip route // показать текущие маршруты
- no ip ruote 192.168.1.0 255.255.255.0 10.1.1.254 // удаление маршрута

## Настройка динамической маршрутизации RIP
- router rip
- version 2
- no auto-summary //отключаем автоматическое суммирование маршрутов
- network <классовая сеть>

## Настройка IP V6
- ipv6 address 2001::123/64 
- ipv6 address 2001::/64 eui-64
- Router(config)#unicast-routing
  

## Основнае команды на коммутарорах
- show running config
- show vlan
- show vlan brief
- show interfaces trunk
- show ip route
- show interfaces
- show run interfaces (нет в симуляторе)
- show mac-address table
- show arp

## Выход
exit