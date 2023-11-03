# TP3

### I. Setup GNS3

#### 🌞 Mettre en place la topologie dans GS3

Reproduisez la topologie, configurez les IPs et les noms sur toutes les machines
Une fois en place, assurez-vous donc que :

Toutes les machines du réseau 1 peuvent se ping entre elles:

node1net1 -> routernet1

```
[adprx@node1net1 ~]$ ping 10.3.1.254
PING 10.3.1.254 (10.3.1.254) 56(84) bytes of data.
64 bytes from 10.3.1.254: icmp_seq=1 ttl=64 time=6.41 ms
64 bytes from 10.3.1.254: icmp_seq=2 ttl=64 time=1.93 ms
^C
--- 10.3.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.930/4.172/6.414/2.242 ms
```

node2net1 -> node1net1

```
[adprx@node2net1 ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.177 ms
^C
--- 10.3.1.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.044/0.110/0.177/0.066 ms
```

routernet1 -> node2net1

```
[adprx@routernet1 ~]$ ping  10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=4.98 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=2.40 ms
^C
--- 10.3.1.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 2.395/3.688/4.982/1.293 ms
```

Toutes les machines du réseau 2 peuvent se ping entre elles:

routernet2 -> node2net2

```
[adprx@routernet2 ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=64 time=1.85 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=64 time=2.24 ms
^C
--- 10.3.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.848/2.045/2.243/0.197 ms
```

node2net2 -> node1net2

```
[adprx@node2net2 ~]$ ping 10.3.2.11
PING 10.3.2.11 (10.3.2.11) 56(84) bytes of data.
64 bytes from 10.3.2.11: icmp_seq=1 ttl=64 time=4.70 ms
64 bytes from 10.3.2.11: icmp_seq=2 ttl=64 time=4.46 ms
^C
--- 10.3.2.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 4.463/4.581/4.699/0.118 ms
```

node1net1 -> routernet2

```
[root@node1net2 ~]# ping 10.3.2.254
PING 10.3.2.254 (10.3.2.254) 56(84) bytes of data.
64 bytes from 10.3.2.254: icmp_seq=1 ttl=64 time=3.99 ms
64 bytes from 10.3.2.254: icmp_seq=2 ttl=64 time=2.63 ms
^C
--- 10.3.2.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 2.626/3.307/3.989/0.681 ms
```

Toutes les machines du réseau 3 peuvent se ping entre elles:

router1net3 -> router2net3

```
[adprx@routernet1 ~]$ ping  10.3.100.2
PING 10.3.100.2 (10.3.100.2) 56(84) bytes of data.
64 bytes from 10.3.100.2: icmp_seq=1 ttl=64 time=3.71 ms
64 bytes from 10.3.100.2: icmp_seq=2 ttl=64 time=2.23 ms
^C
--- 10.3.100.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 2.234/2.970/3.706/0.736 ms
```

routeur2net3 -> routeur1net3
```
[adprx@routernet2 ~]$ ping 10.3.100.1PING 10.3.100.1 (10.3.100.1) 56(84) bytes of data.
64 bytes from 10.3.100.1: icmp_seq=1 ttl=64 time=2.95 ms
64 bytes from 10.3.100.1: icmp_seq=2 ttl=64 time=1.26 ms
^C
--- 10.3.100.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 1.255/2.101/2.948/0.846 ms
```


Le router1.tp3 doit avoir un accès internet normal

Référez-vous au TP2 pour le setup
Prouvez avec une commande ping qu'il peut joindre une IP publique connue:
```
[adprx@routernet1 ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=128 time=489 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=128 time=9.22 ms
^C
--- 1.1.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 9.217/248.969/488.722/239.752 ms
```

Prouvez avec une commande ping qu'il peut joindre des machines avec leur nom DNS public (genre efrei.fr):

```
[adprx@routernet1 ~]$ ping efrei.frPING efrei.fr (51.255.68.208) 56(84) bytes of data.
64 bytes from ns3028977.ip-51-255-68.eu (51.255.68.208): icmp_seq=1 ttl=128 time=15.4 ms
64 bytes from ns3028977.ip-51-255-68.eu (51.255.68.208): icmp_seq=2 ttl=128 time=15.8 ms
^C
--- efrei.fr ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 15.354/15.556/15.758/0.202 ms
```


### II. Routes routes routes
#### 🌞 Activer le routage sur les deux machines router

```
[adprx@routernet1 ~]$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
[adprx@routernet1 ~]$ sudo firewall-cmd --add-masquerade
success
[adprx@routernet1 ~]$ sudo firewall-cmd --add-masquerade --permanent
success
```

```
[adprx@routernet2 ~]$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
[adprx@routernet2 ~]$ sudo firewall-cmd --add-masquerade
success
[adprx@routernet2 ~]$ sudo firewall-cmd --add-masquerade --permanent
success
```


#### 🌞 Mettre en place les routes locales
