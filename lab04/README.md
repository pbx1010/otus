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



<dd>root@LEAF-01> ping 172.16.0.1</dd>
<dd>PING 172.16.0.1 (172.16.0.1): 56 data bytes</dd>
<dd>64 bytes from 172.16.0.1: icmp_seq=0 ttl=64 time=128.931 ms</dd>
<dd>64 bytes from 172.16.0.1: icmp_seq=1 ttl=64 time=175.771 ms</dd>
<dd>64 bytes from 172.16.0.1: icmp_seq=2 ttl=64 time=125.804 ms</dd>
<dd>64 bytes from 172.16.0.1: icmp_seq=3 ttl=64 time=115.109 ms</dd>
<dd>64 bytes from 172.16.0.1: icmp_seq=4 ttl=64 time=120.655 ms</dd>
<dd>^C</dd>
<dd>--- 172.16.0.1 ping statistics ---</dd>
<dd>5 packets transmitted, 5 packets received, 0% packet loss</dd>
<dd>round-trip min/avg/max/stddev = 115.109/133.254/175.771/21.771 ms</dd>

<dd>{master:0}</dd>
<dd>root@LEAF-01> ping 172.16.0.2</dd>
<dd>PING 172.16.0.2 (172.16.0.2): 56 data bytes</dd>
<dd>64 bytes from 172.16.0.2: icmp_seq=0 ttl=64 time=137.927 ms</dd>
<dd>64 bytes from 172.16.0.2: icmp_seq=1 ttl=64 time=120.264 ms</dd>
<dd>64 bytes from 172.16.0.2: icmp_seq=2 ttl=64 time=140.855 ms</dd>
<dd>64 bytes from 172.16.0.2: icmp_seq=3 ttl=64 time=139.917 ms</dd>
<dd>^C</dd>
<dd>--- 172.16.0.2 ping statistics ---</dd>
<dd>4 packets transmitted, 4 packets received, 0% packet loss</dd>
<dd>round-trip min/avg/max/stddev = 120.264/134.741/140.855/8.425 ms</dd>


<dd>root@LEAF-01> show bgp summary</dd>
<dd>Groups: 1 Peers: 2 Down peers: 0</dd>
<dd>Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending</dd>
<dd>inet.0</dd>
<dd>                       6          6          0          0          0          0</dd>
<dd>Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...</dd>
<dd>10.1.1.0              65101       4622       4642       0       2 1d 10:55:25 3/3/3/0              0/0/0/0</dd>
<dd>10.1.2.0              65101      13249      13294       0       0  4d 4:03:27 3/3/3/0              0/0/0/0</dd>



root@SPINE-01> show bgp summary
Groups: 1 Peers: 3 Down peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...<dd>
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
