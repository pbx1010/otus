## Лабораторная работа: Underlay. BGP
Цель:
Настроить еBGP для Underlay сети
<dd>План работы:</dd>
Принципы назначения IP адресов:
Принципы выделения IPv4-адресов для построения Underlay сети:
<dd>• для организации внутренней связности выдаем адреса из приватной сети 10.0.0.0/8</dd>
<dd>• для p2p сетей между spine и leaf коммутаторами используем маску сети /31</dd>
<dd>• второй октет адреса - соответсвует номеру ДЦ (в нашем случае только один ДЦ)</dd>
<dd>• третий октет - соответсвует номеру spine</dd>
<dd>• четвёртый октет - значение по порядку, младший ip - spine, старший ip - leaf</dd>


Принципы назначения IPv4-адресов для подключения клиентов:
<dd>• для организации связности с клиентами выдаем адреса из приватной
сети 192.168.0.0/16</dd>
<dd>• для p2p сетей между leaf коммутаторами и клиентами используем маску сети /30</dd>
<dd>• третий октет адреса - соответсвует номеру leaf</dd>
<dd>• четвёртый октет - значение по порядку, младший ip - leaf, старший ip - Клиент</dd>
Принципы выделения IPv4-адресов для Loopback интерфейсов:
<dd>• для Loopback интерфейсов выдаем адреса из приватной сети 172.16.0.0/12</dd>
<dd>• второй октет адреса 16 - соответсвует номеру ДЦ (в нашем случае только один ДЦ)</dd>
<dd>• третий октет - 0 соответсвует уровню spine; 1 - соответствует уровню leaf</dd>
<dd>• четвёртый октет - значение по порядку, соответствует номеру spine/leaf</dd>
<dd></dd>

<dd>Распределение адресного пространства используется из предыдущих лабораторных работ
и отражено на схеме сети.</dd>
<dd>Распределение номеров автономных систем (AS):</dd>
<dd>Spine01: 65101</dd>
<dd>Spine02: 65102</dd>
<dd>Leaf01: 65001</dd>
<dd>Leaf01: 65002</dd>
<dd>Leaf01: 65003</dd>

## Типовая конфигурация для Leaf коммутаторов.
Предварительное условие:
<dd>• Выполнена настройка ip связности между spine и leaf коммутаторами</dd>
<dd>• Выполнена настройка Loopback интерфейсов</dd>
## Указываем явно Router ID:
<dd>set routing-options router-id %ip адрес лупбек интерфейса%</dd>
Задаем номер автономной системы:
<dd>set routing-options autonomous-system %%</dd>
## Настраиваем eBGP underlay:
<dd>set protocols bgp group POD-underlay export export-lo0</dd>
<dd>set protocols bgp group POD-underlay multipath multiple-as</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip spine01% description "Spine01"</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip spine01% peer-as 65101</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip spine02% description "Spine02"</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip spine02% peer-as 65102</dd>
## Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
<dd>set policy-options policy-statement export-lo0 term 1 from interface lo0.0</dd>
<dd>set policy-options policy-statement export-lo0 term 1 then accept</dd>
Настраиваем ECMP:
<dd>set routing-options forwarding-table export lbpp</dd>
<dd>set policy-options policy-statement lbpp term 1 then load-balance per-packet</dd>
## Типовая конфигурация для Spine коммутаторов.
Предварительное условие:
<dd>• Выполнена настройка ip связности между spine и leaf коммутаторами</dd>
<dd>• Выполнена настройка Loopback интерфейсов</dd>
Указываем явно Router ID:
<dd>set routing-options router-id %ip адрес лупбек интерфейса%</dd>
Задаем номер автономной системы:
<dd>set routing-options autonomous-system %%</dd>
## Настраиваем eBGP underlay:
<dd>set protocols bgp group POD-underlay export export-lo0</dd>
<dd>set protocols bgp group POD-underlay multipath multiple-as</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf01% description "Leaf01"</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf01% peer-as 65001</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf02% description "Leaf02"</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf02% peer-as 65002</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf03% description "Leaf03"</dd>
<dd>set protocols bgp group POD-underlay neighbor %ip Leaf03% peer-as 65003</dd>
## Настраиваем политику для экспорта ip адреса Loopback интерфейса в eBGP:
set policy-options policy-statement export-lo0 term 1 from interface lo0.0
set policy-options policy-statement export-lo0 term 1 then accept
Настраиваем ECMP:
<dd>set routing-options forwarding-table export lbpp</dd>
<dd>set policy-options policy-statement lbpp term 1 then load-balance per-packet</dd>




