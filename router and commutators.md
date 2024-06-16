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

## Витуальный канал для OSPF для соединения ARIA
- router ospf 1  // Наройтере стыковом
- aria 12 virtual-link 172.16.1.1 // Указываем aria через которую будем проходить и id(ip) роутера на выходе. Так же делаем с противоположной стороны.

## BGP

- R1(config-router)#neighbor <ip-address | peer-group-name> remote-as <as-number>

# Настройка BGP-соседства
R1(config)#router bgp 110
R1(config-router)#neighbor 5.5.5.5 remote-as 110
R1(config-router)#neighbor 5.5.5.5 update-source loopback0

# Опция eBGP multihop
R1(config-router)# neighbor <ip-address | peer-group-name> ebgp-multihop [ttl]
С помощью настройки eBGP multihop вы указываете максимальное количество
допустимых прыжков. Это пропускает проверку BGP для TTL на значение равное 1:
По умолчанию BGP проверяет, находится ли eBGP-сосед в непосредственно
присоединённой сети локального маршрутизатора.
Эту проверку отключает параметр disable-connected-check

# BGP-аутентификация 
Аутентификация с шифрованием Message Digest 5 (MD5) — это результат простогозадания пароля на устройствах:
R1(config#router bgp 220
R1(config-router)#neighbor 8.8.8.8 remote-as 110
R1(config-router)#neighbor 8.8.8.8 update-source loopbackO
R1(config-router)#neighbor 8.8.8.8 ebgp-multihop 2
R1(config-router)#neighbor 8.8.8.8 password MySuperSecret121
R1(config-router)#end

# Route-map
Например:
● Пакеты, прибывающие из источника с адресов 1.1.1.1, направляются на адрес 192.168.1.1
● Пакеты, прибывающие из источника с адресом 2.2.2.2, направляются на адрес 192.168.2.2
access-list 1 permit host 1.1.1.1
access-list 2 permit host 2.2.2.2
route-map BGP1 permit 10
match ip address 1
set ip default next-hop 192.168.1.1
route-map BGP2 permit 20
match ip address 2
set ip default next-hop 192.168.2.2
Если маршрутизатор не имеет никакого явного маршрута для этих пакетов

## BGP Управление трафиком
# AS-path acl 
С помощью AS-path acl вы можете, например, запретить принимать анонсы маршрутов, принадлежащих AS200.
«.» — любой символ, включая пробел;
«*» — ноль или больше совпадений с выражением;
«+» — одно или больше совпадений с выражением;
«?» — ноль или одно совпадение с выражением;
«^» — начало строки;
«$» — конец строки;
«_» — любой разделитель, включая начало, конец, пробел;
«\» — не воспринимать следующий символ как специальный;
«[ ]»— совпадение с одним из символов в диапазоне;
«|»— логическое «или»
Чтобы стало понятнее, приведём несколько примеров.

127 — маршруты, проходящие через AS127. До и после номера AS идут знаки «_», означающие, что в AS-path номер 200 может стоять в начале, середине или конце, главное, чтобы он был;
^127$ — маршруты из соседней AS127. «^» означает начало списка, а «$» – конец. То есть в AS-path всего один номер AS – это означает, что маршрут был зарождён в AS127 и оттуда сразу был передан нам;
127$ — маршруты, отправленные из AS127. «$» означает конец списка, то есть это самая первая AS, из неё маршрут и зародился, знак «» говорит о том, что неважно, что находится дальше, хоть ничего, хоть семь других AS;
^127_ — сети, находящиеся за AS127. Знак «^» означает, что ASN 200 была добавлена последней, то есть маршрут к нам пришёл из AS200, но это не значит, что родился он в ней же. Знак «_» говорит о том, что это может быть конец списка, а может быть пробел перед следующей AS;
^$ — маршруты локальной AS. Список AS-path пуст, значит, маршрут локальный, сгенерированный внутри нашей AS.

Создаём AS-path acl.
- ip as-path access-list 200 deny _200_
- ip as-path access-list 200 permit .*
- neighbor 30.30.30.1 filter-list 200 in  // filter-list это тот же access-list, анонсы настречу трафику

# Префикс-листы — это привычные нам сеть или маска, и мы указываем, разрешить такие маршруты или нет.
Синтаксис команды:

ip prefix-list {list-name} [seq {value}] {deny|permit} {network/length} [ge {value}] [le {value}], где:

- list-name — название списка. Обычно указывается, как name_in или name_out. Это подсказывает нам, на входящие или исходящие маршруты он будет действовать, но, конечно, на этом этапе никак не определяет;
- seq — порядковый номер правила, как в acl, чтобы проще было оперировать с ними;
- deny/permit — определяем, разрешать такой маршрут или нет;
- network/length— привычная для нас запись, вроде 192.168.14.0/24.
Дальше возможны ещё два параметра:
- ge и le — это означает «greater or equal» и «less or equal». То есть вы можете задать не только один конкретный префикс, но и их диапазон.
Например, запись типа:
ip prefix-list test-in seq 10 permit 100.100.100.0/23 ge 24 le 29 —
означает, что под действие prefix-list попадают маршруты длиной от 24 до 29, входящие в 100.100.100.0/23.
Например, 100.100.101.0/24, 100.100.100.0/26, 100.100.101.56/29.
neighbor 30.30.30.1 prefix-list as300-out out

# Route-map — это инструмент для выборочного применения правил к только определённым маршрутам. AS-path acl и prefix-list позволяют управлять сразу всеми анонсами, полученными или анонсируемыми конкретному пиру, а route-map позволяет отфильтровать конкретные маршруты по тому или иному признаку. У других вендоров route-map может называться route-policy или route-filter.

Синтаксис команды следующий:

route-map {map_name} {permit|deny} {seq}
[match {expression}]
[set {expression}], где:

- map_name – имя карты;
permit/deny – разрешаем или нет прохождение данных, подпадающих под условия route-map;
seq – номер правила в route-map;
match – условие подпадания трафика под это правило. Expression: критерий.
Команда конфигурации:
Network/mask match ip address prefix-list;
AS-path match as-path;
BGP community match community;
Route originator match ip route-source;
BGP next-hop address match ip next-hop.
- set– что сделать с отфильтрованными маршрутами. Expression: параметры.
Команда конфигурации:
AS path prepend set as-path prepend;
Weight set weight;
Local Preference set local-preference;
BGP community set community;
MED set metric;
Origin set origin;
BGP next-hop set next-hop.

Пример

В указанной топологии при анонсах сети 100.100.100.0/24 искуственно увеличим AS-path.
Создадим prefix-list AS300.
- ip prefix-list AS300 permit 100.100.100.0/24
Prefix-list содержит только одно правило. Следующим правилом будет неявное правило deny. Это значит, что под конкретное правило нашей карты маршрутов попадёт только анонс сети 100.100.100.0/24.
Создадим route-map AS300-out и применим его к пиру.
- route-map AS300-uot permit 10
- match ip address prefix-list AS300
- set as-path prepend 100 100 100
  
## Управление исходящим трафиком

# Использовать атрибут weight

R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 weight 200  // По-умолчанию = 0. Чем больше значение атрибута, тем более предпочтительный маршрут.

Либо:

R1(config)#route-map weight-200
R1(config-route-map)#set weight 200

R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 route-map weight-200 in

# Local Preference. Пример настройки
Замена значения по умолчанию:
R1(config)#router bgp 100
R1(config-router)#bgp default local-preference <0-4294967295>
Назначение на маршруты, выбранные с помощью route-map:
R1(config)#route-map local-pref-200
R1(config-route-map)#set local-preference 500
R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 route-map local-pref-200 in

# При использовании MED есть следующие нюансы:

По умолчанию MED на анонсах из разных AS не сравнивается. Правда, есть метод это изменить.

Чем больше MED, тем маршрут менее предпочтительный.

MED передаётся в соседнюю AS, но только в первую, т. е. ту, которая имеет соседство с вами.

Пункт 1 больше всего влияет на политики, где используется MED для распределения исходящего трафика.

- При использовании AS-path также есть нюанс: AS-path — транзитивный атрибут. Если выявляется транзитной AS, то все ваши клиенты или пиры получат маршрут с модифицированным вами AS-path.

Настраивается изменение MED и AS-path также через route-map.
На примере MED:

R1(config)#route-map med-200
R1(config-route-map)#set metric 200
R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 route-map med-200 in

# Балансировка
Трафик можно балансировать до одной и той же сети назначения по разным каналам.
Есть некоторые условия, которые должны быть выполнены для осуществления балансировки.

Совпадение атрибутов:
weight;
Local Preference;
AS-path — не длина, а весь путь. Это значит, что не только количество AS должно совпадать но и номера и порядок AS, т. е. по умолчанию балансировка работает только с маршрутами, полученными от одного провайдера;
origin code;
MED;
IGP metric.
Next hop — маршрутизатор для каждого из маршрутов должен быть разным.
Настраивается:

R1(config)#router bgp 100
R1(config-router)#maximum-paths 2

Чтобы обойти ограничение на совпадение AS-path, можно использовать скрытую команду:

R1(config)#router bgp 100
R1(config)#bgp bestpath as-path multipath-relax

По умолчанию не учитывается разница пропускной способности разных каналов, можно включить такую опцию:

router bgp 100
bgp dmzlink-bw
neighbor 20.20.20.1 dmzlink-bw
neighbor 30.30.30.1 dmzlink-bw

# Управление входящим трафиком

- AS-path prepend
R1(config)#route-map as-prepend-x3
R1(config-route-map)#set as-path prepend 100 100 100

R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 route-map as-prepend-x3 out

# Анонс разных сетей разным операторам
Допустим, в нашей AS100 порождается сеть 100.100.100.0/23. У нас есть 2 стыка с AS200 и AS300, и для распределения нагрузки по входящему трафику мы можем:

распилить /23 сеть на 2 /24;
в один стык анонсировать 1 /24, в другой — другую;
в оба стыка анонсировать более общую /23.

Настраивается это так:

R1(config)#router bgp 100
R1(config-router)#network 100.100.100.0 mask 255.255.255.0
R1(config-router)#network 100.100.101.0 mask 255.255.255.0
R1(config-router)#network 100.100.100.0 mask 255.255.254.0
R1(config)#ip route 100.100.100.0 255.255.255.0 Null0
R1(config)#ip route 100.100.101.0 255.255.255.0 Null0
R1(config)#ip route 100.100.100.0 255.255.254.0 Null0

Конфигурация для R2 идентичная.

R1(config)#ip prefix-list net-to-as200 permit 100.100.100.0/24
R1(config)#ip prefix-list net-to-as200 permit 100.100.100.0/23

R2(config)#ip prefix-list net-to-as300 permit 100.100.101.0/24
R2(config)#ip prefix-list net-to-as300 permit 100.100.100.0/23
R1(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 remote-as 200
R1(config-router)#neighbor 20.20.20.1 prefix-list net-to-as200 out

R2(config)#router bgp 100
R2(config-router)#neighbor 30.30.30.1 remote-as 300
R2(config-router)#neighbor 30.30.30.1 prefix-list net-to-as300 out

# BGP community
Чтобы применить community к анонсам из своей AS, достаточно в route-map выполнить set:

R3(config)#route-map community-set
R3(config-route-map)#set community 200:200 additive

R3(config)#router bgp 100
R1(config-router)#neighbor 20.20.20.1 send-community
R3(config-router)#neighbor 20.20.20.1 route-map community-set out

Community. Создание community-list
Стандартный community-list:
ip community-list <1-99> <permit | deny> <value>
Пример:
ip community-list 1 deny 100:37
ip community-list 1 permit 0:0
Расширенный community-list:
ip community-list <100-199> <permit | deny> <regexp>
ip community-list 200 deny [1-0]+:[1-3][2-2]
ip community-list 200 permit [3-4]00:0

# Community. Применение
Настройка community для обработки маркированных маршрутов:
R3(config)#ip bgp-community new-format
R3(config)#ip community-list standard loc-pref-100 permit
100:100
R3(config)#route-map client-loc-pref-in
R3(config-route-map)#match community loc-pref-100
R3(config-route-map)#set local-preference 100
R3(config)#router bgp 200
R3(config-router)#neighbor 20.20.20.2 route-map
client-loc-pref-in in

# Как обрабатывать входящие анонсы с community

R3(config)#ip community-list standard loc-pref-100 permit 100:100

R3(config)#route-map client-loc-pref-in
R3(config-route-map)#match community loc-pref-100
R3(config-route-map)#set local-preference 100

R3(config)#router bgp 200
R3(config-router)#neighbor 20.20.20.2 route-map client-loc-pref-in in

## Оптимизация iBGP

Как можно улучшить ситуацию

# Peer group позволяет объединять соседей в группы и одной командой задавать нужные параметры сразу всем.
Настраивается так:

R1(config)#router bgp 200
R1(config-router)#neighbor AS200 peer-group
R1(config-router)#neighbor AS200 remote-as 200
R1(config-router)#neighbor AS200 update-source Loopback0
R1(config-router)#neighbor AS200 next-hop-self
R1(config-router)#neighbor 10.10.10.2 peer-group AS200
R1(config-router)#neighbor 10.10.10.3 peer-group AS200
R1(config-router)#neighbor 10.10.10.4 peer-group AS200

В конфигурации выше мы создали peer-group AS200. Общие для всех участников группы параметры:

update-source,

next-hop-self,

remote-as.

В группу мы добавили 3 пира: 10.10.10.2, 10.10.10.3 и 10.10.10.4.

#  Route Reflector
R1(config)#router bgp 200
R1(config-router)#neighbor AS200 peer-group
R1(config-router)#neighbor AS200 remote-as 200
R1(config-router)#neighbor AS200 update-source Loopback0
R1(config-router)#neighbor AS200 route-reflector-client
R1(config-router)#neighbor 10.10.10.2 peer-group AS200
R1(config-router)#neighbor 10.10.10.3 peer-group AS200
R1(config-router)#neighbor 10.10.10.4 peer-group AS200

В конфигурации выше мы определили, что пиры 10.10.10.2, 10.10.10.3 и 10.10.10.4 — это клиенты route reflector.

## Передача данных между протаколами маршрутизации

# Чтобы запустить процесс редистрибуции в OSPF, необходимо использовать команду:
Router(config-router)#redistribute ?
- bgp Border Gateway Protocol (BGP)  //connected Connected
- eigrp //Enhanced Interior Gateway Routing Protocol (EIGRP)
- isis //ISO IS-IS
- ospf //Open Shortest Path First (OSPF)
- ospfv3 //OSPFv3
- rip //Routing Information Protocol (RIP)
- static //Static routes

В команде необходимо указать источник маршрутной информации. Это может быть как другой протокол (eigrp, bgp и т. д.), так и статические (static) или сети подключённых интерфейсов (connected)

Пример:
R1#show ip route static
10.0.0.0/24 is subnetted, 4 subnets
S 10.10.0.0 [1/0] via 192.168.1.1
S 10.10.1.0 [1/0] via 192.168.1.1
S 10.10.2.0 [1/0] via 192.168.1.1
S 10.10.3.0 [1/0] via 192.168.1.1
R1(config)#router ospf 1
R1(config-router)#redistribute static

R3(config)#router ospf 1
Router(config-router)#redistribute bgp 12345

# Опции редистрибуции в OSPF
R3(config-router)#redistribute bgp 12345 ?
- metric //Metric for redistributed routes позволяет задать метрику для маршрута вручную
- metric-type // OSPF/IS-IS exterior metric type for redistributed routes выбор 1 или 2. OSPF External Type 2 metrics — тип метрики, который использует стоимость внешнего маршрута:
при передаче по сети стоимость не увеличивается. OSPF External Type 1 metrics в этом случае будет использовать: метрику внешнего маршрута + метрику до ASBR, которую передал маршрут + метрику до ABR, которую
передал внешний маршрут в другую зону.
- nssa-only //Limit redistributed routes to NSSA areas редистрибуция из специальной тупиковой зоны
- route-map //Route map reference фильтрация редистрибуции при помощи политик
- subnets Consider subnets for redistribution into OSPF опция добавляется автоматически. На старых версиях без этой опции происходила
- tag  //Set tag for routes redistributed into OSPF использование метки для маршрутов

# Конфигурация типа зоны
R1(config-router)#area 10 stub  //Stub area — тупиковая зона
R1(config-router)#area 10 stub no-summary // Totally stubby area
R1(config-router)#area 10 nssa // Not-so-stubby area (NSSA)
R1(config-router)#area 10 nssa no-summary // Totally NSSA


## Методы контроля и управления доступом к сети

# Port security
Конфигурация port security производится на интерфейсе. Активирование:

Switch(config)#interface gigabitEthernet 0/1
Switch(config-if)#switchport port-security

Конфигурация разрешенных MAC-адресов:

Switch(config-if)#switchport port-security mac-address ?

- H.H.H Статическое задание разрешенного MAC-адреса
- forbidden Статическое задание запрещенного MAC-адреса
- sticky Динамическое изучение подключенных к интерфейсу MAC-адресов

Задание максимального количество допустимых MAC-адресов:

Switch(config-if)#switchport port-security maximum // <1-4097> Maximum addresses
Задание времени хранения в конфигурации динамически изученных sticky MAC-адресов:
Switch(config-if)#switchport port-security aging time  // <1-1440> Aging time in minutes. Enter a value between 1 and 1440

Если MAC-адрес в течение заданного времени неактивен, адрес удаляется из конфигурации

Настройка реакции на нарушение политик:

Switch(config-if)#switchport port-security violation ?
- protect Security violation protect mode
- restrict Security violation restrict mode
- shutdown Security violation shutdown mode

● Protect — когда количество MAC-адресов достигает предела и появился неизвестный MAC-адрес,
пакеты с неизвестными адресами отбрасываются. Уведомление о нарушении безопасности
в этом случае отсутствует
● Restrict — Protect mode, но при этом отправляются SNMP-уведомления о нарушении политик
● Shutdown — переводит порт в состояние error-disable. Никакой трафик не передаётся, пока интерфейс
не выйдет из этого состояния. Можно настроить таймер для error-disable recovery, чтобы восстановить
состояние порта через определённое время

show port-security interface Fa0/1  команда показывает общую настройку port security
show port-security address — команда показывает только статически и динамически разрешённые адреса


## Конфигурация dot1x на Cisco
# Активирование aaa-сервисов и конфигурация RADIUS-сервера:
Switch(config)#aaa new-model Включение aaa-сервисов
Switch(config)#radius server server_name Создание Radius-сервера
Switch(config-radius-server)#address ipv4 192.168.1.111 auth-port 1645 acct-port ?
<0-65535> Port number Задание IP-адреса и портов для аутентификации (auth) и учёта (acct)
Switch(config-radius-server)#key // 0 Specifies an UNENCRYPTED key will follow, 7 Specifies HIDDEN key will follow
LINE The UNCRYPTED (cleartext) shared key
Switch(config-radius-server)#key 0 radius_key Задание пароля

# Глобальная конфигурация dot1x-сервисов и серверов:

Switch(config)#dot1x system-auth-control Глобальное активирование dot1x
Switch(config)#aaa authentication dot1x default ?
group Использование группы Radius-сервер
local Использование локальной базы пользователей
Switch(config)#aaa authentication dot1x default group radius local
Задание последовательности использования источников:
1. radius cервера
2. local использование локальной базы при недоступности Radius-сервера

# Конфигурация порта для обмена сообщениями EAP и перевод в режим authenticator:
Switch(config)#interface Fa0/0
Switch(config-if)#authentication port-control auto
Задаёт режим auto, в котором порт сразу после поднятия будет автоматически обмениваться EAP-сообщениями с подключённым устройством
Switch(config-if)#dot1x pae authenticator //Задаёт роль порта как authenticator. Также порт может выступать в роли supplicant или both (authenticator + supplicant)

## Ролевая модель доступа
# Технологию RBAC можно настроить локально на свитче при помощи функции parser view
Switch(config)#parser view view_name Задание имени view
Switch(config-view)#?
- commands Задание команд свитча
- secret Задание пароля для view
Switch(config-view)#commands command_type ?
- exclude Запретить использование команды в этом view
- include Разрешить использование команды в этом view
- include-exclusive Разрешить в этом view, но исключить из других view

Switch(config)#parser view vlan_change_view //Задаём имя view
Switch(config-view)#secret password1 //Задаём пароль для этого view
Switch(config-view)#commands exec include all configure //Разрешаем команду configure в Exec mode
Switch(config-view)#commands configure include all interface //Разрешаем заходить в конфигурацию интерфейсов
Switch(config-view)#commands interface include switchport access vlan //Разрешаем команду switchport access vlan в Interface mode

## Конфигурация DHCP snooping
Глобальная активация DHCP snooping на свитче:

Switch(config)#ip dhcp snooping
Switch(config)#ip dhcp snooping vlan 10 //Активация DHCP snooping на свитче для определённого vlan:

Конфигурация trust-портов:
Switch(config)#interface Fa0/0
Switch(config-if)#ip dhcp snooping trust 

Конфигурация untrust-портов отсутствует, так как при включении ip dhcp snooping все порты становятся untrust-портами

# Включение или отключение DHCP option:
Switch(config)#ip dhcp snooping information option
Switch(config)#no ip dhcp snooping information option

Включение или отключение проверки на соответствие MAC-адреса
клиента ( L2 Header ) и MAC-адреса в DHCP-запросе:

Switch(config)#ip dhcp snooping verify mac-address
Switch(config)#no ip dhcp snooping verify mac-address по умолчанию, проверка соответствия MAC-адресов включена на свитче, при несовпадении DHCP пакет отбрасывается

# Информация о работе и настройке DHCP snooping:
Switch#show ip dhcp snooping покажет, какие функции включены, какие vlan и порты настроены как trust
Switch#show ip dhcp snooping binding покажет, с каких портов были сделаны запросы DHCP и какие адреса были выданы клиентам

# Глобальная активация ARP inspection для vlan:
Switch(config)#ip arp inspection vlan 10

# Конфигурация trust-портов:
Switch(config)#interface Fa0/0
Switch(config-if)#ip arp inspection trust

# Конфигурация ARP ACL-фильтров для статической проверки:
Switch(config)#ip arp inspection filter ?
WORD ARP acl name

# Дополнительные проверки ARP inspection 
Switch(config)#ip arp inspection validate ?
- dst-mac //Validate destination MAC address //Проверяется соответствие destination MAC-адреса в L2-заголовке и ARP-заголовке
- src-mac //Validate source MAC address //Проверяется соответствие source MAC-адреса в L2-заголовке и ARP-заголовке
- ip //Validate IP addresses //Проверяется правильность IP-адреса

Switch#show ip arp inspection ?
- interfaces Показывает статус (trust state) интерфейса и статистику
- log Показывает логи ARP inspection
- statistics Cтатистика ARP-проверок
- vlan Посмотреть статус для определённого vlan

## IP source guard

# IP source guard включается на интерфейсе коммутатора:
Switch(config)#interface Fa0/0
Switch(config-if)#ip verify source

# Конфигурация статического соответствия между mac/vlan/ip/port:
Switch(config)#ip source binding 0050.7966.6809 vlan 10 192.168.10.105 interface Gi0/3

show ip verify source

# Чтобы проверить не только Source IP, но и MAC-адрес, нужно указать опцию port-security
Switch(config)#interface Fa0/1
Switch(config-if)#ip verify source port-security

## NAT
- show ip nat translation
Inside global — адрес и порт отправителя в интернете после процедуры трансляции на маршрутизаторе
Inside local — адрес и порт отправителя в оригинальном пакете до процедуры трансляции
Outside local — адрес и порт получателя в оригинальном пакете до процедуры трансляции
Outside global — адрес и порт получателя пакета в интернете после процедуры трансляции

# Настройка static NAT
1. Выбираем inside-интерфейс. На него приходит пакет,который должен быть преобразован. Важно: один интерфейс не может быть одновременно inside и outside
interface GigabitEthernet0/0
ip nat inside
2. Выбираем outside-интерфейс
interface GigabitEthernet0/1
ip nat outside
3. Пишем правила обработки пакетов
ip nat inside source static 192.168.1.10 10.10.10.2
ip nat outside source static 10.10.10.10 192.168.1.2
ip route 192.168.1.2 255.255.255.255 10.10.10.10
или
ip nat inside source static 192.168.1.10 10.10.10.2
ip nat outside source static 10.10.10.10 192.168.1.2 add-route

# Dynamic NAT
Выюираем так же inside и outside  порты
1. Создаём пул публичных IP-адресов
ip nat pool pool2 173.10.15.20 173.10.15.110
2. Создаём acl для фильтрации внутренних IP-адресов
access-list 100 permit ip 10.10.10.0 0.0.0.255 any
3. Пишем правило преобразования пакета. В нём описывается, какие пакеты должны быть транслированы
ip nat inside source list 100 pool2

# PAT Port address translation
Пишем правило преобразования пакета. В нём описывается, какие пакеты должны быть транслированы
ip nat inside source static tcp 10.10.10.100 23 173.10.15.12 2323

# PAT overload:
1. Задаём ACL, фильтрующий внутренние адреса, требующие трансляции
access-list 100 permit ip 10.10.10.0 0.0.0.255 any
2. Пишем правило преобразования пакета. В нём описывается, какие пакеты должны быть транслированы
ip nat inside source list 100 interface GigabitEthernet0/0 overload
или
ip nat inside source list 100 pool pool2 overload


## VPN

R1#interface Tunnel0
tunnel mode ipip
ip address 10.0.0.1 255.255.255.0
tunnel source 1.1.1.1
tunnel destination 2.2.2.2
R1(config)#ip route 192.168.20.0 255.255.255.0 10.0.0.2

R2#interface Tunnel0
tunnel mode ipip
ip address 10.0.0.2 255.255.255.0
tunnel source 2.2.2.2
tunnel destination 1.1.1.1
R2(config)#ip route 192.168.10.0 255.255.255.0 10.0.0.1

tunnel source — Source IP-адрес при инкапсуляции
tunnel destination — Destination IP-адрес при инкапсуляции
tunnel mode ipip — указание протокола инкапсуляции пакета

## IKE (Internet Key Exchange protocol) — протокол обмена ключами, который используют для формирования IPSec SA (Security Association)

# Алгоритмы хеширования и шифрования первой фазы
R1(config)#crypto isakmp policy ?
<1-10000> Priority of protection suite

R1(config)#crypto isakmp policy 10
R1(config-isakmp)#encryption ?
3des Three key triple DES
aes AES — Advanced Encryption Standard.
des DES — Data Encryption Standard (56 bit keys).

R1(config-isakmp)#hash ?
md5 Message Digest 5
sha Secure Hash Standard
sha256 Secure Hash Standard 2 (256 bit)
sha384 Secure Hash Standard 2 (384 bit)
sha512 Secure Hash Standard 2 (512 bit)

R1(config-isakmp)#group ?
1 Diffie-Hellman group 1 (768 bit)
14 Diffie-Hellman group 14 (2048 bit)
15 Diffie-Hellman group 15 (3072 bit)
16 Diffie-Hellman group 16 (4096 bit)
19 Diffie-Hellman group 19 (256 bit ecp)
2 Diffie-Hellman group 2 (1024 bit)
20 Diffie-Hellman group 20 (384 bit ecp)
21 Diffie-Hellman group 21 (521 bit ecp)
24 Diffie-Hellman group 24 (2048 bit, 256 bit subgroup)
5 Diffie-Hellman group 5 (1536 bit)
R1(config-isakmp)#group 5

R1(config-isakmp)#authentication ?
pre-share Pre-Shared Key
rsa-encr Rivest-Shamir-Adleman Encryption
rsa-sig Rivest-Shamir-Adleman Signature
R1(config-isakmp)#authentication pre-share
R1(config-isakmp)#crypto isakmp key password address Ip-адрес пира

# Конфигурация политик первой и второй фазы
crypto isakmp policy 10
encr aes 256
hash sha512
authentication pre-share
group 5
lifetime 3600

Настраиваем набор политик первой фазы с приоритетом 10. Предпочтение политикам с низшим приоритетом
crypto isakmp key pass address 100.0.0.2

Настраиваем пароль для адреса пира
crypto ipsec transform-set SEC55 ah-sha-hmac esp-aes 256 mode transport
Настраиваем трасформ-сет из протоколов шифрования второй фазы. Определяем набор протоколов ESP и AH

# Конфигурация политик первой и второй фазы
1. Трафик, который мы хотим зашифровать
access-list 110 permit ip 192.168.5.0 0.0.0.255 172.16.1.0 0.0.0.255 /
2. Создаём криптомап, который связывает тип трафика, пир и политики шифрования
crypto map Sec_MAP 10 ipsec-isakmp
set peer 100.0.0.2
set transform-set Sec
match address 110 
3. Привязываем к интерфейсу, который используется для установки шифрованного канала
interface gigabitEthernet 0/0
ip address 100.0.0.1 255.255.255.0
crypto map Sec_MAP

show crypto isakmp sa detail

## Выход
