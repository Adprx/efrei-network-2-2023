## I. Docker

### 1. Install

- 🌞Installer Docker sur la machine

```$ pacman -S docker```

- Démarrer le service docker avec une commande systemctl
  
```$ systemctl start docker```

- Ajouter votre utilisateur au groupe docker
  
```$ sudo usermod -aG docker $(whoami)```

### 🌞3. Lancement de conteneurs 

#### Utiliser la commande docker run
lancer un conteneur nginx

- l'app NGINX doit avoir un fichier de conf personnalisé
- l'app NGINX doit servir un fichier index.html personnalisé
- l'application doit être joignable grâce à un partage de ports
vous limiterez l'utilisation de la RAM et du CPU de ce conteneur
- le conteneur devra avoir un nom
Tout se fait avec des options de la commande docker run.
- Créer un fichier de conf NGINX sur l'hôte
- il porte l'extension .conf
- il comporte une conf minimale pour écouter sur un port et servir un site dans un dossier précis
- grâce à une option -v ... sur le docker run
- de poser votre fichier de conf dans /etc/nginx/conf.d/
```
~/td_docker/nginx 
❯ cat index.html 
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: index.html
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ test
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

~/td_docker/nginx 
❯ cat ad.conf 
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: ad.conf
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ server {
   2   │   # on définit le port où NGINX écoute dans le conteneur
   3   │   listen 9999;
   4   │   
   5   │   # on définit le chemin vers la racine web
   6   │   # dans ce dossier doit se trouver un fichier index.html
   7   │   root /var/www/tp_docker; 
   8   │ }
   9   │ 
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

~/td_docker/nginx 
❯ docker run -d --rm --name adrien -p 8888:9999 -v /home/tiroir/td_docker/nginx/index.html:/var/www/tp_docker/index.html -v /home/tiroir/td_docker/nginx/ad.conf:/etc/nginx/conf.d/ad.conf nginx
3cbbc4456ab60b84bfae3d54b9132c77d7aebfe7b1b65d9f007b3571df0e50b7
```

### 🌞2. Construisez votre propre Dockerfile
Construire votre propre image
image de base (celle que vous voulez : debian, alpine, ubuntu, etc.)
une image du Docker Hub
qui ne porte aucune application par défaut

```$ docker pull debian```

vous ajouterez
mise à jour du système
installation de Apache (pour les systèmes debian, le serveur Web apache s'appelle apache2 et non pas httpd comme sur Rocky)
page d'accueil Apache HTML personnalisée

## III. docker-compose





