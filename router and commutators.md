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

## Настройка привилегированного режима доступа
enable secret <pswd> 

# Настройка использования локальных учётных данных для аутентификации
aaa <название>
aaa authentication login default local 
aaa authentication enable default enable # Настройка аутентификации в привилегированном режиме

## Показать список пользователей
do sh run

## Разрешаем авторизацию на виртуальном терминале — VTY

line vty 0 15
login local
transport input ssh # Настройка возможности удалённого подключения к VTY по SSH

## Обязательно устанавливаем пароль для привилегированного режима:
enable secret пароль
или заводим пользователя с привелегированными правами\
username ИМЯ privilage 15 secret ПАРОЛЬ

## Для SSH Генерируем ключ
crypto key generate rsa general-keys modulus 2048
ip ssh version 2 # Включение SSH-сервера
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
- int f0/4 выбираем порт или int range f0/2-3 // диапазон портов
- switchport trunk encapsulation dot1g  //для старых cisco
- switchport mode trunk
- switchport trunk all vl 10,20,30 //  указываем какие vlan передавать через это порт
- switchport trunk all vl add 40 // добавить к существующим
- switchport trunk all vl remove 40 // Убрать.
- show int trunk // посмотреть какие порты vlan передаються

## Настройка статического маршрута на маршрутизаторе
- ip routing // включение маршрутизации на коммнутаторе
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
- show ip arp // чьи arp запросы дошли до шлюза

## Определение информации о соседях
- show cdp neighbors detail // работает только на cisco
- lldp run
- show lldp neighbors detail // работает на всехвендорах

## Ограничение DHCP через комутатор
- ip dhcp snooping trust // сделать на выбраннос интерфейсе
- ip dhcp snooping // в настройки конфигурации сключаем функцию глобально
- ip dhcp snooping vlan 1 // указывает для какого vlan будет работь

## Сабинтерфейс расширение портов на маршрутизаторе
- int g0/1.100  // Создание сабинтерфейса
- encapsulation dot1q 100
- ip address (ip address mask) // задаем адрес и сакру для саб интерфейс
  
## Ускорение работы протакола STP
- spanning-tree mode rapid-pvst // в режиме конфигурации настроить на обоих коммутаторах. Ускоряет переключение между линиями.
- show spanning-tree //показать какие заблокированы порты
- show spanning-tree vlan xx // вы водим STP для определенноего VLAN
- show spanning-tree vlan xx detail //Выводим детализированную информацию Spanning tree для определенного vlan
- show spanning-tree vlan xx bridge //Показать статус и конфигурацию STP локального коммутатора для vlan
- spanning-tree cost <цена> //  на выбранном интерфейсе поменять стоимость порта
- spanning-tree vlan <vlan_list> priority <priority> # Включение PVRST во всех VLAN и назначение приоритета для рута
- spanning-tree vlan 1-4096 root primary // делает рутом данный коммутатор отстапая в приоретете на шага назад (2*4096)
- debug spanning-tree root //Посмотреть журнал событий STP касательно root:
- spanning-tree vlan 1-4096 root secondary // делаем заместителя рута и отсутпаем один шаг в приоретете (1*4096)
- spanning-tree bpduguard enable # Включение фильтрации BPDU на портах конечных устройств
- spanning-tree portfast # Настройка режима portfast на портах доступа
- spanning-tree portfast edge // настраивает порт для конечного устройства.
- panning-tree vlan <vlan_list> port-priority <priority> // настраиваем приоритете порта
- debug spanning-tree events //Посмотреть журнал общих событий STP

## Агрегирование портов на коммутаторах
- interface range fa0/1-2 // в режиме конфигурации выбираем 2 порта
- channel-group 1 mode on //объединение портов в одну группу. Сделать на обоих коммутаторах.
- show etherchannel summary — просмотр общей информации о EtherChannel
- show etherchannel port-channel — просмотр информации о EtherChannel по порт-каналу

## Настройка статической агрегации L3
- interface range f0/1-4 — выбор группы интерфейсов FastEthernet
- shutdown — отключение всех выбранных портов
- channel-group 1 mode on — создание группы для агрегации с технологией EtherChannel
- no shutdown — включить все выбранные порты
- exit
- interface PortChannel1 — выбор агрегированного интерфейса PortChannel1 для настройки
- no switchport — перевод интерфейса в режим L3
- ip address 192.168.1.1 255.255.255.0 — настройка IP-адреса на интерфейсе L3

## Балансировка агрегации
- show etherchannel load-balance - показать метод балансировки
- port-channel load-balance

## указания шлюза по умолчанию для коммутароров
- ip default-gateway 10.10.0.195

## Настройка шлюха по умочанию на 2- разных маршрутизаторах
Настройки GW1:
- standby 1(номер группы) ip 10.10.10.254 // в режиме настройки порта указать вертуальный IP
- standby 1 priority 200
- standby 1 preempt delay 60  - вытеснение 
- standby 1 authentication md5 XXX  - пароль 
- standby 1 track gigabitEthernet 0/0/1
Настройки GW2:
- standby 1 ip 192.168.1.1
- standby 1 priority 100
- standby 1 authentication md5 XXX

## Протакол VRRP
Настройки GW1:
- vrrp 1 ip 192.168.1.1
- vrrp 1 preempt delay minimum 15
- vrrp 1 authentication md5 XXXXXX
Настройки GW2:
- vrrp 1 ip 192.168.1.1
- vrrp 1 preempt delay minimum 15
- vrrp 1 authentication md5 XXXXXX

## Настройка GLBP
Настройки R1:
- glbp 1 ip 192.168.0.254
- glbp 1 priority 255
- glbp 1 preempt delay minimum 15
- glbp 1 authentication md5 XXXXXX
Настройки R2:
- glbp 1 ip 192.168.0.254
- glbp 1 preempt delay minimum 15
- glbp 1 authentication md5 XXXXXX
Настройки R3:
- glbp 1 ip 192.168.0.254
- glbp 1 priority 90
- glbp 1 authentication md5 XXXXXX

## Включение storm-control в контексте конфигурирования порта  
storm-control broadcast level <0.0 - 100.0> 

## Динамичесая маршрутизация. Протакол OSPF
- router ospf 10(номер Process ID)
- router-id 200.0.0.0 // задаем route id в формате IP
- network 10.1.1.0 0.0.0.255 area 0 //Area ID — идентификатор зоны для интерфейса этой подсети
- interface gi0/1
- ip ospf 10 area 0 // другой способ задать OSPF на порту
- ip ospf priority 10 //Приоритет для заданного интерфейса для выбора DR
- clear ip ospf process //Перезапуск процесса OSPF
- show ip ospf neighbor //Показывает информацию о протоколе, в том числе Router ID
- show ip ospf interface //Показывает существующих соседей и их состояния:
- show ip ospf database //Показывает информацию об OSPF-интерфейсах:
  
## Выход
