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

On fait le choix de rediriger 5% des utilisateurs vers la nouvelle application.

## Modification des fichiers dans un container multi-stage

### Création des versions

Dans un premier temps, il faut créer deux versions d'un service. Nous avons choisi de modifier le front (et donc le service nginx). Le dossier front a été dupliqué pour avoir un dossier v1 et un dossier v2. Nous avons modifié la page de connexion (première page du site internet) pour visualiser la version courante.

### Mise à jour des containers

Etant donné que nginx récupère les fichiers du front, nous avons modifié le dockerfile du serveur web. Nous avons créer deux images du front (une pour chaque version), dans lesquels nous faisons un build pour obtenir les fichiers statiques du front. Nous avons créé deux étapes, une pour chaque version, renommé v1 et v2. Nous avons modifié l'étape de la construction de l'image nginx, pour copier les fichiers de l'image v1 dans le dossier /usr/share/nginx/html1 et ceux de l'image v2 dans le dossier /usr/share/nginx/html2. De cette manière nous avons un dossier par version. En tentant de créer le container, nous avions des erreurs à la création des images de version du front, indiquant qu'il ne connaissait pas les fichiers à copier indiqués, sans comprendre la raison réelle. Après avoir essayé différents chemins sans succès, nous avons modifié notre architecture. Au lieu d'avoir un dossier front avec un dossier v1 et v2, nous avons supprimé le dossier front et l'avons remplacé par un dossier v1 et v2. De cette manière nous arrivions à créer le container du serveur. 

### Mise à jour des redirections

Nous devons ensuite gérer les redirections. Pour cela, nous allons modifier le fichier de configuration du serveur web, nginx.conf. Premièrement, nous utilisons la directive `split_clients` de Nginx. Elle permet de diviser les utilisateurs selon une clé de répartition et de stocker le résultat de la répartition dans une variable. La syntaxe générale de split_clients est `split_clients key $variable {...}`, où key est la clé utilisée pour calculer la répartition et $variable est la variable dans laquelle le résultat de la répartition sera stocké. Nous souhaitons les diviser selon leur adresse IP, ce sera donc la clé et la variable s'appellera "variant". Dans le bloc de code, nous allons introduire les règles de répartition. Nous souhaitons que 5% des utilisateurs aient la version 2 et le reste la version 1. La syntaxe pour les règles est la suivante : `proportion valeur`;
Dans notre situation nous aurons 5% d'utilisateurs sur le variant 1 et 95% sur le variant 2. Nous avons donc le bloc :
`5% 1;
95% 2;`
A la place du 95%, nous pouvons utiliser le wildcard "*" signifiant "le reste des utilisateurs". 
Pour la clé de répartition, n'utilisant qu'un ordinateur, nous l'avons modifié pour voir les changements. Nous prenons en compte l'adesse IP, l'agent utilisateur HTTP et la date. Nous avons donc cette commande : 
```
split_clients "app${remote_addr}${http_user_agent}${date_gmt}" $variant {
        50% "1";
        * "2";
}
```

### Intégration de la persistance des données

## Modification des fichiers d'un container simple
Ici, nous créons une version différente d'un container, nous choisissons toujours le front (pour voir le changement rapidement) mais cette fois en mode développement quand le front n'est pas contenu dans nginx.

### Création des versions
Nous conservons les deux dossiers v1 et v2 précédent. On va cependant modifier chaque dockerfile pour prendre les fichiers du dossier correspondant.

### Mise à jour des containers
Nous allons ensuite modifier le docker-compose, pour créer un container par version, 

# Fuzzing
