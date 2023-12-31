## 3.Firewall

### 🌞 Ouvrir le port nécessaire dans le firewall

- ouvrez donc à l'aide d'une commande ce port UDP dans le firewall de dns.net2.tp3
```
[root@dnsnet2 adprx]# sudo firewall-cmd --add-port=53/udp --permanent
success
[root@dnsnet2 adprx]# sudo firewall-cmd --reload
success
```
- vérifiez avec une deuxième commande que le port est bien actuellement ouvert dans le firewall
```
[root@dnsnet2 adprx]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 53/udp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
## 4. Test

### 🌞 Depuis l'une des machines clientes du réseau 1

- utiliser dig pour trouver à quelle IP correspond le nom web.net2.tp3
```
[adprx@node1net1 ~]$ dig @10.3.2.102 web.net2.tp3

; <<>> DiG 9.16.23-RH <<>> @10.3.2.102 web.net2.tp3
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 57027
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: a9ccb6a440ccee1101000000653fb2893e27addc68799fe6 (good)
;; QUESTION SECTION:
;web.net2.tp3.                  IN      A

;; ANSWER SECTION:
web.net2.tp3.           86400   IN      A       10.3.2.101

;; Query time: 12 msec
;; SERVER: 10.3.2.102#53(10.3.2.102)
;; WHEN: Mon Nov 06 04:27:18 CET 2023
;; MSG SIZE  rcvd: 85
```

- utiliser curl pour visiter le site web sur web.net2.tp3 en utilisant son nom.
```
[adprx@node1net1 ~]$ curl web.net2.tp3
coucou EFREI
```
- assurez-vous de purger votre fichier hosts de vos éventuelles précédentes modifications
```
[adprx@node1net1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.2.101  web.net2.tp3
```

## 5. DHCP my old friend

### 🌞 Editez la configuration du serveur DHCP sur dhcp.net1.tp3

- l'adresse du serveur DNS qui est donnée au client doit désormais être celle de dns.net2.tp3 (il faut bien préciser une IP, pas le nom)
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
    option domain-name-servers     10.3.2.102;
}    
```

- prouvez que ça fonctionne avec un dig depuis un client qui a fraîchement récupéré une IP
```
[adprx@node1net1 ~]$ dig web.net2.tp3

; <<>> DiG 9.16.23-RH <<>> web.net2.tp3
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50825
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: c726ccc29020250001000000653fb8757c7e5f35e56bc98f (good)
;; QUESTION SECTION:
;web.net2.tp3.                  IN      A

;; ANSWER SECTION:
web.net2.tp3.           86400   IN      A       10.3.2.101

;; Query time: 8 msec
;; SERVER: 10.3.2.102#53(10.3.2.102)
;; WHEN: Mon Nov 06 04:31:25 CET 2023
;; MSG SIZE  rcvd: 85
```
