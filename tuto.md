# Introduction
Nous avons choisi de travailler sur le projet dockerisé suivant : ![Site internet](https://github.com/SterennLeHir/Website). Il correspond à un site Internet recensant des associations rennaises avec leurs informations telles que les membres, les comptes rendus, etc.
Ce projet suit une architecture de micro-services (en mode production) avec : 

- une base de données
- un service du back-end
- un service de reverse-proxy (nginx) contenant les fichiers statiques du front-end

# Canary Testing

Pour le canary testing, on souhaite diviser les utilisateurs en plusieurs groupes. Chaque groupe va être dirigé sur une version particulière du projet.

## Modification des fichiers d'une image incluse dans une autre

### Création des versions

Dans un premier temps, il faut créer deux versions d'un service. Nous avons choisi de modifier le front (et donc le service nginx). Le dossier front a été dupliqué pour avoir un dossier v1 et un dossier v2. Nous avons modifié la page de connexion (première page du site internet) pour visualiser la version courante.

### Mise à jour des containers
Ensuite, étant donné 

### Mise à jour des redirections

### Intégration de la persistance des données

## Modification des fichiers d'un container
Ici, nous créons une version différente d'un container, nous choisissons toujours le front (pour voir le changement rapidement) mais cette fois en mode développement quand le front n'est pas contenu dans nginx.

### Création des versions
Nous conservons les deux dossiers v1 et v2 précédent. On va cependant modifier chaque dockerfile pour prendre les fichiers du dossier correspondant.

### Mise à jour des containers
Nous allons ensuite modifier le docker-compose, pour créer un container par version, 
# Fuzzing
