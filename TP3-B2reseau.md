
3. Setup topologie 1
🌞 Commençons simple

définissez les IPs statiques sur les deux VPCS

changer IP pour PC1 : 
```
PC1> ip 10.3.1.1/24
Checking for duplicate address...
PC1 : 10.3.1.1 255.255.255.0
```

changer IP pour PC2 :
```
PC1> ip 10.3.1.2/24
checking for duplicate address...
PC1 : 10.3.1.2 255.255.255.0
```

Verifier pour PC1:
```
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:02
LPORT       : 20006
RHOST:PORT  : 127.0.0.1:20007
MTU         : 1500

```

ping un VPCS depuis l'autre

```
PC1> ping 10.3.1.2/24

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.004 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.596 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=0.522 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=0.560 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=0.486 ms
```


afficher la CAM table du switch et vérifier les MAC qui s'y trouvent (Google pour ça, c'est pas dans le mémo)

```
IOU1#sh mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6802    DYNAMIC     Et0/0
   1    0050.7966.6803    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
```





II. VLAN


déclaration du VLAN sur tous les switches











1. Topologie 2


🌞 Adressage

définissez les IPs statiques sur tous les VPCS
```
PC3> ip 10.3.1.3/24
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0
```


🌞 Configuration des VLANs



creer vlan 10 + lier VPC1+2:


```
IOU1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#vlan 10
IOU1(config-vlan)#name guests
IOU1(config-vlan)#exit
IOU1(config)#interface Etherne0/0
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit
IOU1(config)#interface Etherne0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit

IOU1(config)#^Z
IOU1#
*Nov  6 17:08:33.243: %SYS-5-CONFIG_I: Configured from console by console
```

creer vlan 20 + lier VPC3:

```
IOU1(config)#vlan 20
IOU1(config-vlan)#name admins
IOU1(config-vlan)#exit
IOU1(config)#interface Etherne0/2
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
IOU1(config-if)#exit
```




🌞 Vérif Ping

```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.482 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.564 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=0.820 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=0.905 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=0.713 ms

```

```
PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.620 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=0.869 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=64 time=0.506 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=64 time=1.615 ms

```

```
PC1> ping 10.3.1.3/24

host (10.3.1.3) not reachable
```



III. Ptite VM DHCP
On va ajouter une VM dans la topologie, histoire que vous voyiez cet aspect de GNS.




🌞 VM dhcp.tp3.b2

Rocky Linux, IP statique, nom défini à dhcp.tp3.b2, SELinux désactivé, firewall activé, système à jour, NORMAL LA ROUTINE QUOI
installez un serveur DHCP

il doit délivrer des IPs entre 10.3.1.100 et 10.3.1.200



vérifier avec le pc4 que vous pouvez récupérer une IP en DHCP
```
PC5>
PC5> sh ip  IP 10.3.1.100/24

NAME        : PC5[1]
IP/MASK     : 10.3.1.100/24
GATEWAY     : 0.0.0.0
DNS         :
DHCP SERVER : 10.3.1.253
DHCP LEASE  : 599, 600/300/525
DOMAIN NAME : srv.world
MAC         : 00:50:79:66:68:04
LPORT       : 20019
RHOST:PORT  : 127.0.0.1:20020
MTU         : 1500

```
vérifier avec le pc5 que vous ne pouvez PAS récupérer une IP en DHCP

```
PC4> ip dhcp
DDD
Can't find dhcp server

```








































