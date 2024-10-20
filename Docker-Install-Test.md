# Documentation & Cheat sheet Docker

### 1. Introduction 

**Docker** est une plateforme de virtualisation légère qui permet de créer, déployer et exécuter des applications dans des **conteneurs**. Contrairement aux machines virtuelles traditionnelles, les **conteneurs Docker** partagent le même noyau du système hôte, ce qui les rend plus légers et rapides à démarrer. Il est largement utilisé pour le développement, les tests et le déploiement d'applications dans des environnements standardisés. Les **avantages** incluent la **portabilité des applications**, la **gestion simplifiée des dépendances**, et la possibilité de faire **fonctionner plusieurs instances** d'une même application **sans conflits**.

### 2. Installation Docker sur Debian


##### Suppression de la précédente version de Docker si il y a:

``sudo apt remove docker docker-engine docker.io containerd runc``

Cette commande permet de supprimer toute version antérieure de Docker, ainsi que ses composants associés tels que **docker-engine**, **docker.io**, **containerd**, **et runc**. Cela garantit que vous partez d'une installation propre avant de mettre en place la nouvelle version de Docker.

- **docker-engine** : C'est l'ancien nom du moteur Docker. Il s'agissait du logiciel principal permettant de créer et de gérer des conteneurs sur un système. Depuis, Docker a été scindé en plusieurs composants, et docker-engine est obsolète dans les versions récentes de Docker. La suppression de ce package garantit que d'anciennes versions du moteur Docker ne provoquent pas de conflits.

- **docker.io** : Il s'agit du package Docker disponible dans les dépôts par défaut d'Ubuntu et Debian. Il fournit une version de Docker, mais elle peut être obsolète par rapport à la version officielle disponible sur le dépôt Docker lui-même. La suppression de ce package assure qu'il n'y ait pas de confusion entre différentes sources de Docker lors de l'installation d'une nouvelle version.

- **containerd** : C'est un runtime de conteneur indépendant de Docker qui gère l'exécution et le cycle de vie des conteneurs. Il s'agit d'une couche sous-jacente essentielle dans Docker pour la gestion des conteneurs. La suppression de containerd permet de s'assurer qu'aucune ancienne version de ce runtime ne reste active lors de la mise à jour de Docker.

- **runc** : C'est un outil en ligne de commande qui exécute les conteneurs spécifiés au format OCI (Open Container Initiative). runc est utilisé par containerd pour créer et exécuter les conteneurs sur le système. Le retirer permet d'éliminer toute version obsolète de cet outil, afin que la nouvelle installation de Docker utilise la version correcte et à jour de runc.

Ces composants font partie de l'écosystème de Docker et sont tous impliqués dans la gestion des conteneurs. La suppression des versions précédentes permet d'éviter les conflits et les incohérences lors de l'installation d'une nouvelle version complète de Docker.

##### Mise en place du dépôt Docker

1. **Installation des dépendances nécessaires** :

``sudo apt install ca-certificates curl gnupg lsb-release``

Cette commande installe les **certificats CA** pour sécuriser les connexions, **curl** pour télécharger des fichiers depuis Internet, **gnupg** pour la gestion des clés GPG (nécessaire pour vérifier les sources logicielles) et **lsb-release** pour obtenir des informations sur la version de l'OS.

2. **Création du répertoire pour les clés** :

``sudo mkdir -m 0755 -p /etc/apt/keyrings``

Cette commande crée un répertoire sécurisé (0755 pour les permissions) où sera stockée la clé GPG de Docker.

3. **Ajout de la clé GPG Docker** :

``curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg``

Ici, **curl** télécharge la clé GPG du dépôt Docker, et la commande **gpg --dearmor** la convertit au format binaire, puis elle est enregistrée dans le répertoire **/etc/apt/keyrings/** pour sécuriser les futures mises à jour du dépôt Docker.

4. **Ajout du dépôt Docker dans les sources APT** :

``echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null``

Cette commande configure le système pour utiliser le dépôt officiel Docker en spécifiant l'architecture de votre machine ($(dpkg --print-architecture) renvoie par exemple amd64) et en sécurisant les paquets avec la clé GPG. Le dépôt est ajouté dans la liste des sources d'APT, ce qui permet d'installer Docker directement via le gestionnaire de paquets APT.

##### Installation de docker

``sudo apt update``
``sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin``

Mise à jour de la liste des paquets : **sudo apt update** télécharge les informations sur les paquets disponibles dans les dépôts, y compris celui de Docker récemment ajouté.

Installation de Docker : Cette commande installe les composants suivants :

- **docker-ce** : le moteur de conteneur Docker.
- **docker-ce-cli** : l'interface en ligne de commande pour interagir avec Docker.
- **containerd.io** : le runtime de conteneur qui gère l'exécution des conteneurs.
- **docker-buildx-plugin** : le plugin pour construire des images Docker avec des fonctionnalités avancées.
- **docker-compose-plugin** : le plugin qui permet d’utiliser Docker Compose pour gérer des applications multi-conteneurs.

### Premier pas 

**Afficher les conteneurs actifs** :

``docker ps``

Cette commande liste tous les conteneurs Docker en cours d'exécution sur votre système.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-FirstDoc1.png)

**Exécuter un conteneur NGINX** :

``docker run nginx:latest``

Cette commande télécharge et exécute une instance du serveur web NGINX à partir de l'image nginx:latest. Si l'image n'est pas présente localement, elle sera automatiquement téléchargée depuis Docker Hub.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-FirstDoc2.png)

**Afficher tous les conteneurs (actifs et inactifs)** :

``docker ps -a``

Cela affiche tous les conteneurs, qu'ils soient en cours d'exécution ou arrêtés, ainsi que leur état (ex. : "Exited" ou "Up").

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-FirstDoc3.png)

**Exécuter NGINX en arrière-plan (mode détaché)** :

``docker run -d nginx:latest``

Le flag -d lance le conteneur en arrière-plan, ce qui permet de continuer à utiliser le terminal sans être bloqué par le processus NGINX en cours d'exécution.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-FirstDoc4.png)

