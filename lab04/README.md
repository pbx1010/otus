Лабораторная работа: Underlay. BGP
Цель:
Настроить еBGP для Underlay сети
План работы:
Принципы назначения IP адресов:
Принципы выделения IPv4-адресов для построения Underlay сети:
<dd>• для организации внутренней связности выдаем адреса из приватной сети 10.0.0.0/8</dd>
<dd>• для p2p сетей между spine и leaf коммутаторами используем маску сети /31</dd>
<dd>• второй октет адреса - соответсвует номеру ДЦ (в нашем случае только один ДЦ)</dd>
<dd>• третий октет - соответсвует номеру spine</dd>
<dd>• четвёртый октет - значение по порядку, младший ip - spine, старший ip - leaf</dd>
Принципы назначения IPv4-адресов для подключения клиентов:
• для организации связности с клиентами выдаем адреса из приватной
сети 192.168.0.0/16
• для p2p сетей между leaf коммутаторами и клиентами используем маску сети /30
• третий октет адреса - соответсвует номеру leaf
• четвёртый октет - значение по порядку, младший ip - leaf, старший ip - Клиент
Принципы выделения IPv4-адресов для Loopback интерфейсов:
• для Loopback интерфейсов выдаем адреса из приватной сети 172.16.0.0/12
• второй октет адреса 16 - соответсвует номеру ДЦ (в нашем случае только один ДЦ)
• третий октет - 0 соответсвует уровню spine; 1 - соответствует уровню leaf
• четвёртый октет - значение по порядку, соответствует номеру spine/leaf
Распределение адресного пространства используется из предыдущих лабораторных работ
и отражено на схеме сети.
Распределение номеров автономных систем (AS):
Spine01: 65101
Spine02: 65102
Leaf01: 65001
Leaf01: 65002
Leaf01: 65003
Типовая конфигурация для Leaf коммутаторов.
Предварительное условие:
• Выполнена настройка ip связности между spine и leaf коммутаторами
• Выполнена настройка Loopback интерфейсов
Указываем явно Router ID:
set routing-options router-id %ip адрес лупбек интерфейса%
Задаем номер автономной системы:
set routing-options autonomous-system %%
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor %ip spine01% description "Spine01"
set protocols bgp group POD-underlay neighbor %ip spine01% peer-as 65101
set protocols bgp group POD-underlay neighbor %ip spine02% description "Spine02"
set protocols bgp group POD-underlay neighbor %ip spine02% peer-as 65102
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Типовая конфигурация для Spine коммутаторов.
Предварительное условие:
• Выполнена настройка ip связности между spine и leaf коммутаторами
• Выполнена настройка Loopback интерфейсов
Указываем явно Router ID:
set routing-options router-id %ip адрес лупбек интерфейса%
Задаем номер автономной системы:
set routing-options autonomous-system %%
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor %ip Leaf01% description "Leaf01"
set protocols bgp group POD-underlay neighbor %ip Leaf01% peer-as 65001
set protocols bgp group POD-underlay neighbor %ip Leaf02% description "Leaf02"
set protocols bgp group POD-underlay neighbor %ip Leaf02% peer-as 65002
set protocols bgp group POD-underlay neighbor %ip Leaf03% description "Leaf03"
set protocols bgp group POD-underlay neighbor %ip Leaf03% peer-as 65003
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Конфигурация Leaf01.
Указываем явно Router ID:
set routing-options router-id 172.16.1.1
Задаем номер автономной системы:
set routing-options autonomous-system 65001
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor 10.1.1.0 description "Spine01"
set protocols bgp group POD-underlay neighbor 10.1.1.0 peer-as 65101
set protocols bgp group POD-underlay neighbor 10.1.2.0 description "Spine02"
set protocols bgp group POD-underlay neighbor 10.1.2.0 peer-as 65102
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Конфигурация Leaf02.
Указываем явно Router ID:
set routing-options router-id 172.16.1.2
Задаем номер автономной системы:
set routing-options autonomous-system 65002
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor 10.1.1.2 description "Spine01"
set protocols bgp group POD-underlay neighbor 10.1.1.2 peer-as 65101
set protocols bgp group POD-underlay neighbor 10.1.2.2 description "Spine02"
set protocols bgp group POD-underlay neighbor 10.1.2.2 peer-as 65102
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Конфигурация Leaf03.
Указываем явно Router ID:
set routing-options router-id 172.16.1.3
Задаем номер автономной системы:
set routing-options autonomous-system 65003
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor 10.1.1.4 description "Spine01"
set protocols bgp group POD-underlay neighbor 10.1.1.4 peer-as 65101
set protocols bgp group POD-underlay neighbor 10.1.2.4 description "Spine02"
set protocols bgp group POD-underlay neighbor 10.1.2.4 peer-as 65102
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Конфигурация для Spine01.
Указываем явно Router ID:
set routing-options router-id 172.16.0.1
Задаем номер автономной системы:
set routing-options autonomous-system 65101
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor 10.1.1.1 description "Leaf01"
set protocols bgp group POD-underlay neighbor 10.1.1.1 peer-as 65001
set protocols bgp group POD-underlay neighbor 10.1.1.3 description "Leaf02"
set protocols bgp group POD-underlay neighbor 10.1.1.3 peer-as 65002
set protocols bgp group POD-underlay neighbor 10.1.1.5 description "Leaf03"
set protocols bgp group POD-underlay neighbor 10.1.1.5 peer-as 65003
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
Конфигурация для Spine02.
Указываем явно Router ID:
set routing-options router-id 172.16.0.2
Задаем номер автономной системы:
set routing-options autonomous-system 65102
Настраиваем eBGP underlay:
set protocols bgp group POD-underlay export export-lo0
set protocols bgp group POD-underlay multipath multiple-as
set protocols bgp group POD-underlay neighbor 10.1.2.1 description "Leaf01"
set protocols bgp group POD-underlay neighbor 10.1.2.1 peer-as 65001
set protocols bgp group POD-underlay neighbor 10.1.2.3 description "Leaf02"
set protocols bgp group POD-underlay neighbor 10.1.2.3 peer-as 65002
set protocols bgp group POD-underlay neighbor 10.1.2.5 description "Leaf03"
set protocols bgp group POD-underlay neighbor 10.1.2.5 peer-as 65003
Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
set routing-options forwarding-table export lbpp
set policy-options policy-statement lbpp term 1 then load-balance per-packet
