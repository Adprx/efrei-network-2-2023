## I. DHCP

### 🌞 Setup de la machine dhcp.net1.tp3

il doit donner des IPs aux clients du réseau 1 entre 10.3.1.50/24 et 10.3.1.99/24
il doit aussi informer les clients du réseau 1 de l'adresse de leur passerelle
il doit aussi indiquer l'adresse IP 1.1.1.1 comme serveur DNS utilisable par les clients
je veux voir le fichier de config dans le compte-rendu

```
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
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
subnet 10.3.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.3.1.50 10.3.1.99;
    # specify broadcast address
    option broadcast-address 10.3.1.255;
    # specify gateway
    option routers 10.3.1.254;
}
```
### 🌞 Preuve !

Effectuer une demande d'adresse IP depuis node1.net1.tp3:
```
[adprx@dhcpnet1 ~]$ sudo dhclient -r enp0s3
[adprx@dhcpnet1 ~]$ sudo dhclient enp0s3
```
Montrez l'IP obtenue:
```
[adprx@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:ec:35:a9 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.50/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 503sec preferred_lft 503sec
    inet 10.3.1.51/24 brd 10.3.1.255 scope global secondary dynamic enp0s3
       valid_lft 654sec preferred_lft 654sec
    inet6 fe80::a00:27ff:feec:35a9/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e6:ea:bc brd ff:ff:ff:ff:ff:ff
    inet 192.168.83.15/24 brd 192.168.83.255 scope global dynamic noprefixroute enp0s8
       valid_lft 574sec preferred_lft 574sec
    inet6 fe80::6f9f:2a40:a044:5dbe/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
Montrez que votre table de routage a été mise à jour:
```
[adprx@localhost ~]$ ip r s
default via 10.3.1.254 dev enp0s3
default via 10.3.1.254 dev enp0s3 proto dhcp src 10.3.1.50 metric 102
10.3.1.0/24 dev enp0s3 proto kernel scope link src 10.3.1.50 metric 102
192.168.83.0/24 dev enp0s8 proto kernel scope link src 192.168.83.15 metric 101
```
Montrez l'adresse du serveur DNS que vous utilisez:
````
[adprx@localhost ~]$ dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39050
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             43      IN      A       142.250.75.238

;; Query time: 22 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mon Nov 06 23:10:17 CET 2023
;; MSG SIZE  rcvd: 55
```
