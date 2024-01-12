TP4 : Router-on-a-stick


I. Topo 1 : VLAN et Routing

3. Setup topologie 1



🖥️ VM web1.servers.tp4, déroulez la Checklist VM Linux dessus

🌞 Adressage

```
PC1> ip 10.1.10.1/24
```

```
PC2> ip 10.1.10.2/24
```

```
PC3> ip 10.1.20.1/24
```
pour le serveur on utilise

```
ip a
```
```
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3


NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.1.30.1
NETMASK=255.255.255.0
```
```
sudo nmcli con reload
sudo nmcli con up enp0s3

```

🌞 Configuration des VLANs

Declaration

```
(config)# vlan 10
(config-vlan)# name clients
(config-vlan)# exit

(config)# vlan 20
(config-vlan)# name admins
(config-vlan)# exit

(config)# vlan 30
(config-vlan)# name servers
```

Attribution

```
# conf t
(config)# interface Ethernet0/0
(config-if)# switchport mode access
(config-if)# switchport access vlan 10
(config-if)# exit
```

Resultat

```
sw1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   clients                          active    Et0/0, Et0/1
20   admins                           active    Et0/2
30   servers                          active    Et0/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

```
switch-routeur mode trunk

```
sw1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.

sw1(config-if)#switchport trunk encapsulation dot1q
sw1(config-if)#switchport mode trunk
sw1(config-if)#
*Nov 24 13:17:48.143: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/0, changed state to down
sw1(config-if)#
*Nov 24 13:17:51.167: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/0, changed state to up
sw1(config-if)#switchport trunk allowed vlan add 10,20,30
```

➜ Pour le routeur

🌞 Config du routeur

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int fastEthernet0/0.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip addr 10.1.10.254 255.255.255.0
R1(config-subif)#exit
R1(config)#int fastEthernet0/0.20
R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip addr 10.1.20.254 255.255.255.0
R1(config-subif)#exit
R1(config)#int fastEthernet0/0.30
R1(config-subif)#encapsulation dot1q 30
R1(config-subif)#ip addr 10.1.30.254 255.255.255.0
R1(config-subif)#exit

```
🌞 Vérif

tout le monde doit pouvoir ping le routeur sur l'IP qui est dans son réseau

```
PC1> ping 10.1.10.254/24

10.1.10.254 icmp_seq=1 timeout
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=9.727 ms
84 bytes from 10.1.10.254 icmp_seq=3 ttl=255 time=2.715 ms
84 bytes from 10.1.10.254 icmp_seq=4 ttl=255 time=5.105 ms
84 bytes from 10.1.10.254 icmp_seq=5 ttl=255 time=2.004 ms

PC1> ping 10.1.20.254

No gateway found

```
en ajoutant une route vers les réseaux, ils peuvent se ping entre eux

* ajoutez une route par défaut sur les VPCS
```
PC1> ip 10.1.10.1/24 10.1.10.254
Checking for duplicate address...
PC1 : 10.1.10.1 255.255.255.0 gateway 10.1.10.254

```
* ajoutez une route par défaut sur la machine virtuelle
```
sudo ip route add default via 10.1.30.0 dev enp0s3
ip route show
```



* testez des ping entre les réseaux

De VPC3 a VPC1 + server Rocky
```
VPCS> ping 10.1.10.1

10.1.10.1 icmp_seq=1 timeout
84 bytes from 10.1.10.1 icmp_seq=2 ttl=63 time=37.366 ms
84 bytes from 10.1.10.1 icmp_seq=3 ttl=63 time=21.770 ms
84 bytes from 10.1.10.1 icmp_seq=4 ttl=63 time=20.769 ms
84 bytes from 10.1.10.1 icmp_seq=5 ttl=63 time=14.244 ms


VPCS> ping 10.1.30.1

10.1.30.1 icmp_seq=1 timeout
10.1.30.1 icmp_seq=2 timeout
10.1.30.1 icmp_seq=3 timeout
10.1.30.1 icmp_seq=4 timeout
10.1.30.1 icmp_seq=5 timeout
```
De VPC2 a server Rocky + VPC3
```
VPCS> ping 10.1.30.1

10.1.30.1 icmp_seq=1 timeout
10.1.30.1 icmp_seq=2 timeout
10.1.30.1 icmp_seq=3 timeout
10.1.30.1 icmp_seq=4 timeout
10.1.30.1 icmp_seq=5 timeout

```

```
VPCS> ping 10.1.20.1

84 bytes from 10.1.20.1 icmp_seq=1 ttl=63 time=33.154 ms
84 bytes from 10.1.20.1 icmp_seq=2 ttl=63 time=19.867 ms
84 bytes from 10.1.20.1 icmp_seq=3 ttl=63 time=18.934 ms
84 bytes from 10.1.20.1 icmp_seq=4 ttl=63 time=16.321 ms
84 bytes from 10.1.20.1 icmp_seq=5 ttl=63 time=21.030 ms

```
De VPC1 au routeur

```
VPCS> ping 10.1.30.254

84 bytes from 10.1.30.254 icmp_seq=1 ttl=255 time=26.936 ms
84 bytes from 10.1.30.254 icmp_seq=2 ttl=255 time=11.918 ms
84 bytes from 10.1.30.254 icmp_seq=3 ttl=255 time=2.842 ms
84 bytes from 10.1.30.254 icmp_seq=4 ttl=255 time=2.837 ms
84 bytes from 10.1.30.254 icmp_seq=5 ttl=255 time=2.509 ms

VPCS> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=5.312 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=7.680 ms
84 bytes from 10.1.10.254 icmp_seq=3 ttl=255 time=5.387 ms
84 bytes from 10.1.10.254 icmp_seq=4 ttl=255 time=5.158 ms
84 bytes from 10.1.10.254 icmp_seq=5 ttl=255 time=3.270 ms

VPCS> ping 10.1.20.254

84 bytes from 10.1.20.254 icmp_seq=1 ttl=255 time=4.965 ms
84 bytes from 10.1.20.254 icmp_seq=2 ttl=255 time=9.242 ms
84 bytes from 10.1.20.254 icmp_seq=3 ttl=255 time=3.831 ms
84 bytes from 10.1.20.254 icmp_seq=4 ttl=255 time=2.976 ms
84 bytes from 10.1.20.254 icmp_seq=5 ttl=255 time=2.819 ms

```
# II. NAT

### 🌞 Ajoutez le noeud Cloud à la topo


* côté routeur, il faudra récupérer un IP en DHCP (voir le mémo Cisco)

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int FastEthernet0/1
R1(config-if)#ip address dhcp
R1(config-if)#no shut

```
```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES NVRAM  up                    up  
FastEthernet0/0.10         10.1.10.254     YES NVRAM  up                    up  
FastEthernet0/0.20         10.1.20.254     YES NVRAM  up                    up  
FastEthernet0/0.30         10.1.30.254     YES NVRAM  up                    up  
FastEthernet0/1            10.0.3.16       YES DHCP   up                    up  

```

* vous devriez pouvoir ping 1.1.1.1

```
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 56/116/212 ms

```

### 🌞 Configurez le NAT

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int fastEthernet 0/0.10
R1(config-subif)#ip nat inside

*Mar  1 00:02:45.143: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, chan
R1(config-subif)#
R1(config-subif)#exit

R1(config)#int fastEthernet 0/0.20
R1(config-subif)#ip nat inside
R1(config-subif)#exit

R1(config)#int fastEthernet 0/0.30
R1(config-subif)#ip nat inside
R1(config-subif)#exit

R1(config)#int fastEthernet 0/1
R1(config-if)#ip nat outside
R1(config-if)#exit

R1(config)#access-list 1 permit any

R1(config)#ip nat inside source list 1 interface fastEthernet0/1 overload

R1(config)#int fastEthernet0/0
R1(config-if)#no shut
R1(config-if)#exit
```

### 🌞 Test

* configurez l'utilisation d'un DNS

sur les VPCS

```
VPCS> ip dns 1.1.1.1
```

sur la machine Linux

```
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3





NAME=hicha
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.1.30.1
NETMASK=255.255.255.0

# La suite est optionnelle
GATEWAY=10.1.30.254
DNS1=1.1.1.1

sudo nmcli con reload
sudo nmcli con up enp0s3
```


* vérifiez un ping vers un nom de domaine

```
VPCS> ping google.com
google.com resolved to 142.250.75.238

google.com icmp_seq=1 timeout
```
# 3. Setup topologie 3

🌞  Vous devez me rendre le show running-config de tous les équipements



le routeur(raccourci: suite de "!"= "...")

```
R1# show running-config
Building configuration...

Current configuration : 1560 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
...
!
no ip domain lookup
!
multilink bundle-name authenticated
!
...
!
archive
 log config
  hidekeys
!
...
!
ip tcp synwait-time 5
!
...
!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
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
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any
no cdp log mismatch duplex
!
...
!
control-plane
!
...
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end


```
les 3 switches

switch 1 (principal)(raccourci: suite de "!"= "...")

```
sw1#show running-conf
Building configuration...

Current configuration : 1607 bytes
!
! Last configuration change at 12:40:48 UTC Fri Dec 8 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw1
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
...
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
...
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
!
interface Ethernet1/0
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end

```

switch 2(raccourci: suite de "!"= "...")

```
sw2#show running-conf
Building configuration...

Current configuration : 1689 bytes
!
! Last configuration change at 12:40:48 UTC Fri Dec 8 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw2
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
...
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 30
 switchport mode access
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end

```

switch 3: (pas raccourci pour l'exemple)

```
sw3#sh running-config
Building configuration...

Current configuration : 1689 bytes
!
! Last configuration change at 13:58:00 UTC Fri Dec 8 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw3
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end

```



N'oubliez pas les VLANs sur tous les switches.


🌞  Mettre en place un serveur DHCP dans le nouveau bâtiment

il doit distribuer des IPs aux clients dans le réseau clients qui sont branchés au même switch que lui
sans aucune action manuelle, les clients doivent...

avoir une IP dans le réseau clients(Ok)
avoir un accès au réseau servers(Ok)
avoir un accès WAN(OK)
avoir de la résolution DNS(Ok)




Réutiliser un serveur DHCP qu'on a monté dans un autre TP si vous avez.

🌞  Vérification

```
VPCS> ip dhcp
DORA IP 10.1.10.102/24 GW 10.1.10.254

VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 10.1.10.102/24
GATEWAY     : 10.1.10.254
DNS         : 1.1.1.1
DHCP SERVER : 10.1.10.253
DHCP LEASE  : 596, 600/300/525
DOMAIN NAME : srv.world
MAC         : 00:50:79:66:68:03
LPORT       : 20032
RHOST:PORT  : 127.0.0.1:20033
MTU         : 1500

VPCS> ping 10.1.30.1

10.1.30.1 icmp_seq=1 timeout
10.1.30.1 icmp_seq=2 timeout
10.1.30.1 icmp_seq=3 timeout
10.1.30.1 icmp_seq=4 timeout
10.1.30.1 icmp_seq=5 timeout

VPCS> ping 1.1.1.1

1.1.1.1 icmp_seq=1 timeout
1.1.1.1 icmp_seq=2 timeout
1.1.1.1 icmp_seq=3 timeout
1.1.1.1 icmp_seq=4 timeout
1.1.1.1 icmp_seq=5 timeout

VPCS> ping ynov.com
ynov.com resolved to 104.26.10.233

ynov.com icmp_seq=1 timeout
ynov.com icmp_seq=2 timeout
ynov.com icmp_seq=3 timeout
ynov.com icmp_seq=4 timeout
ynov.com icmp_seq=5 timeout

VPCS>

```
