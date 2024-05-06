# Tutoriel : Secrets avec docker-compose

## Introduction

Un secret est une information sensible (un mot de passe, une clé API...), qui ne devrait pas être stockée de manière non encryptée dans des endroits accessibles au grand public. Docker Compose propose un moyen de gérer ces informations sans avoir à les enregistrer comme variable d'environnement, qui risquerait de les exposer de manière non intentionnelle, puisqu'elles sont accessibles à tous les processus, peuvent être affichées dans des logs...

## Mise en place

### Création de secrets

Tout d'abord, la node sur laquelle on va exécuter nos conteneurs doit faire partie d'un swarm
```bash
$ docker swarm init
```
Ensuite, nous pouvons créer des secrets de la manière suivante
```bash
$ echo "SuP3rS3cR5tP@S5w0rD" | docker secret create db_password -
```
Avec ici `db_password` l'identifiant du secret, et `SuP3rS3cRetP@S5w0rD` le secret.

Il est aussi possible de créer des secrets dans des fichiers, mais attention à ne pas les rendre publics par erreur !
```bash
$ echo "SuP3rS3cRetP@S5w0rD" > db_password.txt
```

### Utiliser les secrets dans le `docker-compose.yaml`

Dans `docker-compose.yaml`, commencer par déclarer les secrets que nous allons utiliser dans le projet :
```yaml
secrets:
  db_password:      
    external: true  # Pour les secrets créés avec "docker secret create"
  db_password_file:
    file: db_password.txt # Pour les secrets dans un fichier
```

Puis, pour les utiliser dans un conteneur, il faut ajouter le block `secrets` suivi des secrets que l'on veut utiliser. On peut par la suite récupérer les secrets dans le conteneur à l'emplacement `/run/secrets/`.

```yaml
services:
  db:
    image: mysql
    environment:
      MYSQL_USER: tlc
      MYSQL_PASSWORD: /run/secrets/db_password
    secrets:
       - db_password
```
Et voilà ! Nos secrets sont maintenant passés aux conteneurs, tout en étant mieux protégés contre de potentielles attaques !

## Conclusion

L'utilisation de la fonctionnalité de secrets de docker nous permet donc d'ajouter facilement une couche de sécurité à notre application. Toutefois, même si les secrets sont effectivement encryptés en dehors des conteneurs, ils restent en tant que variable d'environnement en clair à l'intérieur de ceux-ci. Une faille dans une image docker pourrait alors permettre à un attaquant de tout de même récupérer nos données sensibles.

##  Authors

 - Noam GEFFROY
 - Pol JAOUEN
 - Stevan METAYER
 - Fanny SHEHABI
