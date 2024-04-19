# Introduction
Nous avons choisi de travailler sur le projet dockerisé suivant : [Site internet](https://github.com/SterennLeHir/Website). Il correspond à un site Internet recensant des associations rennaises avec leurs informations telles que les membres, les comptes rendus, etc.
Ce projet suit une architecture de micro-services (en mode production) avec : 

- une base de données
- un service du back-end
- un service de reverse-proxy (nginx) contenant les fichiers statiques du front-end

# Canary Testing

Le canary testing permet le déploiement d'une version mise à jour du projet à une petite proportion des utilisateurs finaux. Cela permet de tester la dernière version de notre application dans un réel environnement de production, tout en réduisant les risques car peu d'utilisateurs seront touchés.

Pour implémenter le canary testing dans notre application, nous aurons besoin de :

- la version d'origine de l'application
- la nouvelle version de l'application : dans notre cas on modifiera le service front uniquement
- une redirection vers ces applications : notre serveur nginx redirige les utilisateurs soit vers l'ancienne soit vers la nouvelle application

On fait le choix de rediriger 10% des utilisateurs vers la nouvelle application.

## Modification des fichiers dans un container multi-stage

### Création des versions

Dans un premier temps, il faut créer deux versions d'un service. Nous avons choisi de modifier le front (et donc le service nginx). Le dossier front a été dupliqué pour avoir un dossier v1 et un dossier v2. Nous avons modifié la page de connexion (première page du site internet) pour visualiser la version courante.

### Mise à jour des containers

Etant donné que nginx récupère les fichiers du front, nous avons modifié le dockerfile du serveur web. Nous avons créer deux images du front (une pour chaque version), dans lesquels nous faisons un build pour obtenir les fichiers statiques du front. Nous avons créé deux étapes, une pour chaque version, renommé v1 et v2. Nous avons modifié l'étape de la construction de l'image nginx, pour copier les fichiers de l'image v1 dans le dossier /usr/share/nginx/html1 et ceux de l'image v2 dans le dossier /usr/share/nginx/html2. De cette manière nous avons un dossier par version. 

### Mise à jour des redirections

### Intégration de la persistance des données

## Modification des fichiers d'un container simple
Ici, nous créons une version différente d'un container, nous choisissons toujours le front (pour voir le changement rapidement) mais cette fois en mode développement quand le front n'est pas contenu dans nginx.

### Création des versions
Nous conservons les deux dossiers v1 et v2 précédent. On va cependant modifier chaque dockerfile pour prendre les fichiers du dossier correspondant.

### Mise à jour des containers
Nous allons ensuite modifier le docker-compose, pour créer un container par version, 
# Fuzzing
