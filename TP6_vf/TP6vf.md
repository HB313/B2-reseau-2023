

# TP6 INFRA : STP, OSPF, bigger infra







## I. STP

### 1. Basic setup


#### 🌞 Configurer STP sur les 3 switches

```

IOU5#show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Altn BLK 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
 --More--

```


#### 🌞 Altérer le spanning-tree en désactivant un port

désactiver juste un port de un switch pour provoquer la mise à jour de STP

```
IOU4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU4(config)#int Ethernet0/2
IOU4(config-if)#shutdown
IOU4(config-if)#
*Mar 31 20:34:03.794: %LINK-5-CHANGED: Interface Ethernet0/2, changed state to administratively down
*Mar 31 20:34:04.801: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
IOU4(config-if)#exit
IOU4(config)#exit
IOU4#
*Mar 31 20:34:36.762: %SYS-5-CONFIG_I: Configured from console by console
IOU4#sh spa
IOU4#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------

Et2/0               Desg FWD 100       128.9    P2p
Et2/1               Desg FWD 100       128.10   P2p
Et2/2               Desg FWD 100       128.11   P2p
Et2/3               Desg FWD 100       128.12   P2p
Et3/0               Desg FWD 100       128.13   P2p
Et3/1               Desg FWD 100       128.14   P2p
Et3/2               Desg FWD 100       128.15   P2p
Et3/3               Desg FWD 100       128.16   P2p
```

int Ethernet0/2 a disparu sur le switch4, le switch 3 n'a plus de port en mode BLK

lorsque j'ai rallumer l'interface, il est passé en learning puis en fwd. l'interface qui avait un port BLK au tout debut et repassé en BLK. Ca doit avoir un rapport avec le coup.

c'est bon j'ai compris, il faut augmenter le coup du lien 

🌞 Altérer le spanning-tree en modifiant le coût d'un lien

On va baisser le coup de l'interface qui est bloqué: ca a pas l'air de marcher, je vais l'augmenter. tjrs pas

j'ai baissé le cout de l'interface qui est activé(du switch ou l'autre interface est bloqué) et ca marché cette fois. je sais pas pourquoi ?

```
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Altn BLK 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p

IOU5#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU5(config)#int Et
IOU5(config)#int Ethernet0/1
IOU5(config-if)#spa
IOU5(config-if)#spanning-tree cost 5
IOU5(config-if)#exit
IOU5(config)#exit
IOU5#sh
*Mar 31 21:18:16.805: %SYS-5-CONFIG_I: Configured from console by console
IOU5#sh spa
IOU5#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        5
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 5         128.2    P2p
Et0/2               Desg LIS 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p

```




## II. OSPF

#### 🌞 Configurer OSPF sur tous les routeurs

R1:
```
R1# sh running
R1# sh running-config
Building configuration...

interface FastEthernet0/0
 ip address 10.6.21.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.13.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.41.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet2/0
 ip address 10.6.3.254 255.255.255.0
 duplex auto
 speed auto
!
router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 network 10.6.3.0 0.0.0.255 area 2
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.41.0 0.0.0.3 area 3
 default-information originate
!
ip forward-protocol nd
!
!

R1#sh ip ospf neigh

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:34    10.6.13.2       FastEthernet0/1
2.2.2.2           1   FULL/DR         00:00:32    10.6.21.2       FastEthernet0/0
4.4.4.4           1   FULL/BDR        00:00:35    10.6.41.2       FastEthernet1/0
R1#sh ip rout
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.21.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet0/1
O       10.6.1.0/24 [110/11] via 10.6.41.2, 00:45:55, FastEthernet1/0
O       10.6.2.0/24 [110/2] via 10.6.41.2, 00:45:55, FastEthernet1/0
C       10.6.3.0/24 is directly connected, FastEthernet2/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
O       10.6.23.0/30 [110/20] via 10.6.21.2, 00:48:14, FastEthernet0/0
                     [110/20] via 10.6.13.2, 00:48:14, FastEthernet0/1
C       10.6.41.0/30 is directly connected, FastEthernet1/0
O IA    10.6.52.0/30 [110/11] via 10.6.21.2, 00:48:16, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.21.2, 00:28:17, FastEthernet0/0
R1#


```

R2:

```

R2#sh running-config
Building configuration...

interface FastEthernet0/0
 ip address 10.6.21.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.23.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.52.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 2.2.2.2
 log-adjacency-changes
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
 network 10.6.52.0 0.0.0.3 area 1
 default-information originate
!


R2#h ip ospf neig
R2#sh ip ospf neigh

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:36    10.6.21.1       FastEthernet0/0
3.3.3.3           1   FULL/BDR        00:00:34    10.6.23.1       FastEthernet0/1
5.5.5.5           1   FULL/DR         00:00:36    10.6.52.1       FastEthernet1/0
R2#sh ip rout
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.52.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/20] via 10.6.23.1, 00:53:28, FastEthernet0/1
                     [110/20] via 10.6.21.1, 00:52:04, FastEthernet0/0
O IA    10.6.1.0/24 [110/21] via 10.6.21.1, 00:48:17, FastEthernet0/0
O IA    10.6.2.0/24 [110/12] via 10.6.21.1, 00:48:17, FastEthernet0/0
O IA    10.6.3.0/24 [110/11] via 10.6.21.1, 00:50:31, FastEthernet0/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O IA    10.6.41.0/30 [110/11] via 10.6.21.1, 00:50:12, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet1/0
O*E2 0.0.0.0/0 [110/1] via 10.6.52.1, 00:31:48, FastEthernet1/0
R2#


```

R3:

```


R3#sh running-config

!
interface FastEthernet0/0
 ip address 10.6.13.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.23.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 3.3.3.3
 log-adjacency-changes
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
!
ip forward-protocol nd

R3#sh ip ospf neigh

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:37    10.6.23.2       FastEthernet0/1
1.1.1.1           1   FULL/BDR        00:00:37    10.6.13.1       FastEthernet0/0
R3#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.23.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet0/0
O IA    10.6.1.0/24 [110/21] via 10.6.13.1, 00:50:19, FastEthernet0/0
O IA    10.6.2.0/24 [110/12] via 10.6.13.1, 00:50:19, FastEthernet0/0
O IA    10.6.3.0/24 [110/11] via 10.6.13.1, 00:52:33, FastEthernet0/0
O       10.6.21.0/30 [110/20] via 10.6.23.2, 00:55:27, FastEthernet0/1
                     [110/20] via 10.6.13.1, 00:54:06, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O IA    10.6.41.0/30 [110/11] via 10.6.13.1, 00:52:14, FastEthernet0/0
O IA    10.6.52.0/30 [110/11] via 10.6.23.2, 00:55:29, FastEthernet0/1
O*E2 0.0.0.0/0 [110/1] via 10.6.23.2, 00:33:00, FastEthernet0/1
R3#


```

R4:

```


R4#sh running-config

!
interface FastEthernet0/0
 ip address 10.6.41.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.1.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.2.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 4.4.4.4
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 3
 network 10.6.2.0 0.0.0.255 area 3
 network 10.6.41.0 0.0.0.3 area 3
 default-information originate
!
ip forward-protocol nd

R4#h ip ospf neig
R4#help ip ospf neig
        ^
% Invalid input detected at '^' marker.

R4#sh ip ospf neigh

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:38    10.6.41.1       FastEthernet0/0
R4#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.41.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O IA    10.6.13.0/30 [110/20] via 10.6.41.1, 00:53:21, FastEthernet0/0
C       10.6.1.0/24 is directly connected, FastEthernet0/1
C       10.6.2.0/24 is directly connected, FastEthernet1/0
O IA    10.6.3.0/24 [110/11] via 10.6.41.1, 00:53:21, FastEthernet0/0
O IA    10.6.21.0/30 [110/20] via 10.6.41.1, 00:53:21, FastEthernet0/0
O IA    10.6.23.0/30 [110/30] via 10.6.41.1, 00:53:21, FastEthernet0/0
C       10.6.41.0/30 is directly connected, FastEthernet0/0
O IA    10.6.52.0/30 [110/21] via 10.6.41.1, 00:53:23, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.41.1, 00:35:44, FastEthernet0/0
R4#


```

R5:

```

R5#sh running-config

!
interface FastEthernet0/0
 ip address 10.6.52.1 255.255.255.252
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 5.5.5.5
 log-adjacency-changes
 network 10.6.52.0 0.0.0.3 area 1
 default-information originate always
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
ip nat inside source list 1 interface FastEthernet0/1 overload

R5#sh ip ospf neigh

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:35    10.6.52.2       FastEthernet0/0
R5#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 192.168.122.1 to network 0.0.0.0

C    192.168.122.0/24 is directly connected, FastEthernet0/1
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O IA    10.6.13.0/30 [110/30] via 10.6.52.2, 01:00:53, FastEthernet0/0
O IA    10.6.1.0/24 [110/31] via 10.6.52.2, 00:55:42, FastEthernet0/0
O IA    10.6.2.0/24 [110/22] via 10.6.52.2, 00:55:42, FastEthernet0/0
O IA    10.6.3.0/24 [110/21] via 10.6.52.2, 00:57:56, FastEthernet0/0
O IA    10.6.21.0/30 [110/20] via 10.6.52.2, 01:02:28, FastEthernet0/0
O IA    10.6.23.0/30 [110/20] via 10.6.52.2, 01:02:37, FastEthernet0/0
O IA    10.6.41.0/30 [110/21] via 10.6.52.2, 00:57:37, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet0/0
S*   0.0.0.0/0 [254/0] via 192.168.122.1
R5#


```

🌞 Test

```

waf.tp6.b1> ping 10.6.3.11
10.6.3.11 icmp_seq=1 timeout
10.6.3.11 icmp_seq=2 timeout
84 bytes from 10.6.3.11 icmp_seq=3 ttl=62 time=34.208 ms
84 bytes from 10.6.3.11 icmp_seq=4 ttl=62 time=38.250 ms
84 bytes from 10.6.3.11 icmp_seq=5 ttl=62 time=30.757 ms

waf.tp6.b1> ping 10.6.2.11
10.6.2.11 icmp_seq=1 timeout
10.6.2.11 icmp_seq=2 timeout
84 bytes from 10.6.2.11 icmp_seq=3 ttl=63 time=16.646 ms
84 bytes from 10.6.2.11 icmp_seq=4 ttl=63 time=34.183 ms
84 bytes from 10.6.2.11 icmp_seq=5 ttl=63 time=22.895 ms

waf.tp6.b1> ping 10.6.52.1
84 bytes from 10.6.52.1 icmp_seq=1 ttl=252 time=76.951 ms
84 bytes from 10.6.52.1 icmp_seq=2 ttl=252 time=47.581 ms
84 bytes from 10.6.52.1 icmp_seq=3 ttl=252 time=49.459 ms
84 bytes from 10.6.52.1 icmp_seq=4 ttl=252 time=48.617 ms
84 bytes from 10.6.52.1 icmp_seq=5 ttl=252 time=63.771 ms

waf.tp6.b1> ping 1.1.1.1
1.1.1.1 icmp_seq=1 timeout
1.1.1.1 icmp_seq=2 timeout
1.1.1.1 icmp_seq=3 timeout
1.1.1.1 icmp_seq=4 timeout
1.1.1.1 icmp_seq=5 timeout

```

tout le monde peut ping tout le monde, par contre les clients n'ont pas internet. 

update: pas de soucis au final, c'est les VPCs qui bug, la VM dans ma topo peut ping internet


🦈 tp6_ospf.pcapng

[text](tp6_ospf2.pcapng)

j'ai fais une capture entre R1 et R4 et je vois des trames ospf ACK et update qui me montre bien que l'ospf verifie et se met a jour, puis ils s'envoient des hello en continu


## III. DHCP relay


➜ DHCP Relay !



🌞 Configurer un serveur DHCP sur dhcp.tp6.b1

j'arrive tjrs pas a ssh ma VM
![alt text](<capture dhcpd.PNG>)



🌞 Configurer un DHCP relay sur la passerelle de John

```
waf.tp6.b1> ip dhcp
DDORA IP 10.6.1.100/24 GW 10.6.1.254

```

```

john.tp6.b1> ip dhcp
DDORA IP 10.6.3.100/24 GW 10.6.3.254

```

R1 running conf:

```


!
interface FastEthernet2/0
 ip address 10.6.3.254 255.255.255.0
 ip helper-address 10.6.1.253
 duplex auto
 speed auto
!
```


### IV. Bonus

#### 1. ACL

🌞 Configurer une access-list

 R4:

 ```
 R4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R4(config)#acces-li
R4(config)#access-list 101 permit udp any any eq bootpc
R4(config)#access-list 101 permit udp any any eq bootps
R4(config)#access-list 101 permit icmp any any echo-reply
R4(config)#access-list 101 permit icmp any any echo
R4(config)#access-list 101 permit tcp any any eq www
R4(config)#access-list 101 deny ip any any
R4(config)#int fas
R4(config)#int fastEthernet 0/1
R4(config-if)#ip acc
R4(config-if)#ip access-gr
R4(config-if)#ip access-group 101 in
R4(config-if)#int fastEthernet 1/0
R4(config-if)#ip access-group 101 in

 ```

R1:

```

R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int fas
R1(config)#int fastEthernet 2/0
R1(config-if)#acc
R1(config-if)#access-list 101 permit udp any host 10.6.1.253 eq bootps
R1(config)#access-list 101 permit icmp any any echo-reply
R1(config)#access-list 101 permit icmp any any echo
R1(config)#access-list 101 permit tcp any any eq www
R1(config)#access-list 101 deny ip any any
R1(config)#access-list 101 permit udp any host 10.6.1.253 eq bootps
R1(config)#int fastEthernet 2/0
R1(config-if)#ip access-group 101 in
R1(config-if)#exit
R1(config)#exit

```


