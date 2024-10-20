# Documentation & Cheat sheet Docker

### 1. Manipuler

**``usermod -aG docker $USER``**

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation1.png)

- **usermod** : C'est une commande Linux utilisée pour modifier les propriétés d'un utilisateur du système.
- **-aG** : Ce sont deux options combinées :
        **-a** : "Append" (ajouter) l'utilisateur à un groupe. Cette option est essentielle car sans elle, l'utilisateur serait retiré des autres groupes et uniquement ajouté au groupe spécifié.
        **-G** : Spécifie le groupe auquel l'utilisateur doit être ajouté. Ici, le groupe cible est docker.
    docker : C'est le groupe de sécurité utilisé pour accorder l'accès à Docker. Les utilisateurs faisant partie de ce groupe peuvent exécuter des commandes Docker sans avoir besoin d'utiliser sudo.
- **$USER** : C'est une variable d'environnement qui fait référence à l'utilisateur actuellement connecté. Cette variable est automatiquement remplacée par le nom de l'utilisateur en cours d'exécution.

Cette commande ajoute l'utilisateur actuel (représenté par $USER) au groupe docker. Une fois dans ce groupe, l'utilisateur pourra exécuter des commandes Docker sans avoir besoin de privilèges administratifs (sans utiliser sudo). Cela simplifie grandement l'utilisation de Docker.

**``docker --help``**

- **docker** : C'est la commande principale pour interagir avec Docker. Elle permet de gérer des images, des conteneurs, des réseaux et des volumes.
- **--help** : Il liste les sous-commandes disponibles, ainsi que des descriptions de ce que fait chaque sous-commande.


### 2. Quelques manipulations

1. **``docker run -ti debian:latest``**

    - **docker run** : Cette commande lance un nouveau conteneur basé sur une image Docker.
    - **-ti** : Ce sont deux flags combinés :
    - **-t** : Attribue un pseudo-TTY (terminal), ce qui permet de se connecter de manière interactive au conteneur.
     - **-i** : Maintient l’entrée standard (stdin) ouverte, même si le conteneur n'est pas connecté à un terminal. Cela permet de garder la session interactive ouverte pour des interactions directes dans le conteneur.
    - **debian:latest** : Il s'agit de l'image Docker utilisée pour créer le conteneur. L'image 
    debian:latest fait référence à la dernière version stable de Debian. Si elle n'est pas présente localement, Docker va la télécharger depuis Docker Hub.

Cette commande te permet donc de lancer un nouveau conteneur Debian et d'y accéder en mode interactif pour exécuter des commandes directement dans le conteneur.(root@<id du conteneur>)

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Manipulation3.png)

2. **``docker run -ti --hostname Contain2 debian:latest``**

    - **--hostname Contain2** : Ce flag permet de définir le nom d'hôte (hostname) du conteneur à Contain2. Cela modifie le nom que le conteneur utilisera pour s'identifier sur le réseau ou dans le système.

La commande lance un conteneur Debian en mode interactif, comme la commande précédente, mais avec le nom d’hôte défini comme Contain2 à l'intérieur du conteneur. Lorsque tu ouvriras une session dans ce conteneur, l'invite de commande affichera Contain2 comme nom de la machine.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation2.png)

3. **``docker run -ti --rm --hostname Contain2 debian:latest``**

    - **--rm** : Ce flag permet de supprimer automatiquement le conteneur après son arrêt. Cela évite d'accumuler des conteneurs "zombies" inutilisés sur le système. C’est utile pour les tests ou les conteneurs éphémères qui ne nécessitent pas d'être conservés une fois arrêtés.

Cette commande lance un conteneur Debian avec le nom d'hôte Contain2, en mode interactif, et une fois que tu quitteras le conteneur ou que celui-ci sera arrêté, Docker supprimera automatiquement le conteneur.
On peut voir dans l'image suivante qu'il n'apparait plus dans la liste de conteneur présent.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/docker-Manipulation4.png)

4. **``docker stop Contain1``**

    - **docker stop** : Cette commande arrête un conteneur en cours d'exécution de manière "gracieuse". Elle envoie un signal SIGTERM au processus principal du conteneur, lui permettant de se terminer correctement avant l'arrêt.
    - **Contain1** : Nom ou ID du conteneur que tu souhaites arrêter. Ici, le conteneur nommé Contain1 sera arrêté.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation5.png)

5. **``docker start Contain1``**

    - **docker start** : Cette commande redémarre un conteneur qui a été précédemment arrêté. Elle ne recrée pas le conteneur, mais relance celui qui a été mis en pause.
    - **Contain1** : Nom ou ID du conteneur à démarrer. Cela redémarre le conteneur Contain1 que tu avais arrêté précédemment.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation6.png)

6. **``docker rm -f Contain1``**

    - **docker rm** : Cette commande supprime un ou plusieurs conteneurs arrêtés. Un conteneur ne peut pas être supprimé s'il est encore en cours d'exécution, sauf si on utilise un flag spécifique.
    - **-f** : Ce flag force la suppression du conteneur, même s'il est en cours d'exécution. Cela envoie d'abord un signal pour arrêter le conteneur, puis le supprime immédiatement.
    Contain1 : Nom ou ID du conteneur que tu veux forcer à arrêter et supprimer. Ici, le conteneur Contain1 sera arrêté (s’il tourne encore) puis supprimé.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation7.png)

7. **``docker ps -q`` & ``docker ps -qa``**

    - **docker ps -q** : Cette commande imbriquée récupère la liste des IDs des conteneurs actuellement en cours d'exécution (le flag -q de docker ps retourne uniquement les IDs, sans autres informations).

    - **docker ps -qa** : Cette commande imbriquée récupère la liste des IDs de tous les conteneurs (en cours d'exécution ou arrêtés). Le flag -a de docker ps liste tous les conteneurs, et -q filtre pour ne retourner que leurs IDs.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation8.png)

9. **``docker rm -f $(docker ps -q)``**

    - **docker rm -f $(docker ps -q)** : Supprime tous les conteneurs en cours d'exécution de manière forcée, en récupérant leurs IDs avec docker ps -q, puis en les passant à la commande docker rm -f.

![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation9.png)

9. **``docker rm -f $(docker ps -qa)``**

    - **docker rm -f $(docker ps -qa**) : Supprime de manière forcée tous les conteneurs sur le système, qu'ils soient en cours d'exécution ou arrêtés. C’est une commande puissante qui permet de faire le ménage complet en supprimant tous les conteneurs en une seule opération.


![](https://github.com/Shanks69000/Docker-Doc/blob/main/img-Docker/Docker-Manipulation10.png)

