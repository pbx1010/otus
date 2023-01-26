## Лабораторная работа: Underlay. BGP
Цель:
Настроить еBGP для Underlay сети

![2023-01-20_19-13-28.png](2023-01-20_19-13-28.png)

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




root@SPINE-01> show bgp summary
Groups: 1 Peers: 3 Down peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.1.1              65001         50         48       0       0       21:09 0/0/0/0              0/0/0/0
10.1.1.3              65002         48         47       0       0       21:01 0/0/0/0              0/0/0/0
10.1.1.5              65003          0          0       0       0       25:31 Connect


root@SPINE-02> show bgp summary
Groups: 1 Peers: 3 Down peers: 2
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.2.1              65001          0          0       0       0       27:44 Connect
10.1.2.3              65002          0          0       0       0       27:44 Connect
10.1.2.5              65003         55         54       0       0       23:27 0/0/0/0              0/0/0/0

root@LEAF-01> show bgp summary
Groups: 1 Peers: 2 Down peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.1.0              65101         58         58       0       0       25:28 0/0/0/0              0/0/0/0
10.1.2.0              65102          0          0       0       0       29:57 Idle

root@LEAF-02> show bgp summary
Groups: 1 Peers: 2 Down peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.1.2              65101         60         59       0       0       26:06 0/0/0/0              0/0/0/0
10.1.2.2              65102          0          0       0       0       30:37 Idle


root@LEAF-03> show bgp summary
Groups: 1 Peers: 2 Down peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.1.4              65101          0          0       0       0       31:31 Connect
10.1.2.4              65102         62         61       0       0       27:03 0/0/0/0              0/0/0/0
