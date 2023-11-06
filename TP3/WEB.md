## 1. Installation

L'install et la config sont à réaliser sur la machine web.net2.tp3.

#### 🌞 Installation du serveur web NGINX
```
dnf install nginx
```

## 2. Page HTML et racine web

### 🌞 Création d'une bête page HTML

on va créer un nouveau dossier qui hébergera tous les fichiers de notre site (bon là y'en aura qu'un, et il sera moche, c'est un détail)
créez le dossier /var/www/efrei_site_nul/:

[root@webnet2]# mkdir -p /var/www/efrei_site_nul

- Ce dossier va contenir tous les fichiers de notre site web. On l'appelle la racine de notre site web. Ou racine web. Ou webroot pour les anglophones.
- faites le appartenir à l'utilisateur nginx (sinon le contenu du dossier ne sera pas accessible par le serveur Web NGINX, et il ne pourra pas servir le site !)
```
[root@webnet2 var]# chown nginx www/efrei_site_nul/
[root@webnet2 var]# ls -l www/
total 0
drwxr-xr-x. 2 nginx root 23 Nov  6 23:29 efrei_site_nul
```
- créez un fichier texte /var/www/efrei_site_nul/index.html avec la phrase de votre choix à l'intérieur
```
[root@webnet2 efrei_site_nul]# touch index.html && echo "coucou EFREI"> index.html
```
- ce fichier aussi doit appartenir à l'utilisateur nginx
```
[root@webnet2 efrei_site_nul]# chown nginx index.html
[root@webnet2 efrei_site_nul]# ls -l
total 4
-rw-r--r--. 1 nginx root 13 Nov  6 23:46 index.html
```

## 3. Config de NGINX

### 🌞 Création d'un fichier de configuration NGINX

- créez le fichier /etc/nginx/conf.d/web.net2.tp3.conf et ajoutez le contenu suivant :

```
[root@webnet2 efrei_site_nul]# cat /etc/nginx/conf.d/web.net2.tp3.conf
 server {
      # on indique le nom d'hôte du serveur
      server_name   web.net2.tp3;

      # on précise sur quelle IP et quel port on veut que le site soit dispo
      listen        10.3.2.101:80;

      location      / {
          # on indique l'endroit où se trouve notre racine web
          root      /var/www/efrei_site_nul;

          # et on indique le nom de la page d'accueil, pour pas que le client ait besoin de le préciser explicitement
          index index.html;
      }
  }
```
## 4. Firewall

### 🌞 Ouvrir le port nécessaire dans le firewall

- le trafic HTTP c'est encapsulé dans du TCP
- comme indiqué dans la conf, on va servir le site sur le port standard : 80
- ouvrez donc à l'aide d'une commande le port 80 dans le firewall de web.net2.tp3 (voir le mémo)

```
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```
- vérifiez avec une deuxième commande que le port est bien actuellement ouvert dans le firewall
```
[root@webnet2 efrei_site_nul]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 80/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

## 5. Test

### 🌞 Démarrez le service NGINX !

```
[root@webnet2 efrei_site_nul]# systemctl start nginx
[root@webnet2 efrei_site_nul]# sudo systemctl enable nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
```

### 🌞 Test local

- vous pouvez visiter le site web en local, depuis la ligne de commande de la machine web.net2.tp3, avec la commande curl : par exemple curl http://10.3.2.101

```
[root@webnet2 efrei_site_nul]# curl http://10.3.2.101
coucou EFREI
```

### 🌞 Accéder au site web depuis un client

- direction n'importe quelle machine du réseau 1, et accédez au site web
- vous pouvez ajouter une machine avec interface graphique si vous voulez, sinon un curl fera très bien l'affaire !

```
[adprx@node1net1 ~]$ curl http://10.3.2.101
coucou EFREI
```

### 🌞 Avec un nom ?

- utilisez le fichier hosts de votre machine client pour accéder au site web en saissant http://web.net2.tp3
```
[adprx@node1net1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.2.101  web.net2.tp3
```
