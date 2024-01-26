# Documentation Archi Linux

Cette documentation détaille le déploiement de notre site web à travers trois machines virtuelles sur un serveur Linux. Ce système permet à tout utilisateur connecté au même réseau que ces machines d'accéder au site depuis un terminal Linux.

## Configuration

**Pré-requis** : un ordinateur Linux ou machine virtuelle avec Linux installé

### Machines virtuelles pour faire fonctionner Linux

- Virtualbox si Intel
- UTM si M1, M2 etc

### VPN Mesh

Installation curl

```
sudo apt install curl
```

Installation pip3

```
sudo apt-get install pip3
```

Installation Tailscale : Linux

```
curl -fsSL https://tailscale.com/install.sh | sh : installer tailscape
```

 Se connecter à tailscape

```
sudo tailscale up
```

### Environnement

- Accéder au fichier contenant l’adresse IP du serveur :
    
    ```
    sudo nano /etc/network/interfaces
    ```
    
- Accéder au fichier contenant l’adresse bind :
    
    ```
    sudo nano/etc/mysql/mariadb.conf.d/50-server.cnf
    ```
    
- Une fois le fichier de l’adresse IP et l’adresse bind modifiés, redémarrer le réseau :
    
    ```
    sudo systemctl restart networking
    ```
    
- Afficher les différentes adresses IP connectées à la machine virtuelle :
    
    ```
    ip addr show
    ```
    
- Changer le nom de la machine virtuelle :
    
    ```
    sudo nano /etc/hosts
    ```
    
- Redémarrer la machine virtuelle pour implanter les nouveaux changements :
    
    ```
    sudo reboot
    ```
    
- `command`+`maj`+`p` : connection ssh dans VS code

### Développement

- MariaDB
    - id mariaDB : 33
    - version du serveur : 10.5.21-MariaDB-0+deb11u1 Debian 11
    - Installer MariaDB :
        
        ```
        sudo apt install mariadb-server
        ```
        
    - Activer MariaDB :
        
        ```
        systemctl start mariadb
        ```
        
    
- PHP
    
    ```
    apt-get install php
    ```
    
- Python
    
    ```
    sudo apt-get install python3
    ```
    

## Architecture du réseau

Pour accéder au site, il est nécessaire d'utiliser un terminal Linux connecté au même réseau que les machines virtuelles et associé au compte Tailscape spécifié.

Tailscale permet la création d'un réseau privé virtuel sécurisé où les machines, qu'elles soient physiques ou virtuelles, peuvent communiquer directement entre elles, facilitant ainsi la mise en réseau et le partage de ressources au sein de cet environnement.

Il fonctionne en tant que VPN de type "mesh" (*maille*) : chaque machine connectée à Tailscale a la possibilité de communiquer directement avec d'autres machines de manière chiffrée, sans nécessiter un passage par un serveur central.

**Topologie réseau**

Les machines connectées via Tailscale peuvent être situées à des endroits différents. Cela permet un accès distant sécurisé aux ressources de votre réseau, comme si les machines étaient physiquement connectées au même réseau local.

![Réseau.png](Documentation%20Archi%20Linux%203ecd398226ca456189a1e708f48b1558/Reseau.png)

- La machine virtuelle de Julien héberge la base de données sous MariaDB, choisie pour ses avantages pratiques et fonctionnelles. Cette machine stocke et gère toutes les données du site.
- La machine virtuelle de Fabien est responsable de l’API, cette dernière construire en langage Python. Elle sert d'intermédiaire pour récupérer les données de la base de données de la machine de Julien et les transmettre selon les requêtes reçues.
- La machine virtuelle d’Hélène affiche les données utilisateurs en récupérant les informations via l'API de la machine de Fabien. Cette machine gère l'interface utilisateur et la présentation des données.

Toutes ces machines virtuelles sont correctement configurées et connectées au même réseau. Le compte Tailscape est utilisé en commun à toutes les machines pour garantir un accès fluide au site.

## Architecture du projet

### BDD

- Rentrer dans la BDD tout en se connectant au serveur qui héberge cette BDD :
    
    ```
    mysql -h <nom du serveur> -u <nom de l’utilisateur> -p
    ```
    
    ```jsx
    CREATE DATABASE linux;
    ```
    
    ```jsx
    CREATE TABLES series(id_series INT AUTO_INCREMENT, name VARCHAR(100), nbr_seasons INT, nbr_episodes INT, PRIMARY KEY (id_series)) ENGINE InnoDB;
    ```
    
    ```jsx
    INSERT INTO series(name, nbr_seasons, nbr_episodes) VALUES ('Game of Thrones', 8, 73);
    ```
    
    ```jsx
    SELECT * FROM series;
    ```
    

### Back (API)

- Installer fast-api :
    
    ```
    pip3 install fast-api
    ```
    
- Installer un package permettant d’utiliser les BDD :
    
    ```
    pip3 install databases
    ```
    
- Lancer le fichier python, donc l’API :
    
    ```
    python3 -m uvicorn main:app —reload
    ```
    

### Front

- Installation PHP sur la vm
    
    ```
    sudo apt update
    sudo apt install php
    ```
    
- Ouverture d’un terminal (machine hélène)
- Copier le fichier sur la vm avec scp (dans `var/www/html`)
    
    ```
    scp /Users/travail/Desktop/AddSeries.php root@100.114.70.103:/var/www/html
    ```
    
- Dans un autre terminal : connexion VM en root
    
    ```
    ssh root@100.114.70.103
    ```
    
- aller dans le dossier `var/www/html` et vérifier avec `ls` que le fichier a bien été copié
    
    ![Capture d’écran 2024-01-26 à 14.19.14.png](Documentation%20Archi%20Linux%203ecd398226ca456189a1e708f48b1558/Capture_decran_2024-01-26_a_14.19.14.png)
    
- Adresse dans le navigateur pour la vue : [http://100.114.70.103/AddSeries.php](http://100.114.70.103/AddSeries.php)

## Contributeurs

- Fabien ARNAL
- Hélène GUILLEMINOT
- Hywel BONNEFOND
- Julien QUATRAVAUX

## Annexes

### Liens

| ssh | https://www.ionos.fr/assistance/serveurs-et-cloud/serveur-dedie-pour-les-serveurs-achetes-avant-le-28102018/reseau/modifier-ou-ajouter-une-adresse-ipv4-a-un-serveur-dedie-linux/ |
| --- | --- |
| tailscale | https://tailscale.com/download/linux |
| mariaDB tuto | https://www.youtube.com/watch?v=0a6ElOkgC94 |

---
