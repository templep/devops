Authors : Alexandre Lecomte et Sterenn Le Hir

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

### Création des versions

Dans un premier temps, il faut créer deux versions d'un service. Nous avons choisi de modifier le front (et donc le service nginx). Le dossier front a été dupliqué pour avoir un dossier v1 et un dossier v2. Nous avons modifié la page de connexion (première page du site internet) pour visualiser la version courante.

### Mise à jour des containers

Etant donné que nginx récupère les fichiers du front, nous avons modifié le dockerfile du serveur web. Nous avons créer deux images du front (une pour chaque version), dans lesquels nous faisons un build pour obtenir les fichiers statiques du front. Nous avons créé deux étapes, une pour chaque version, renommé v1 et v2. Nous avons modifié l'étape de la construction de l'image nginx, pour copier les fichiers de l'image v1 dans le dossier /usr/share/nginx/html1 et ceux de l'image v2 dans le dossier /usr/share/nginx/html2. De cette manière nous avons un dossier par version. En tentant de créer le container, nous avions des erreurs à la création des images de version du front, indiquant qu'il ne connaissait pas les fichiers à copier indiqués, sans comprendre la raison réelle. Après avoir essayé différents chemins sans succès, nous avons modifié notre architecture. Au lieu d'avoir un dossier front avec un dossier v1 et v2, nous avons supprimé le dossier front et l'avons remplacé par un dossier v1 et v2. De cette manière nous arrivions à créer le container du serveur. 

### Mise à jour des redirections

Nous devons ensuite gérer les redirections. Pour cela, nous allons modifier le fichier de configuration du serveur web, nginx.conf. Premièrement, nous utilisons la directive `split_clients` de Nginx. Elle permet de diviser les utilisateurs selon une clé de répartition et de stocker le résultat de la répartition dans une variable. La syntaxe générale de split_clients est `split_clients key $variable {...}`, où key est la clé utilisée pour calculer la répartition et $variable est la variable dans laquelle le résultat de la répartition sera stocké. Nous souhaitons les diviser selon leur adresse IP, ce sera donc la clé et la variable s'appellera "variant". Dans le bloc de code, nous allons introduire les règles de répartition. Nous souhaitons que 5% des utilisateurs aient la version 2 et le reste la version 1. La syntaxe pour les règles est la suivante : `proportion valeur`;
Dans notre situation nous aurons 5% d'utilisateurs sur le variant 1 et 95% sur le variant 2. Nous avons donc le bloc :
`5% 2;
95% 1;`
A la place du 95%, nous pouvons utiliser le wildcard "*" signifiant "le reste des utilisateurs". 
Pour la clé de répartition, n'utilisant qu'un ordinateur, nous l'avons modifié pour voir les changements. Nous prenons en compte l'adesse IP, l'agent utilisateur HTTP et la date. Nous avons donc cette commande : 
```
split_clients "app${remote_addr}${http_user_agent}${date_gmt}" $variant {
        5% "2";
        * "1";
}
```
Pour nos tests, nous avons répartis équitablement les utilisateurs pour qu'on puisse se voir attribuer la version 1 ou 2. Avec le variant, nous pouvons maintenant récupérer le dossier avec les fichiers de la bonne version. Initialement, nous nous placions dans le dossier /usr/share/nginx/html, mais maintenant nous nous plaçons dans /usr/share/nginx/html$variant. $variant est égal à 1 ou 2 donc on retrouve bien les dossiers des versions.
En relançant plusieurs fois le projet, nous avons pu voir qu'en moyenne, une fois sur deux nous avions la version 1 et sinon la version 2. 

### Intégration de la persistance des données

En rechargant le site internet, on remarque qu'on change souvent de versions. C'est problématique car un utilisateur utilisant le site Internet devrait toujours être sur la même version. Nous devons donc garder en mémoire la version de l'utilisateur, passant par la gestion des cookies. Nous allons ajouter un cookie ayant la valeur du variant : 
`add_header Set-Cookie "variant=$variant; Path=/; Max-Age=604800";` 
Ensuite, avant d'intégrer notre variant dans le nom du dossier : /usr/share/nginx/html$variant, nous vérifions si un cookie est défini. S'il l'est nous affections la valeur du cookie à la variable "variant".
```
if ($cookie_variant) {
            set $variant $cookie_variant;
}
```

Dans le cas où le cookie est vide, on passera dans la directive `split_clients` vue précédemment. Nous avons pu tester en rechargeant de nombreuses fois la page (avec une répartition 50/50) et cela fonctionnait.

/!\ Si l'utilisateur supprime ses cookies, il pourra avoir une autre version.

# Fuzzing

Le fuzzing est une technique de test automatique qui consiste à injecter des données plus ou moins aléatoires dans un système pour détecter des bugs, des failles de sécurité ou des comportements inattendus. L'objectif est de pousser le logiciel à ses limites et de découvrir des vulnérabilités potentielles qui pourraient être exploitées par des attaquants ou causer des problèmes de fonctionnement.


On choisit de tester notre Front-end avec l'outil [Ffuf](https://github.com/ffuf/ffuf) et le code de notre Back-end avec l'outil [Jazzer.js](https://github.com/CodeIntelligenceTesting/jazzer.js?hsCtaTracking=70b51c80-f6c2-4cbd-ad16-a5633f6c341f%7C93f2aec2-9b6f-497d-aff2-019a1c8eaa4a). 

## Test du Front-End
### Présentation de ffuf

Ffuf est un outil de fuzzing de contenu web écrit en go. Il est principalement utilisé pour découvrir des ressources cachées ou non référencées sur un site web, pour tester la présence de répertoires et de fichiers sensibles, ainsi que pour identifier des points d'entrée potentiels pour des attaques.

Ffuf utilise des listes de mots (wordlists) pour générer des requêtes HTTP personnalisées, qu'il envoie au serveur cible. Il analyse ensuite les réponses du serveur (200, 401, 404...) pour détecter des réponses inhabituelles ou des codes d'erreur qui pourraient indiquer la présence de vulnérabilités ou de configurations incorrectes.

### Mise en place
Pour utiliser Ffuf il faut d'abord l'installer depuis le repo Github donné plus haut en hyperlien. En plus de cela, il faudra installer le langage [Go](https://go.dev/doc/install) sur sa machine afin d'executer l'outil; et ne pas oublier de configurer la variable d'environnement correspondante si ce n'est pas déjà fait automatiquement /!\ . Une fois Go et Ffuf installés, on peut désormais installer une première liste de mots-clés (wordlist). Cette dernière servira à la génération des différents URLs auxquels on essaiera d'accéeder depuis notre site avec Ffuf. On peut dans un premier temps récupérer des wordlists assez générales et basiques en executant les commandes suivantes (trouvables sur Ffuf.me).
```
cd ~
mkdir wordlists
cd wordlists
wget http://ffuf.me/wordlist/common.txt
wget http://ffuf.me/wordlist/parameters.txt
wget http://ffuf.me/wordlist/subdomains.txt
```
Une fois ceci fait, on est prêt à lancer l'outil.

### Utilisation

Pour utiliser l'outil, il ne reste plus qu'à lancer un terminal et prompter la commande suivante
```
.\ffuf -w .\chemin\vers\ma\wordlist\mawordlist.txt -u url_de_mon_site/FUZZ
```

Dans cet exemple, ffuf effectuera autant de requêtes GET qu'il y a de mots dans la wordlist, à l'URL spécifié, en remplaçant à chaque fois le mot-clé "FUZZ" par un mot de la wordlist.

Si la section que vous essayer de fuzzer nécessite une authentification et que le site web vous fournit un token d'authentification, vous pouvez réutiliser ce token dans la commande comme suit (-H ajoute un header à la requête, et permet donc de joindre le token).

```
.\ffuf -w .\chemin\vers\ma\wordlist\mawordlist.txt -u url_de_mon_site/zone_accessible_avec_token/FUZZ -H "Authorization: Bearer VOTRE_TOKEN_D_ACCES" 
```

### Notre test
On a donc lancé notre application Website puis lancé la commande
```
.\ffuf -w .\les_wordlists\customwords.txt -u http://localhost:4200/FUZZ -H "Authorization: Bearer VOTRE_TOKEN_D_ACCES" 
```
Or, on s'est rendu compte que notre front-end redirigeait toutes les requetes de pages incconues vers une page 404 sans renvcoyer le code d'erreur 404. Alors tous les résultats nous ont donné un code de retour 200 car on atteint bien une page, la 404 (cela est dû à la configuration de nos routes dans Angular et une lacune concernant la gestion des codes de retour dans notre serveur nginx).

### Autre méthode

Ffuf permet donc d'automatiser la tâche de fuzzing mais on peut aussi manuellement envoyer des données aléatoires ou pseudo-aléatoires dans notre application. On peut par exemple essayer d'envoyer des lettres dans des formulaires qui devraient contenir des chiffres et observer le résultat.

On s'est alors rendu compte que dans le champ pour modifier l'âge on pouvait écrire autre chose que des caractères chiffrés. Ainsi, si on écrit ":)" dans "âge" on aura ":) ans" d'affiché sur la page des informations de notre compte personnel.

## Test du Back-End
[PARTIE DE GUILLAUME]
### Présentation de Jazzer.js
### Mise en place
### Utilisation
