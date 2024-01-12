# TP1 : Maîtrise réseau du poste
## I. Basics

#### ☀️ Carte réseau WiFi

Cmd : ip config /all
```
Physical Address. . . . . . . . . : 18-47-3D-80-AE-A4
IPv4 Address. . . . . . . . . . . : 10.33.66.167(Preferred)
Subnet Mask . . . . . . . . . . . : 255.255.240.0
Notation CIDR : 10.33.66.167/20
``` 
    

#### ☀️ Déso pas déso

    Adresse réseau LAN : 10.33.66.0

    Adresse de broadcast : 10.33.79.255

    Nombre d'IP disponibles : 4096 = (79-66+1)*256 ?

#### ☀️ Hostname

    Cmd :  hostname
        
        LAPTOP-ASH

#### ☀️ Passerelle du réseau

```
ipconfig /all
Default Gateway . . . . . . . . . : 10.33.79.254
```

    cmd : arp -a
    10.33.79.254          7c-5a-1c-d3-d8-76     dynamic

#### ☀️ Serveur DHCP et DNS

    DHCP Server . . . . . . . . . . . : 10.33.79.254
    DNS Servers . . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
                                       
☀️ Table de routage

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0     10.33.79.254     10.33.66.167     40
ca doit etre ca

**II. Go further**

☀️ Hosts ?

Path : "C:\Windows\System32\drivers\etc\hosts"

```
PS C:\Users\belma> cat C:\Windows\System32\drivers\etc\hosts
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#       127.0.0.1       localhost
#       ::1             localhost
        1.1.1.1         b2.hello.vous
       
```
Ping 

```PS C:\Users\belma> ping b2.hello.vous

Pinging b2.hello.vous [1.1.1.1] with 32 bytes of data:
Reply from 1.1.1.1: bytes=32 time=57ms TTL=57
Reply from 1.1.1.1: bytes=32 time=19ms TTL=57
```

☀️ Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...

```
PS C:\Users\belma> netstat -anb | select-string 91.68.245.78 -Context 0,1

>   UDP    0.0.0.0:58297          91.68.245.78:443
   [brave.exe]
```
☀️ Requêtes DNS

* à quelle adresse IP correspond le nom de domaine www.ynov.com
```
PS C:\Users\belma> nslookup www.ynov.com
Server:  dns.google
Address:  8.8.8.8

Non-authoritative answer:
Name:    www.ynov.com
Addresses:  2606:4700:20::ac43:4ae2
          2606:4700:20::681a:ae9
          2606:4700:20::681a:be9
          104.26.11.233
          172.67.74.226
          104.26.10.233
```
* à quel nom de domaine correspond l'IP 174.43.238.89

```
PS C:\Users\belma> nslookup 174.43.238.89
Server:  dns.google
Address:  8.8.8.8

Name:    89.sub-174-43-238.myvzw.com
Address:  174.43.238.89
```
☀️ Hop hop hop

par combien de machines vos paquets passent quand vous essayez de joindre www.ynov.com

```
PS C:\Users\belma> tracert  104.26.11.233

Tracing route to 104.26.11.233 over a maximum of 30 hops

  1    13 ms     2 ms     2 ms  10.33.79.254
  2     5 ms     4 ms    87 ms  145.117.7.195.rev.sfr.net [195.7.117.145]
  3    14 ms     5 ms     3 ms  237.195.79.86.rev.sfr.net [86.79.195.237]
  4   129 ms     6 ms     5 ms  196.224.65.86.rev.sfr.net [86.65.224.196]
  5   203 ms    12 ms    87 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  6    94 ms    11 ms    86 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  7    20 ms    74 ms    11 ms  141.101.67.48
  8   117 ms    97 ms    14 ms  172.71.124.4
  9    17 ms    91 ms    13 ms  104.26.11.233

Trace complete.
```

☀️ IP publique

l'adresse IP publique de la passerelle du réseau :-1: 

```
PS C:\Users\belma> curl ifconfig.me/ip                                                                                  

StatusCode        : 200
StatusDescription : OK
Content           : 195.7.117.146
RawContent        : HTTP/1.1 200 OK
                    access-control-allow-origin: *
                    x-envoy-upstream-service-time: 0
                    strict-transport-security: max-age=2592000; includeSubDomains
                    Content-Length: 13
                    Content-Type: text/plain
                    Date: Th...
Forms             : {}
Headers           : {[access-control-allow-origin, *], [x-envoy-upstream-service-time, 0], [strict-transport-security,
                    max-age=2592000; includeSubDomains], [Content-Length, 13]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 13
```
☀️ Scan réseau

combien il y a de machines dans le LAN auquel vous êtes connectés:

```
PS C:\Users\belma> nmap -sP 10.33.66.0/20
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-26 16:52 Romance Summer Time
```
                            ...
```
Nmap scan report for 10.33.66.167
Host is up.
Nmap done: 4096 IP addresses (643 hosts up) scanned in 180.02 seconds
```
☀️ Capture ARP


📁 fichier arp.pcap

capturez un échange ARP entre votre PC et la passerelle du réseau           
                            
