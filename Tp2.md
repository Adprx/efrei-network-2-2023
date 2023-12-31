# TP2

## I.ROUTAGE

#### ☀️ Configuration de router.tp2.efrei

Configurer de façon statique son IP:

```[root@node1tp2 network-scripts]# cat ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.2.1.1
NETMASK=255.255.255.0
```  

Prouvez avec une commande ping que node1.tp2.efrei peut joindre router.tp2.efrei

```[root@node1tp2 network-scripts]# ping 10.2.1.254
PING 10.2.1.254 (10.2.1.254) 56(84) bytes of data.
64 bytes from 10.2.1.254: icmp_seq=1 ttl=64 time=2.76 ms
64 bytes from 10.2.1.254: icmp_seq=2 ttl=64 time=5.14 ms
^C
--- 10.2.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 2.764/3.953/5.142/1.189 ms
```
Ajoutez une route par défaut qui passe par router.tp2.efrei
```
[root@node1tp2 network-scripts]# cat ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.2.1.1
NETMASK=255.255.255.0

GATEWAY=10.2.1.254
DNS1=1.1.1.1
```
Prouvez que vous avez un accès internet depuis node1.tp2.efrei désormais, avec une commande ping
```
[root@node1tp2 network-scripts]# ping google.fr
PING google.fr (172.217.20.195) 56(84) bytes of data.
64 bytes from waw02s08-in-f195.1e100.net (172.217.20.195): icmp_seq=1 ttl=127 time=26.7 ms
64 bytes from waw02s08-in-f3.1e100.net (172.217.20.195): icmp_seq=2 ttl=127 time=25.3 ms
^C
--- google.fr ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 25.339/26.006/26.674/0.667 ms
```
Utilisez une commande traceroute pour prouver que vos paquets passent bien par router.tp2.efrei avant de sortir vers internet
```
[root@node1tp2 network-scripts]# traceroute google.com
traceroute to google.com (142.250.178.142), 30 hops max, 60 byte packets
 1  _gateway (10.2.1.254)  3.935 ms  3.798 ms  3.700 ms
 2  192.168.195.2 (192.168.195.2)  5.186 ms  6.131 ms  9.933 ms^C
````
## II. Serveur DHCP

#### ☀️ Install et conf du serveur DHCP sur dhcp.tp2.efrei

Pour l'install du serveur, il faut un accès internet... il suffit d'ajouter là encore une route par défaut, qui passe par router.tp2.efrei
```
[root@node1tp2 network-scripts]# cat ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.2.1.1
NETMASK=255.255.255.0

GATEWAY=10.2.1.254
```
Cette fois, dans la conf, ajoutez une option DHCP pour donner au client l'adresse de la passerelle du réseau (c'est à dire l'adresse de router.tp2.efrei) en plus de leur proposer une IP libre
```
[root@dhcptp2 adprx]# cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
#

# create new

# specify domain name

option domain-name     "srv.world";
# specify DNS server's hostname or IP address

option domain-name-servers     1.1.1.1;
# default lease time

default-lease-time 600;
# max lease time

max-lease-time 7200;
# this DHCP server to be declared valid

authoritative;
# specify network address and subnetmask

subnet 10.2.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.2.1.200 10.2.1.253;
    # specify broadcast address
    option broadcast-address 10.2.1.255;
    # specify gateway
    option routers 10.2.1.254;
}
```
#### ☀️ Test du DHCP sur node1.tp2.efrei

enlevez toute config IP effectuée au préalable:
```
[root@node1 adprx]# nmcli con del enp0s3
Connection 'enp0s3' (9fba3e11-1548-316f-908b-cf39777f9a96) successfully deleted.
Connection 'enp0s3' (3c36b8c2-334b-57c7-91b6-4401f3489c69) successfully deleted.

configurez l'interface pour qu'elle récupère une IP dynamique, c'est à dire avec DHCP:

[root@node1 ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```
vérifiez que :

l'IP obtenue est correcte:
```
[root@node1 adprx]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:74:d8:87 brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.200/24 brd 10.2.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 432sec preferred_lft 432sec
    inet6 fe80::a00:27ff:fe74:d887/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:51:c7:bf brd ff:ff:ff:ff:ff:ff
    inet 192.168.83.4/24 brd 192.168.83.255 scope global dynamic noprefixroute enp0s8
       valid_lft 576sec preferred_lft 576sec
    inet6 fe80::fe04:b4cd:7eca:88c7/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
votre table de routage a bien été mise à jour automatiquement avec l'adresse de la passerelle en route par défaut (votre option DHCP a bien été reçue !)

```
[root@node1 adprx]# ip r s
default via 10.2.1.254 dev enp0s3 proto dhcp src 10.2.1.200 metric 102
10.2.1.0/24 dev enp0s3 proto kernel scope link src 10.2.1.200 metric 102
192.168.83.0/24 dev enp0s8 proto kernel scope link src 192.168.83.4 metric 101

vous pouvez immédiatement joindre internet:

[root@node1 adprx]# ping google.com
PING google.com (142.250.178.142) 56(84) bytes of data.
64 bytes from par21s22-in-f14.1e100.net (142.250.178.142): icmp_seq=1 ttl=127 time=17.3 ms
64 bytes from par21s22-in-f14.1e100.net (142.250.178.142): icmp_seq=2 ttl=127 time=23.9 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 17.273/20.599/23.925/3.326 ms
```
#### 🌟 BONUS

ajouter une autre ligne dans la conf du serveur DHCP pour qu'il donne aussi l'adresse d'un serveur DNS (utilisez 1.1.1.1 comme serveur DNS : c'est l'un des serveurs DNS de CloudFlare, un gros acteur du web)
```
[root@dhcptp2 adprx]# vim /etc/dhcp/dhcpd.conf
Et j'ai ajouté cette ligne: option domain-name-servers     1.1.1.1;
```
#### ☀️ Wireshark it !

[Capture DHCP](dhcp.pcapng)

## III. ARP

### 1. Les tables ARP

#### ☀️ Affichez la table ARP de router.tp2.efrei
```
[adprx@routeur ~]$ ip n s
10.2.1.200 dev enp0s9 lladdr 08:00:27:74:d8:87 STALE
192.168.83.1 dev enp0s8 lladdr 0a:00:27:00:00:04 REACHABLE
192.168.83.2 dev enp0s8 lladdr 08:00:27:02:92:30 STALE
192.168.195.2 dev enp0s3 lladdr 00:50:56:fc:97:b9 DELAY
10.2.1.253 dev enp0s9 lladdr 08:00:27:3e:41:e2 STALE
```
#### ☀️ Capturez l'échange ARP avec Wireshark

[Capture ARP](arp.pcapng)

### 2. ARP poisoning

#### ☀️ Exécuter un simple ARP poisoning
```
[adprx@node1 ~]$ ip neigh change 10.2.1.254 lladdr aa:bb:cc:dd:ee:ff dev enp0s3
RTNETLINK answers: Operation not permitted
[adprx@node1 ~]$ sudo !!
sudo ip neigh change 10.2.1.254 lladdr aa:bb:cc:dd:ee:ff dev enp0s3

[adprx@node1 ~]$ ip n s
10.2.1.254 dev enp0s3 lladdr aa:bb:cc:dd:ee:ff PERMANENT
192.168.83.1 dev enp0s8 lladdr 0a:00:27:00:00:04 REACHABLE
192.168.83.2 dev enp0s8 lladdr 08:00:27:02:92:30 STALE
10.2.1.253 dev enp0s3 lladdr 08:00:27:3e:41:e2 STALE
```
