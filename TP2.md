# TP2 : Environnement virtuel

## 0. Prérequis

## I. Topologie réseau

```
vboxnet0 : 192.168.56.1
vboxnet1 : 192.168.57.1
NAT pour routeur : 10.0.2.0
range dhcp : 192.168.56.100 <--> 192.168.56.200
```

### ☀️ Sur node1.lan1.tp2

```hb313@chaussette:~$ sudo ssh user@192.168.56.11
user@192.168.56.11's password: 
Last login: Tue Feb 27 23:16:48 2024 from 192.168.56.1
[user@node1 ~]$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:db:4b:ac brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.11/24 brd 192.168.56.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fedb:4bac/64 scope link 
       valid_lft forever preferred_lft forever
[user@node1 ~]$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:db:4b:ac brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.11/24 brd 192.168.56.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fedb:4bac/64 scope link 
       valid_lft forever preferred_lft forever
[user@node1 ~]$ ip route show
192.168.56.0/24 dev enp0s3 proto kernel scope link src 192.168.56.11 metric 100 
192.168.57.0/24 via 192.168.56.254 dev enp0s3 proto static metric 100 
[user@node1 ~]$ ping node2.lan2.tp2
PING node2.lan2.tp2 (192.168.57.12) 56(84) bytes of data.
64 bytes from node2.lan2.tp2 (192.168.57.12): icmp_seq=1 ttl=63 time=1.64 ms
64 bytes from node2.lan2.tp2 (192.168.57.12): icmp_seq=2 ttl=63 time=1.07 ms
64 bytes from node2.lan2.tp2 (192.168.57.12): icmp_seq=3 ttl=63 time=1.06 ms
64 bytes from node2.lan2.tp2 (192.168.57.12): icmp_seq=4 ttl=63 time=1.28 ms
64 bytes from node2.lan2.tp2 (192.168.57.12): icmp_seq=5 ttl=63 time=1.03 ms
^C
--- node2.lan2.tp2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 1.032/1.217/1.643/0.230 ms
[user@node1 ~]$ traceroute node2.lan2.tp2
traceroute to node2.lan2.tp2 (192.168.57.12), 30 hops max, 60 byte packets
 1  router.tp2 (192.168.56.254)  2.731 ms  2.651 ms  2.400 ms
 2  node2.lan2.tp2 (192.168.57.12)  2.262 ms !X  2.117 ms !X  1.910 ms !X
[user@node1 ~]$ 
```

### ☀️ Sur router.tp2

prouvez que vous avez un accès internet (ping d'une IP publique).

prouvez que vous pouvez résoudre des noms publics (ping d'un nom de domaine public)

```
hb313@chaussette:~$ sudo ssh user@192.168.56.254
[sudo] password for hb313: 
user@192.168.56.254's password: 
Last login: Wed Feb 28 19:52:35 2024
[user@router ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:f4:92:2f brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.254/24 brd 192.168.56.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fef4:922f/64 scope link 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:92:fb:a7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.57.254/24 brd 192.168.57.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe92:fba7/64 scope link 
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:67:da:14 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.4/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s9
       valid_lft 451sec preferred_lft 451sec
    inet6 fe80::1161:8939:f7ad:8226/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
[user@router ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=51 time=637 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=51 time=660 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=51 time=478 ms
^C
--- 1.1.1.1 ping statistics ---
4 packets transmitted, 3 received, 25% packet loss, time 3026ms
rtt min/avg/max/mdev = 478.421/591.510/659.572/80.517 ms
[user@router ~]$ ping www.cloudflare.com
PING www.cloudflare.com (104.16.123.96) 56(84) bytes of data.
64 bytes from 104.16.123.96 (104.16.123.96): icmp_seq=1 ttl=51 time=213 ms
64 bytes from 104.16.123.96 (104.16.123.96): icmp_seq=2 ttl=51 time=295 ms
64 bytes from 104.16.123.96 (104.16.123.96): icmp_seq=3 ttl=51 time=91.1 ms
64 bytes from 104.16.123.96 (104.16.123.96): icmp_seq=4 ttl=51 time=66.6 ms
64 bytes from 104.16.123.96 (104.16.123.96): icmp_seq=5 ttl=51 time=209 ms
^C64 bytes from 104.16.123.96: icmp_seq=6 ttl=51 time=84.4 ms

--- www.cloudflare.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 9374ms
rtt min/avg/max/mdev = 66.610/159.877/295.280/84.342 ms
[user@router ~]$ 


```

### ☀️ Accès internet LAN1 et LAN2

ajoutez une route par défaut sur les deux machines du LAN1


ajoutez une route par défaut sur les deux machines du LAN2


configurez l'adresse d'un serveur DNS que vos machines peuvent utiliser pour résoudre des noms

dans le compte-rendu, mettez-moi que la conf des points précédents sur node2.lan1.tp2

prouvez que node2.lan1.tp2 a un accès internet :

il peut ping une IP publique

il peut ping un nom de domaine public

```
[user@node2 ~]$ hostname
node2.lan1.tp2
[user@node2 ~]$ cat /etc/sysconfig/network-scripts/route-enp0s3
192.168.57.0/24 via 192.168.56.254
default via 10.0.2.4
[user@node2 ~]$ cat /etc/resolv.conf
# Generated by NetworkManager
search lan1.tp2
nameserver 1.1.1.1
[user@node2 ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=50 time=259 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=50 time=475 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=50 time=293 ms
^C
--- 1.1.1.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2012ms
rtt min/avg/max/mdev = 258.936/342.301/475.152/94.952 ms
[user@node2 ~]$ ping www.cloudflare.com
PING www.cloudflare.com (104.16.124.96) 56(84) bytes of data.
64 bytes from 104.16.124.96 (104.16.124.96): icmp_seq=1 ttl=50 time=377 ms
64 bytes from 104.16.124.96 (104.16.124.96): icmp_seq=2 ttl=50 time=430 ms
64 bytes from 104.16.124.96 (104.16.124.96): icmp_seq=3 ttl=50 time=171 ms
^C
--- www.cloudflare.com ping statistics ---
4 packets transmitted, 3 received, 25% packet loss, time 3255ms
rtt min/avg/max/mdev = 170.888/325.952/429.675/111.713 ms
[user@node2 ~]$ 


```
## III. Services réseau

### 1. DHCP

#### ☀️ Sur dhcp.lan1.tp2

###### n'oubliez pas de renommer la machine (node2.lan1.tp2 devient dhcp.lan1.tp2)
###### hangez son adresse IP en 10.1.1.253

###### setup du serveur DHCP

commande d'installation du paquet, fichier de conf, service actif

```
user@dhcp ~]$ sudo dnf install -y dhcp-server
[sudo] password for user: 
Last metadata expiration check: 0:10:54 ago on Wed 28 Feb 2024 08:37:16 PM CET.
Package dhcp-server-12:4.4.2-19.b1.el9.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
[user@dhcp ~]$ hostname
dhcp.lan1.tp2
[user@dhcp ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:d9:ce:73 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.253/24 brd 192.168.56.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fed9:ce73/64 scope link 
       valid_lft forever preferred_lft forever
[user@dhcp ~]$ sudo system enable dhcpd
sudo: system: command not found
[user@dhcp ~]$ sudo systemctl enable dhcpd
Created symlink /etc/systemd/system/multi-user.target.wants/dhcpd.service → /usr/lib/systemd/system/dhcpd.service.
[user@dhcp ~]$ sudo systemctl start dhcpd
[user@dhcp ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Wed 2024-02-28 20:49:15 CET; 5s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 2014 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 11037)
     Memory: 5.2M
        CPU: 12ms
     CGroup: /system.slice/dhcpd.service
             └─2014 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no->

Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Config file: /etc/dhcp/dhcpd.conf
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Database file: /var/lib/dhcpd/dhcpd.leases
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: PID file: /var/run/dhcpd.pid
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Source compiled to use binary-leases
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Wrote 0 leases to leases file.
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Listening on LPF/enp0s3/08:00:27:d9:ce:73/192.168.5>
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Sending on   LPF/enp0s3/08:00:27:d9:ce:73/192.168.5>
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Sending on   Socket/fallback/fallback-net
Feb 28 20:49:15 dhcp.lan1.tp2 systemd[1]: Started DHCPv4 Server Daemon.
Feb 28 20:49:15 dhcp.lan1.tp2 dhcpd[2014]: Server starting service.

[user@dhcp ~]$ cat /etc/dhcp/dhcpd.conf
cat: /etc/dhcp/dhcpd.conf: Permission denied
[user@dhcp ~]$ sudo !!
sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
default-lease-time 600;
# max lease time

max-lease-time 7200;

option domain-name-servers     1.1.1.1; 

authoritative;
# specify network address and subnetmask

subnet 192.168.56.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 192.168.56.100 192.168.56.200;
    # specify gateway
    option routers 192.168.56.254;
    }


[user@dhcp ~]$ 


```

#### ☀️ Sur node1.lan1.tp2

##### demandez une IP au serveur DHCP
##### prouvez que vous avez bien récupéré une IP via le DHCP
##### prouvez que vous avez bien récupéré l'IP de la passerelle
##### prouvez que vous pouvez ping node1.lan2.tp2

```
[user@node1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:db:4b:ac brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.100/24 brd 192.168.56.255 scope global dynamic noprefixroute enp0s3
       valid_lft 548sec preferred_lft 548sec
    inet6 fe80::a00:27ff:fedb:4bac/64 scope link 
       valid_lft forever preferred_lft forever
[user@node1 ~]$ sudo cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
[sudo] password for user: 
NAME=enp0s3
DEVICE=enp0s3
ONBOOT=yes
BOOTPROTO=dhcp
NETMASK=255.255.255.0

[user@node1 ~]$ ip route show
default via 10.0.2.4 dev enp0s3 proto static metric 100 
default via 192.168.56.254 dev enp0s3 proto dhcp src 192.168.56.100 metric 100 
10.0.2.4 dev enp0s3 proto static scope link metric 100 
192.168.56.0/24 dev enp0s3 proto kernel scope link src 192.168.56.100 metric 100 
192.168.57.0/24 via 192.168.56.254 dev enp0s3 proto static metric 100 
[user@node1 ~]$ ping 192.168.57.11
PING 192.168.57.11 (192.168.57.11) 56(84) bytes of data.
64 bytes from 192.168.57.11: icmp_seq=1 ttl=63 time=1.56 ms
64 bytes from 192.168.57.11: icmp_seq=2 ttl=63 time=0.924 ms
64 bytes from 192.168.57.11: icmp_seq=3 ttl=63 time=0.767 ms
64 bytes from 192.168.57.11: icmp_seq=4 ttl=63 time=0.658 ms
^C
--- 192.168.57.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3120ms
rtt min/avg/max/mdev = 0.658/0.978/1.563/0.350 ms
[user@node1 ~]$ 


```

### 2. Web web web

#### ☀️ Sur web.lan2.tp2

```
[user@web www]$ mkdir site_nul
mkdir: cannot create directory ‘site_nul’: Permission denied
[user@web www]$ sudo !!
sudo mkdir site_nul
[user@web www]$ ls
site_nul
[user@web www]$ ls
site_nul
[user@web www]$ cd site_nul/
[user@web site_nul]$ touch index.thml
touch: cannot touch 'index.thml': Permission denied
[user@web site_nul]$ sudo !!
sudo touch index.thml
[user@web site_nul]$ ls
index.thml
[user@web site_nul]$ sudo vim index.thml
[user@web site_nul]$ cd
[user@web ~]$ sudo vim /etc/nginx/nginx.conf
[user@web ~]$ sudo systemctl restart nging
Failed to restart nging.service: Unit nging.service not found.
[user@web ~]$ sudo systemctl restart nginx
[user@web ~]$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --reload
success
success
[user@web ~]$ sudo ss -tulpn | grep :80
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=1455,fd=6),("nginx",pid=1454,fd=6),("nginx",pid=1453,fd=6))
[user@web ~]$ sudo firewall-cmd --list-ports
80/tcp
[user@web site_nul]$ rm index.thml //thml au lieu de html
rm: remove write-protected regular file 'index.thml'? y
rm: cannot remove 'index.thml': Permission denied
[user@web site_nul]$ sudo !!
sudo rm index.thml
[user@web site_nul]$ ls
[user@web site_nul]$ sudo vim index.html 
[user@web site_nul]$ sudo cat /etc/nginx/nginx.conf
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
    listen 80;
    server_name site_nul.tp2;

    location / {
        root /var/www/site_nul/;
        index index.html;
    }
}


# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}

[user@web site_nul]$ 




```

#### ☀️ Sur node1.lan1.tp2

```
[user@node1 ~]$ curl http://site_nul.tp2
curl: (6) Could not resolve host: site_nul.tp2
[user@node1 ~]$ sudo vi /etc/hosts
[sudo] password for user: 
[user@node1 ~]$ curl http://site_nul.tp2
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.20.1</center>
</body>
</html>

// j'avais mis index.thml au lieu de html


[user@node1 ~]$ curl site_nul.tp2
<html>
<head>
    <title>Test Page</title>
</head>
<body>
    <p>test</p>
</body>
</html>

[user@node1 ~]$ 


```