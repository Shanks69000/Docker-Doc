# Documentation & Cheat sheet Docker

## Introduction aux Volumes Docker

Un **volume Docker** est un mécanisme de stockage qui permet de persister les données générées et utilisées par les conteneurs, **même après leur arrêt ou leur suppression**. Contrairement au système de fichiers interne d’un conteneur, **les volumes sont indépendants** du cycle de vie des conteneurs et peuvent être réutilisés par d'autres conteneurs. **Les volumes sont très utiles pour stocker des bases de données, des logs, des fichiers partagés ou tout autre type de données** qui doivent persister au-delà de l'existence d'un conteneur.

Docker propose **trois types** principaux de volumes :

- **Volumes Docker** (volumes) : Ils sont gérés par Docker et stockés à un emplacement sur l'hôte. Ces volumes sont idéaux pour partager des données entre plusieurs conteneurs ou pour permettre des backups automatiques.
- **Bind mounts** : Ils lient un répertoire ou fichier spécifique de l’hôte directement dans le conteneur. Cela permet de partager des fichiers locaux, mais offre moins d’isolation et de sécurité que les volumes Docker gérés.
- **TMPFS** : Ces volumes sont stockés en mémoire et ne sont pas persistés sur le disque. Ils sont utilisés pour des données temporaires ou sensibles qui doivent disparaître après l’arrêt du conteneur.

##### L'UserId avec les volumes :

L'importance de l'UID (User ID) lors de l’utilisation des volumes réside dans le fait que les fichiers à l’intérieur du volume doivent être accessibles par le bon utilisateur. Si l’UID de l’utilisateur à l’intérieur du conteneur ne correspond pas à celui de l’utilisateur propriétaire des fichiers sur l’hôte, cela peut entraîner des problèmes de permissions et d'accès. Il est donc essentiel d’adapter ou de configurer correctement l’UID dans le conteneur pour garantir que les fichiers stockés dans les volumes soient accessibles et modifiables comme prévu.

### Gestion des Volumes Docker
1. **``docker volume create Vnginx``**

    - **docker volume create** : Crée un volume Docker. Les volumes sont utilisés pour stocker des données persistantes indépendamment du cycle de vie des conteneurs.
    - **Vnginx** : C'est le nom donné au volume. Ce volume sera utilisé pour partager des données entre le conteneur et l'hôte.

2. **``docker volume ls``**

    - **docker volume ls** : Liste tous les volumes existants sur le système Docker, qu'ils soient utilisés ou non.

3. **Lancement du conteneur avec un volume** :

**``docker run -d --hostname -v Vnginx:/usr/share/nginx/html/ --name c1 debian:latest``**

- **-d** : Lance le conteneur en arrière-plan (mode détaché).
- **--hostname** : Définit le nom d'hôte pour le conteneur.
- **-v Vnginx:/usr/share/nginx/html/** : Monte le volume Vnginx dans le conteneur à l'emplacement /usr/share/nginx/html/. Cela permet de partager les fichiers entre l'hôte et le conteneur à cet emplacement spécifique.
- **--name Container1** : Donne un nom au conteneur (Container1).
- **debian:latest** : Image Debian utilisée pour créer le conteneur.

4. **Accéder au conteneur via Bash** :

**``docker exec -ti c1 bash``**

- **docker exec** : Permet d'exécuter une commande dans un conteneur en cours d'exécution.
- **-ti** : Lance une session interactive avec un pseudo-terminal.
- **Container1** : Nom du conteneur dans lequel la commande sera exécutée.
- **bash** : Ouvre un shell Bash interactif dans le conteneur.

5. **Suppression du volume** :

**``docker volume rm Vnginx``**

- **docker volume rm** : Supprime un volume Docker.
- **Vnginx** : Nom du volume à supprimer. Si ce volume est encore utilisé par un conteneur, la commande échouera à moins de forcer la suppression.

### Gestion des Volumes avec Bind Mounts
1. **Bind Mount** :

**``docker run -d --hostname --mount type=bind,src=/data/,destination=/usr/share/nginx/html/ --name Container1 debian:latest``**

- **--mount** : Utilisé pour spécifier un montage. Ici, nous utilisons un bind mount pour lier un répertoire de l'hôte au conteneur.
- **type=bind** : Spécifie que c'est un montage de type bind mount.
- **src=/data/** : Chemin du répertoire source sur l'hôte qui sera lié au conteneur.
- **destination=/usr/share/nginx/html/** : Chemin cible dans le conteneur où le répertoire source sera monté.

Avec un **bind mount**, les modifications dans le répertoire /data/ sur l'hôte se répercuteront directement dans le conteneur, et inversement.

#### TMPFS Mount
1. **TMPFS** :

**``docker run -d --hostname --mount type=tmpfs,destination=/usr/share/nginx/html/ --name Container1 debian:latest``**

- **type=tmpfs** : Utilise un montage de type TMPFS. TMPFS monte un espace en mémoire temporaire, ce qui signifie que les données ne sont pas persistées et seront perdues lorsque le conteneur sera arrêté.
- **destination=/usr/share/nginx/html/** : Chemin dans le conteneur où le système TMPFS sera monté.

Le **TMPFS** est souvent utilisé pour des besoins de stockage temporaire ou pour des performances accrues, car il évite les écritures disque.
Gestion des Utilisateurs dans Docker

1. **Création d’utilisateurs** :

**``useradd -u 1111 toto``**
**``useradd -u 1112 toto2``**

- **useradd** : Commande Linux pour créer un nouvel utilisateur.
- **-u 1111** : Spécifie l’UID (User ID) pour l’utilisateur. Dans ce cas, l'utilisateur toto aura l'UID 1111.

Ces utilisateurs seront créés dans le conteneur si cette commande est incluse dans le Dockerfile.
2. **Construction de l'image Docker** :

cat Dockerfile
FROM debian:latest
RUN useradd -u 1111 toto
RUN useradd -u 1112 toto2

- **FROM debian:latest** : Utilise l'image Debian de base pour créer l'image Docker personnalisée.
- **RUN useradd -u 1111 toto et RUN useradd -u 1112 toto2** : Ajoute les utilisateurs toto et toto2 avec les UID 1111 et 1112 respectivement lors de la création de l'image.

3. **Lancement d’un conteneur avec utilisateur spécifique** :

**``docker run -d --name Container1 -u toto -v /myvolume/:/data/ myowndebian:v1.0 sleep infinity``**

- **-u toto** : Spécifie que le conteneur sera lancé sous l’utilisateur xavki. L'utilisateur doit exister dans l'image Docker.
- **-v /myvolume/:/data/** : Monte le volume /myvolume/ de l’hôte dans le répertoire /data/ du conteneur.
- **sleep infinity** : Maintient le conteneur en marche indéfiniment pour permettre des interactions ultérieures.

4. **Lancement avec ID d'utilisateur spécifique** :

**``docker run -d --name c1 -u 1111 -v /myvolume/:/data/ myowndebian:v1.0 sleep infinity``**

- **-u 1111** : Spécifie l'UID 1111 (au lieu du nom d'utilisateur). Cela permet d'exécuter le conteneur sous l'utilisateur ayant cet UID.

5. **Changement temporaire d’utilisateur via docker exec** :

**``docker exec -ti toto bash``**
**``docker exec -ti toto2 bash``**

- **docker exec -ti toto bash** : Accède à un terminal Bash dans le conteneur sous l'utilisateur toto.
- **docker exec -ti toto2 bash** : Accède à un terminal Bash dans le conteneur sous l'utilisateur toto2.

Cela te permet d’interagir avec les conteneurs en utilisant différents utilisateurs, pour tester des permissions ou des configurations spécifiques.

![Texte alternatif](/chemin/access/image.jpg "Titre de l'image")
