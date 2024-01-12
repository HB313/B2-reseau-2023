# TP6 INFRA : STP, OSPF, bigger infra

## I. STP

### 1. Basic setup
On va setup STP, au sein d'une topo simple pour que vous le voyiez en action.

#### 🌞 Configurer STP sur les 3 switches

```
IOU3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
 --More--

```



🌞 Altérer le spanning-tree en désactivant un port

désactiver juste un port de un switch pour provoquer la mise à jour de STP

```
IOU2#conf t
IOU2(config)#int Ethernet0/1
IOU2(config-if)#shutdown
IOU2(config-if)#
*Dec  9 12:35:59.083: %LINK-5-CHANGED: Interface Ethernet0/1, changed state to administratively down
*Dec  9 12:36:00.089: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down
IOU2(config-if)#
IOU2#
*Dec  9 12:36:03.970: %SYS-5-CONFIG_I: Configured from console by console
IOU2#sh span
IOU2#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p



```
l'interface Eth0/1 a disparu

show spanning-tree pour voir la diff

le port auparavant bloqué est d'abord passé en "LRN"

Puis automatiquement est passé en "FWD"

```
IOU3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/1               Desg LRN 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p

IOU3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/1               Desg FWD 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
 --More--

```


###### Ré-activer ce port après la manip inutile de le laisser désactivé.

##### 🌞 Altérer le spanning-tree en modifiant le coût d'un lien

modifier le coût d'un lien existant pour modifier l'arbre spanning-tree

```
IOU3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU3(config)#int ethernet0/1
IOU3(config-if)#spanning-tree cost 2
IOU3(config-if)#exit
IOU3(config)#
IOU3#
*Dec 14 13:54:32.502: %SYS-5-CONFIG_I: Configured from console by console
IOU3#sh span
IOU3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0400
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0600
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/1               Altn BLK 2          16.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p

IOU3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU3(config)#int ethernet0/0
IOU3(config-if)#spanning-tree cost 103
IOU3(config-if)#exit
IOU3(config)#exit
IOU3#
*Dec 14 14:01:37.043: %SYS-5-CONFIG_I: Configured from console by console
IOU3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0400
             Cost        102
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0600
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 103       128.1    P2p
Et0/1               Root LRN 2          16.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p

IOU3#sh spanning-tree
```






## II. OSPF






### 🌞 Configurer OSPF sur tous les routeurs

#### R1:

###### show running conf :
```
R1# show running-config
Building configuration...


hostname R1

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

end

R1#

```
###### show ospf + ip:
```
R1#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:36    10.6.21.2       FastEthernet0/0
3.3.3.3           1   FULL/DR         00:00:39    10.6.13.2       FastEthernet0/1
4.4.4.4           1   FULL/DR         00:00:38    10.6.41.2       FastEthernet1/0
R1#show ip route
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
O       10.6.1.0/24 [110/11] via 10.6.41.2, 00:19:18, FastEthernet1/0
O       10.6.2.0/24 [110/2] via 10.6.41.2, 00:19:18, FastEthernet1/0
C       10.6.3.0/24 is directly connected, FastEthernet2/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
O       10.6.23.0/30 [110/11] via 10.6.21.2, 00:19:18, FastEthernet0/0
C       10.6.41.0/30 is directly connected, FastEthernet1/0
O IA    10.6.52.0/30 [110/20] via 10.6.21.2, 00:19:22, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.21.2, 00:18:49, FastEthernet0/0

```
#### R2:
###### show running conf :
```
R2#show running-config
Building configuration...


!
hostname R2
!
!
interface FastEthernet0/0
 ip address 10.6.52.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.21.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.23.2 255.255.255.252
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
!

!
end

```
###### show ospf + ip:
```
R2#sh ospf neighbor
       ^
% Invalid input detected at '^' marker.

R2#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:39    10.6.23.1       FastEthernet1/0
1.1.1.1           1   FULL/BDR        00:00:33    10.6.21.1       FastEthernet0/1
5.5.5.5           1   FULL/BDR        00:00:37    10.6.52.1       FastEthernet0/0
R2#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.52.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/11] via 10.6.23.1, 00:21:49, FastEthernet1/0
O IA    10.6.1.0/24 [110/21] via 10.6.21.1, 00:21:27, FastEthernet0/1
O IA    10.6.2.0/24 [110/12] via 10.6.21.1, 00:21:27, FastEthernet0/1
O IA    10.6.3.0/24 [110/11] via 10.6.21.1, 00:21:27, FastEthernet0/1
C       10.6.21.0/30 is directly connected, FastEthernet0/1
C       10.6.23.0/30 is directly connected, FastEthernet1/0
O IA    10.6.41.0/30 [110/11] via 10.6.21.1, 00:21:31, FastEthernet0/1
C       10.6.52.0/30 is directly connected, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.52.1, 00:21:00, FastEthernet0/0

```

#### R3:
###### show running conf :
```

R3#show running-config
Building configuration...


!
hostname R3
!

!
interface FastEthernet0/0
 ip address 10.6.23.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.13.2 255.255.255.252
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

!
end

```
###### show ospf + ip:
```
R3#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:31    10.6.23.2       FastEthernet0/0
1.1.1.1           1   FULL/BDR        00:00:37    10.6.13.1       FastEthernet0/1
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
C       10.6.13.0/30 is directly connected, FastEthernet0/1
O IA    10.6.1.0/24 [110/21] via 10.6.13.1, 00:22:13, FastEthernet0/1
O IA    10.6.2.0/24 [110/12] via 10.6.13.1, 00:22:13, FastEthernet0/1
O IA    10.6.3.0/24 [110/11] via 10.6.13.1, 00:22:13, FastEthernet0/1
O       10.6.21.0/30 [110/20] via 10.6.23.2, 00:22:45, FastEthernet0/0
                     [110/20] via 10.6.13.1, 00:22:13, FastEthernet0/1
C       10.6.23.0/30 is directly connected, FastEthernet0/0
O IA    10.6.41.0/30 [110/11] via 10.6.13.1, 00:22:17, FastEthernet0/1
O IA    10.6.52.0/30 [110/20] via 10.6.23.2, 00:22:49, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.23.2, 00:21:50, FastEthernet0/0

```

#### R4:
###### show running conf :

```
R4#sh running-config
Building configuration...


hostname R4

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
!
!
end

```
###### show ospf + ip:
```
R4#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:36    10.6.41.1       FastEthernet0/0
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
O IA    10.6.13.0/30 [110/20] via 10.6.41.1, 00:23:05, FastEthernet0/0
C       10.6.1.0/24 is directly connected, FastEthernet0/1
C       10.6.2.0/24 is directly connected, FastEthernet1/0
O IA    10.6.3.0/24 [110/11] via 10.6.41.1, 00:23:05, FastEthernet0/0
O IA    10.6.21.0/30 [110/20] via 10.6.41.1, 00:23:05, FastEthernet0/0
O IA    10.6.23.0/30 [110/21] via 10.6.41.1, 00:23:03, FastEthernet0/0
C       10.6.41.0/30 is directly connected, FastEthernet0/0
O IA    10.6.52.0/30 [110/30] via 10.6.41.1, 00:23:07, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.41.1, 00:22:30, FastEthernet0/0

```

#### R5:
###### show running conf :

```
R5#sh running-config
Building configuration...

!
hostname R5
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
!
!
end

```
###### show ospf + ip:
```
R5#sh ip ospf neighb
R5#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:38    10.6.52.2       FastEthernet0/0
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
O IA    10.6.13.0/30 [110/21] via 10.6.52.2, 00:23:25, FastEthernet0/0
O IA    10.6.1.0/24 [110/31] via 10.6.52.2, 00:23:25, FastEthernet0/0
O IA    10.6.2.0/24 [110/22] via 10.6.52.2, 00:23:25, FastEthernet0/0
O IA    10.6.3.0/24 [110/21] via 10.6.52.2, 00:23:25, FastEthernet0/0
O IA    10.6.21.0/30 [110/20] via 10.6.52.2, 00:23:25, FastEthernet0/0
O IA    10.6.23.0/30 [110/11] via 10.6.52.2, 00:23:28, FastEthernet0/0
O IA    10.6.41.0/30 [110/21] via 10.6.52.2, 00:23:28, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet0/0
S*   0.0.0.0/0 [254/0] via 192.168.122.1

```


Référez-vous au mémo Cisco pour les commandes OSPF.

### 🌞 Test

ping john:

```
john.tp6.b1> ping 1.1.1.1

1.1.1.1 icmp_seq=1 timeout
1.1.1.1 icmp_seq=2 timeout
1.1.1.1 icmp_seq=3 timeout
1.1.1.1 icmp_seq=4 timeout
1.1.1.1 icmp_seq=5 timeout

john.tp6.b1> ping 10.6.1.11

84 bytes from 10.6.1.11 icmp_seq=1 ttl=62 time=99.325 ms
84 bytes from 10.6.1.11 icmp_seq=2 ttl=62 time=35.268 ms
84 bytes from 10.6.1.11 icmp_seq=3 ttl=62 time=47.583 ms
84 bytes from 10.6.1.11 icmp_seq=4 ttl=62 time=45.554 ms
84 bytes from 10.6.1.11 icmp_seq=5 ttl=62 time=38.580 ms

```
ping meo:

```

meo.tp6.b1> ping 8.8.8.8

8.8.8.8 icmp_seq=1 timeout
8.8.8.8 icmp_seq=2 timeout
8.8.8.8 icmp_seq=3 timeout
8.8.8.8 icmp_seq=4 timeout
8.8.8.8 icmp_seq=5 timeout

meo.tp6.b1> ping 10.6.3.11

84 bytes from 10.6.3.11 icmp_seq=1 ttl=62 time=60.578 ms
84 bytes from 10.6.3.11 icmp_seq=2 ttl=62 time=42.268 ms
84 bytes from 10.6.3.11 icmp_seq=3 ttl=62 time=38.626 ms
84 bytes from 10.6.3.11 icmp_seq=4 ttl=62 time=35.946 ms
84 bytes from 10.6.3.11 icmp_seq=5 ttl=62 time=38.302 ms

```
🦈 tp6_ospf.pcapng

je sais pas trop utiliser wireshark pour enregistrer, mais on peut constater une suite de "hello packet" a intervelle regulier


## III. DHCP relay


### 🌞 Configurer un serveur DHCP sur dhcp.tp6.b1

même setup que d'habitude (c'est ce lien que tu cherches ?)

votre serveur DHCP donne des IPs dans les réseaux


10.6.1.0/24

de 10.6.1.100 à 10.6.1.200

informe les clients de l'adresse de la passerelle de ce réseau
informe les clients de l'adresse d'un serveur DNS : 1.1.1.1




10.6.3.0/24

de 10.6.3.100 à 10.6.3.200

informe les clients de l'adresse de la passerelle de ce réseau
informe les clients de l'adresse d'un serveur DNS : 1.1.1.1





pour le compte-rendu ça me suffit :

sudo cat /etc/dhcp/dhcpd.conf
systemctl status dhcpd



🌞 Configurer un DHCP relay sur la passerelle de John

vérifier que Waf et John peuvent récupérer une IP en DHCP
check les logs du serveur DHCP pour voir les DORA

je veux ces 4 lignes de logs dans le compte-rendu
pour John et pour Waf


la conf sur le routeur qui est la passerelle de John c'est :


R1#conf t
R1(config)#interface fastEthernet 2/0 # interface qui va recevoir des requêtes DHCP
R1(config-if)#ip helper-address <DHCP_SERVER_IP_ADDRESS>



Ui c'est tout. Bah... quoi de plus ? Il a juste besoin de savoir à qui faire passer les requêtes !


IV. Bonus

1. ACL
C'est un peu moche que les clients puissent ping les IPs des routeurs de l'autre côté de l'infra.
Normalement, il peut joindre sa passerelle, internet, épuicétou.
🌞 Configurer une access-list

ça se fait sur les routeurs
le but :

les clients peuvent ping leur passerelle
et internet
épuicétou