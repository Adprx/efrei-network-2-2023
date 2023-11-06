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

```
