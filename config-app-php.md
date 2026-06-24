# Configure app php


## Configurer le Projet

### Configurer la VM
```ip a```
192.168.X.X

Il se peut que l'ip ne commence pas par 192 mais par 10. Notre objectif est que l'ip de notre vm soit identique à celle de de notre machine hôte. Pour cela nous allons utiliser l'accès par pont dans les configuration réseau de la vm. Dans ce cas il faut aller dans 
    `> VirtualBox > Click sur ta vm > Configuration > Réseau > Accès par pont > Choisir la Wifi`


Pour se connecter a sa vm depuis sa machine hôte il suffit de faire :
    `ssh user@192.168.X.X`


### Installer les dépendances 

Liste des dépendances: 
- Serveur Apache2 `sudo apt install apache2`
- PHP `sudo apt install php libapache2-mod-php php-cli php-mysql`
- Mysql `sudo apt install mysql-server`
- PhpMyAdmin ``sudo apt install phpmyadmin``
    Pour sélectionner le serveur apache2 pensez a appuyer sur étoile ; Si fail on peut le reconfiguer à l'aide de la commande: 
        `sudo dpkg-reconfigure phpmyadmin`

Relancer apache:
`sudo systemctl restart apache2.service`

Attention il faut forward le port 80 pour y accéder dans le navigateur de la machine hôte.

### Créer un user mysql
Pour accéder à la base de données via phpmyadmin il faut créer un user:
```sh
sudo mysql
```
```SQL
CREATE USER 'dam' IDENTIFIED BY 'toto';
GRANT ALL ON *.* TO 'dam' WITH GRANT OPTION;
FLUSH PRIVILEGES;
QUIT;
```

Ensuite on peux y accéder via l'url: <link>http://localhost/phpmyadmin</link>

### Créer son url

Il est important de savoir que la racine d'un projet php se trouve dans `/var/www/html`.
Nous allons modifier la racine pour `/var/www/portfolio.damienbussy`.

Créer un `/var/www/portfolio.damienbussy/index.html`:
```html
<h1>Portfolio</h1>
```

Modifier les config d'apache en ajoutant un VirtualHost:
```shell
nano /etc/apache2/sites-available/portfolio.damienbussy.conf
```

```shell
<VirtualHost *:80>
        ServerName portfolio.damienbussy
        ServerAlias www.portfolio.damienbussy
        DocumentRoot "/var/www/portfolio.damienbussy"
        <Directory "/var/www/portfolio.damienbussy">
                Options FollowSymLinks
                AllowOverride all
                Require all granted
        </Directory>
        ErrorLog /var/log/apache2/error.portfolio.damienbussy.log
        CustomLog /var/log/apache2/access.portfolio.damienbussy.log combined
</VirtualHost>
```

Activer ce nouveau site:
```sh
sudo a2ensite portfolio.damienbussy
    Enabling site portfolio.damienbussy.
    To activate the new configuration, you need to run:
        systemctl reload apache2
```
Remarque: `a2: apache2`, `ensite: enable site`

Ca ne fonctionne pas encore.
Il faut éditer le fichier `/etc/hosts` afin d'ajouter `portfolio.damienbussy` pour l'adresse locale:
```sh
127.0.0.1 localhost
127.0.1.1 pcdam
127.0.0.1 portfolio.damienbussy
```

Le faire aussi sur la machine hôte:
Pour ce faire il faut exécuter un terminale powershell en tant qu'admin. 
```shell
cd C:\Windows\System32\drivers\etc\
```
Ajouter la ligne:
```sh
192.168.X.X portfolio.damienbussy
```

### Granted parmission on project
Maintenant que le projet est correctement configuré, pour pouvoir le modifier il faut modifier les droits de l'user pour qu'on ait toutes les permissions dans le projet: 
```sh
sudo chmod -R 777 .
```

