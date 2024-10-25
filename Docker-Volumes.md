# Documentation & Cheat sheet Docker

## Introduction aux Volumes Docker

Un **volume Docker** est un mécanisme de stockage qui permet de persister les données générées et utilisées par les containers, **même après leur arrêt ou leur suppression**. Contrairement au système de fichiers interne d’un container, **les volumes sont indépendants** du cycle de vie des containers et peuvent être réutilisés par d'autres containers. **Les volumes sont très utiles pour stocker des bases de données, des logs, des fichiers partagés ou tout autre type de données** qui doivent persister au-delà de l'existence d'un container.

Docker propose **trois types** principaux de volumes :

- **Volumes Docker** (volumes) : Ils sont gérés par Docker et stockés à un emplacement sur l'hôte. Ces volumes sont idéaux pour partager des données entre plusieurs containers ou pour permettre des backups automatiques.
- **Bind mounts** : Ils lient un répertoire ou fichier spécifique de l’hôte directement dans le container. Cela permet de partager des fichiers locaux, mais offre moins d’isolation et de sécurité que les volumes Docker gérés.
- **TMPFS** : Ces volumes sont stockés en mémoire et ne sont pas persistés sur le disque. Ils sont utilisés pour des données temporaires ou sensibles qui doivent disparaître après l’arrêt du container.

##### L'UserId avec les volumes :

L'importance de l'UID (User ID) lors de l’utilisation des volumes réside dans le fait que les fichiers à l’intérieur du volume doivent être accessibles par le bon utilisateur. Si l’UID de l’utilisateur à l’intérieur du container ne correspond pas à celui de l’utilisateur propriétaire des fichiers sur l’hôte, cela peut entraîner des problèmes de permissions et d'accès. Il est donc essentiel d’adapter ou de configurer correctement l’UID dans le container pour garantir que les fichiers stockés dans les volumes soient accessibles et modifiables comme prévu.

### Gestion des Volumes Docker
1. **``docker volume create Vnginx``**

    - **docker volume create** : Crée un volume Docker. Les volumes sont utilisés pour stocker des données persistantes indépendamment du cycle de vie des containers.
    - **Vnginx** : C'est le nom donné au volume. Ce volume sera utilisé pour partager des données entre le container et l'hôte.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes1.png)

2. **``docker volume ls``**

    - **docker volume ls** : Liste tous les volumes existants sur le système Docker, qu'ils soient utilisés ou non.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes2.png)

3. **Lancement du container avec un volume** :

**``docker run -d -- hostname -v Vnginx:/usr/share/nginx/html/ --name Container1 debian:latest``**

- **-d** : Lance le container en arrière-plan (mode détaché).
- **--name** : Définit le nom d'hôte pour le container.
- **-v Vnginx:/usr/share/nginx/html/** : Monte le volume Vnginx dans le container à l'emplacement /usr/share/nginx/html/. Cela permet de partager les fichiers entre l'hôte et le container à cet emplacement spécifique.
- **--name Container1** : Donne un nom au container (Container1).
- **debian:latest** : Image Debian utilisée pour créer le container.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes3.png)

4. **Accéder au container via Bash** :

**``docker exec -ti c1 bash``**

- **docker exec** : Permet d'exécuter une commande dans un container en cours d'exécution.
- **-ti** : Lance une session interactive avec un pseudo-terminal.
- **Container1** : Nom du container dans lequel la commande sera exécutée.
- **bash** : Ouvre un shell Bash interactif dans le container.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes4.png)

Nous constatons que nous pouvons naviguer à l'intérieur du "Container1".

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes5.png)

Nous vérifions le contenu du fichier "index.html" avant de montrer le lien avec le volume après modification du fichier.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes6.png)

Nous inspectons le contenu du volume avec **``docker volume inspect Vnginx``** et vérifions où se trouve le point de montage.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes7.png)

Ensuite, passons à la suite.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes8.png)

Maintenant, modifions le "Container1".

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes9.png)

Regardons dans le volume si la modification a bien été effectuée.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes10.png)

Si nous créons un nouveau container, il devrait normalement récupérer le contenu du volume.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes11.png)

5. **Suppression du volume** :

**``docker volume rm Vnginx``**

- **docker volume rm** : Supprime un volume Docker.
- **Vnginx** : Nom du volume à supprimer. Si ce volume est encore utilisé par un container, la commande échouera à moins de forcer la suppression.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Volumes12.png)

### Gestion des Volumes avec Bind Mounts
1. **Bind Mount** :

**``docker run -d --hostname --mount type=bind,src=/data/,destination=/usr/share/nginx/html/ --name Container1 debian:latest``**

- **--mount** : Utilisé pour spécifier un montage. Ici, nous utilisons un bind mount pour lier un répertoire de l'hôte au container.
- **type=bind** : Spécifie que c'est un montage de type bind mount.
- **src=/data/** : Chemin du répertoire source sur l'hôte qui sera lié au container.
- **destination=/usr/share/nginx/html/** : Chemin cible dans le container où le répertoire source sera monté.

Avec un **bind mount**, les modifications dans le répertoire /data/ sur l'hôte se répercuteront directement dans le container, et inversement.

Exemple :

Nous créons notre container comme mentionné ci-dessus, après avoir préalablement créé le dossier data (``mkdir data``) et le fichier test (``touch test``) à l'intérieur.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typemount1.png)

Maintenant, nous entrons dans le container et constatons que le fichier test s'y trouve.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume2.png)

On peut vérifier le point de montage du volume comme suit.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typemount3.png)

#### Volume Mount

Premièrement, nous créons le volume comme mentionné plus haut dans la documentation.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume1.png)

Création du container avec pratiquement la même syntaxe que pour le volume de type bind mount.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume2.png)

Nous entrons dans le container et vérifions le contenu du fichier.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume3.png)

Nous regardons où se trouve le point de montage du volume sur l'hôte.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume4.png)

Constatons que le contenu a été enregistré dans le volume.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typevolume5.png)

#### TMPFS Mount
1. **TMPFS** :

**``docker run -d --hostname --mount type=tmpfs,destination=/usr/share/nginx/html/ --name Container1 debian:latest``**

- **type=tmpfs** : Utilise un montage de type TMPFS. TMPFS monte un espace en mémoire temporaire, ce qui signifie que les données ne sont pas persistées et seront perdues lorsque le container sera arrêté.
- **destination=/usr/share/nginx/html/** : Chemin dans le container où le système TMPFS sera monté.

Le **TMPFS** est souvent utilisé pour des besoins de stockage temporaire ou pour des performances accrues, car il évite les écritures disque.

Exemple : 

Nous créons notre container.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typetmpfs1.png)

Nous entrons dans le container et ajoutons test2 dans le fichier index.html, puis nous sortons du container et le supprimons.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typetmpfs2.png)

Nous recommençons et constatons que rien n'a été gardé en mémoire.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-typetmpfs3.png)

#### Gestion des Utilisateurs dans Docker

##### 1. **Création d’utilisateurs** :

**``useradd -u 1111 toto``**
**``useradd -u 1112 toto2``**

- **useradd** : Commande Linux pour créer un nouvel utilisateur.
- **-u 1111** : Spécifie l’UID (User ID) pour l’utilisateur. Dans ce cas, l'utilisateur toto aura l'UID 1111.

Ces utilisateurs seront créés dans le container si cette commande est incluse dans le Dockerfile.
##### 2. **Construction de l'image Docker** :

Après avoir créé deux utilisateurs sur l'hôte (toto et toto2), je crée un dossier nommé "**testdocker**" et, dans ce dossier, je crée un fichier "**Dockerfile**" en utilisant l'éditeur nano avec la commande suivante : **``nano Dockerfile``**. Le contenu de ce fichier est :

``
FROM debian:latest
RUN useradd -u 1111 toto
RUN useradd -u 1112 toto2
``

Ce Dockerfile indique que nous allons partir de l'image Debian la plus récente (debian
) et créer deux utilisateurs avec des UID spécifiques : toto (UID 1111) et toto2 (UID 1112).

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user1.png)

Ensuite, je me rends dans le dossier testdocker avec cd testdocker et effectue les manipulations décrites dans l'image ci-dessus :

1. Création de l'image Docker :

- Je construis l'image à partir du "**Dockerfile**" avec la commande **``docker build -t testimage:v1.0``**. L'option **``-t``** permet de donner un nom et une version à l'image (ici, testimage:v1.0), et le point (.) indique que le Dockerfile se trouve dans le répertoire courant.

2. Vérification de l'image :

- Je vérifie que l'image a bien été créée en utilisant la commande docker images, qui affiche toutes les images Docker présentes sur l'hôte.

3. Création d'un dossier pour le volume :

- Avant de lancer un container, je crée un dossier sur l'hôte qui sera utilisé comme volume partagé avec le conteneur : **``mkdir /testvolume``**.

4. Lancement du container :

- Je lance un container avec le volume lié à ce dossier avec la commande **``docker run -d --name container1 -v /testvolume/:/data/ testimage:v1.0``**. L'option **``-d``** permet de lancer le container en arrière-plan (mode détaché), --name donne un nom au container (**container1**), et -v monte le dossier de l'hôte (**/testvolume/**) dans le container au chemin "**/data/**".

5. Vérification des containers en cours d'exécution :

 - Avec la commande **``docker ps``**, je m'attends à voir le container en marche. Cependant, je constate qu'aucun container n'est listé.

6. Vérification des containers arrêtés :

- J'utilise **``docker ps -a``**, qui montre tous les containers, y compris ceux qui sont arrêtés. Je remarque que le container "**container1**" existe bien mais est arrêté.

7. Suppression du container : Je décide alors de supprimer ce container en utilisant docker rm -f container1.

Dans l'image suivante, je ferai des modifications supplémentaires.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user2.png)

Dans cette étape, nous relançons la commande suivante pour démarrer le container :

**``docker run -d --name container1 -v /testvolume/:/data/ testimage:v1.0 sleep infinity``**

Cette fois-ci, nous ajoutons l'option "**sleep infinity**". Cela permet de maintenir le container actif indéfiniment en forçant le processus à « dormir » de façon continue, sans s'arrêter. Cela est utile pour empêcher le container de se fermer immédiatement après son lancement, puisqu'il n'y a pas de processus actif par défaut. Ainsi, tant que le container "dort", il reste en fonctionnement.

1. Vérification du conteneur actif :

- Avec la commande docker ps, nous pouvons maintenant voir que le container "**container1**" est bien actif.

2. Accès au container :

- Nous accédons à l'intérieur du container en utilisant la commande **``docker exec -ti container1 bash``**.
- L'option **``-ti``** permet d'ouvrir un terminal interactif dans le conteneur
-  et **``bash``** ouvre un shell Bash à l'intérieur du conteneur.

3. Création d'un fichier dans le container :

- Une fois dans le container, nous créons un fichier nommé "**test**" dans le répertoire monté en volume "**/data**" avec la commande **``touch /data/test``**.

4. Vérification des droits sur l'hôte :

- Nous quittons le container "**exit**" et revenons sur l'hôte.
- Ensuite, nous vérifions les droits du fichier créé dans le volume partagé avec la commande **``ls -la /testvolume``**.

5. Résultat :

- Nous remarquons que le propriétaire du fichier "**test**" est "**root**". Cela s'explique par le fait que nous avons lancé le container sans spécifier d'utilisateur particulier. Par défaut, **Docker** exécute les processus en tant qu'utilisateur "**root**" à l'intérieur du container, ce qui explique pourquoi le fichier créé a "**root**" comme propriétaire.

#### 3. **Lancement d’un container avec utilisateur spécifique** :

Dans cette étape, nous relançons la commande suivante, cette fois en spécifiant un utilisateur :

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user3.png)

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user4.png)

**``docker run -d --name container1 -v /testvolume/:/data/ -u toto testimage:v1.0 sleep infinity``**

rappel:
- **-u toto** : Spécifie que le container sera lancé sous l’utilisateur "**toto**". L'utilisateur doit exister dans l'image Docker.
- **-v /testvolume/:/data/** : Monte le volume **/testvolume/** de l’hôte dans le répertoire **/data/** du container.
- **sleep infinity** : Maintient le container en marche indéfiniment pour permettre des interactions ultérieures.

L'option -u toto permet de spécifier que le container doit être exécuté avec l'utilisateur toto (précédemment créé dans l'image) plutôt que l'utilisateur "**root**" par défaut.

1. Vérification de l'utilisateur en cours d'exécution :

- Nous utilisons la commande **``ps aux | grep sleep``** pour vérifier quel utilisateur exécute le processus sleep à l'intérieur du container. Cette commande liste tous les processus actifs, et "**``grep``**" filtre ceux contenant le mot "**sleep**". Nous constatons que l'utilisateur "**toto**" est bien celui qui exécute le processus.

2. Accès au container :

- Ensuite, nous accédons au container avec la commande **``docker exec -ti container1 bash``**. En ouvrant un shell Bash à l'intérieur du conteneur, nous remarquons que nous sommes connectés en tant qu'utilisateur "**toto**" et non plus en "**root**".

3. Vérification du fichier créé précédemment :

- Nous nous rendons dans le dossier "**/data/**" où le volume est monté, en utilisant la commande **``cd /data/``**. Ensuite, nous listons le contenu du répertoire avec **``ls``** et remarquons que le fichier "**test**" créé précédemment (lorsque nous étions connectés en tant que root) est toujours présent.

#### 4. Tentative de création d'un fichier :

- Nous essayons de créer un nouveau fichier avec la commande **``touch test2``**, mais nous recevons un message d'erreur indiquant que nous n'avons pas les permissions.

    **Pourquoi n'avons-nous pas les permissions ?**

- Le fichier "**test**" a été créé par l'utilisateur "**root**" lorsque nous avons exécuté le container précédemment sans spécifier d'utilisateur. Étant donné que les permissions du dossier "**/data/**" et du fichier "**test**" sont attribuées à "**root**", l'utilisateur "**toto**" (qui a des privilèges limités) n'a pas le droit de modifier ou d'ajouter des fichiers dans ce répertoire, sauf si les permissions sont modifiées pour permettre l'accès à d'autres utilisateurs.

 **Lancement avec ID d'utilisateur spécifique** :

Nous pouvons spécifier l'UID au lieu du nom d'utilisateur.

**``docker run -d --name c1 -u 1111 -v /testvolume/:/data/ testimage:v1.0 sleep infinity``**

- **-u 1111** : Spécifie l'UID 1111. Cela permet d'exécuter le container sous l'utilisateur ayant cet UID.

#### 5. Seconde tentative de création d'un fichier :

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user6.png)

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user7.png)

Ici, nous modifions le propriétaire du volume partagé en utilisant la commande suivante sur l'hôte :

**``chown toto /testvolume/``**

Cette commande change le propriétaire du répertoire "**/testvolume/**" en "**toto**", ce qui permettra à cet utilisateur d'avoir les droits nécessaires pour modifier le contenu du répertoire.

1. Vérification des permissions :

- Nous confirmons que le changement de propriétaire a bien été pris en compte avec la commande **``ls -la /testvolume``**. Cette commande affiche les permissions détaillées des fichiers et dossiers. Nous pouvons constater que "**toto**" est maintenant affiché comme propriétaire du dossier "**/testvolume**".

2. Retour dans le container :

- Ensuite, nous retournons dans le container avec la commande **``docker exec -ti container1 bash``** pour ouvrir un shell Bash en tant qu'utilisateur "**toto**".

3. Création d'un fichier :

- Une fois dans le container, nous nous déplaçons dans le répertoire monté "**/data/**" avec la commande **``cd /data/``**. Comme ce répertoire correspond à "**/testvolume**" sur l'hôte, les permissions modifiées devraient permettre à "**toto**" de créer un fichier. Nous créons alors un fichier nommé "**test2**" avec la commande **``touch test2``**.

4. Vérification des droits sur l'hôte :

- Nous quittons le container (exit) et revenons sur l'hôte pour vérifier à nouveau les permissions du répertoire "**/testvolume**" avec la commande **``ls -la /testvolume``**. Nous constatons que le fichier "**test2**" est bien présent dans le répertoire, et qu'il appartient à "**toto**", l'utilisateur qui l'a créé dans le container.

#### 6. Connexion avec un utilisateur inconnu de l'hote :

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user8.png)

Dans cette image, nous testons le lancement d’un container avec un utilisateur nommé "**toto3**", qui n'existe pas sur l'hôte :

**``docker run -d --name container1 -v /testvolume/:/data/ -u toto3 testimage:v1.0 sleep infinity``**

1. Connexion au container :

- Nous accédons au container avec **``docker exec -ti container1 bash``** et voyons que nous sommes connectés en tant qu’utilisateur "**toto3**".

2. Tentative de création de fichier :

- Nous essayons de créer un fichier dans le répertoire monté avec **``touch /data/test3``**, mais nous rencontrons une erreur "**Permission denied**". Cela se produit car l’hôte ne reconnaît pas cet utilisateur "**toto3**", et par conséquent, les permissions sur le dossier "**/testvolume**" ne lui permettent pas de créer des fichiers.

3. Modification des permissions pour autoriser l'accès :

- Pour qu’un utilisateur inexistant sur l’hôte puisse écrire dans le répertoire, nous devons ajuster les permissions. Dans cet exemple, j'ai utilisé la commande suivante pour attribuer des permissions complètes :

**``chmod 777 /testvolume``**

Cela attribue "**rwx**" (lecture, écriture, exécution) aux catégories utilisateur, groupe, et autres (user, group, other). Bien que cela fonctionne, il est fortement déconseillé de donner des permissions "**777**" pour des raisons de sécurité.

4. Création du fichier après modification des permissions :

- Avec ces permissions, nous constatons que "**toto3**" peut maintenant créer un fichier dans "**/data**". Nous créons "**test3**" sans rencontrer d'erreur, prouvant ainsi que les permissions élargies permettent à des utilisateurs sans correspondance sur l'hôte d’écrire dans le répertoire partagé.


#### 7. **Changement temporaire d’utilisateur via docker exec** :

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-user9.png)

Sur cette dernière image, nous voyons que nous pouvons nous connecter directement au conteneur en utilisant l’utilisateur existant ""**toto**" :

**``docker exec -ti -u toto container1 bash``**

1. Connexion avec un utilisateur spécifique :

- Avec l'option "**-u toto**", nous accédons au container en tant qu’utilisateur "**toto**" (plutôt que root par défaut). Cela nous permet d’utiliser cet utilisateur pour exécuter des commandes directement dans le container.

2. Observation des permissions après déconnexion :

- Après avoir quitté le container, si nous vérifions les permissions sur les fichiers dans le volume monté (par exemple, avec **``ls -la /testvolume``**), nous remarquons que les fichiers créés par "**toto3**" apparaissent avec **l’UID 1113** et non avec le nom "**toto3**".

- Explication : Cela se produit parce que l’utilisateur toto3 n’existe pas sur l’hôte. Les utilisateurs sont identifiés par leur UID (User ID) et non par leur nom. Le conteneur connaît toto3 avec l'UID 1113, mais l’hôte, n'ayant pas cet utilisateur, affiche uniquement l'UID (1113) au lieu d'un nom d'utilisateur.

![Texte alternatif](/chemin/access/image.jpg "Titre de l'image")

