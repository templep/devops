# Demande de Pull Ajouter de la sécurité pour les conteneurs

## Description

Ajouter de la sécurité pour les conteneurs dans un projet de développement web qui utilise un frontend avec vitejs/React et un backend avec expressjs. Pour pouvoir faire fonctionner le projet, veuillez vous référer au [README.md](./README.md) du projet.

## Les 4 Pratiques de Sécurité Implémentées dans notre Projet

1. Utilisez des Images de Base Fiables
2. Mettez Régulièrement à Jour vos Images
3. Exécutez les Conteneurs en tant que Non-root
4. Utilisation de Constructions Multi-étapes et d'Images Distroless

---

# 1. Utilisez des images de base fiables

- Utilisez des images officielles provenant de Docker Hub
- Utilisez des images provenant de sources fiables
- Utilisez des images avec une faible surface d'attaque
  Une liste des images officielles docker est disponible sur le site de Docker Hub. Vous pouvez les trouver à l'adresse suivante : https://hub.docker.com/search?q=&type=image.
  Elles sont vérifiées et maintenues par Docker et par d'autres organisations comme redhat, ubuntu, etc.

Dans notre projet, nous avons utilisé les images officielles de Node.js et d'Alpine.

- [Image officielle de Node.js Alpine](https://hub.docker.com/_/node)
- [Image officielle d'Alpine](https://hub.docker.com/_/alpine)

Il faut faire attention aux images utilisées pour ne pas utiliser des images obsolètes ou non maintenues. Des images trouvées en ligne peuvent être des cryptojacking ou des images malveillantes. Il est donc important de vérifier la source de l'image et de s'assurer qu'elle est fiable. 
# 2. Mettez régulièrement à jour vos images

Il est important de mettre à jour régulièrement vos images pour bénéficier des dernières mises à jour de sécurité et des correctifs de bogues. Pour cela, vous pouvez aller sur le site de Docker Hub et vérifier si une nouvelle version de l'image est disponible. Vous pouvez également utiliser des outils comme Anchore Engine, Clair, ou Trivy pour analyser vos images et détecter les vulnérabilités.
Pour utiliser trivy vous sur notre application vous devez tout d'abord l'installer sur votre machine. Pour cela, vous pouvez suivre les instructions disponibles sur le site officiel de Trivy à l'adresse suivante : https://aquasecurity.github.io/trivy/v0.18.3/installation/
Ensuite pour analyser une image, vous pouvez utiliser la commande suivante :

```
trivy image <nom_image>
```
Nous avons installé trivy sur notre PC qui utilise une distro Ubuntu pour analyser les images de notre projet.
Pour l'installer nous avons utilisé la commande suivante pour installer le binaire:

```shell
wget https://github.com/aquasecurity/trivy/releases/download/v0.18.3/trivy_0.18.3_Linux-64bit.deb
sudo dpkg -i trivy_0.18.3_Linux-64bit.deb
```
Ensuite pour analyser une image, vous pouvez utiliser la commande suivante :
```shell
trivy image <nom_image>
```
Si vous ne vous souvenez plus du nom de vos images vous pouvez utiliser la commande suivante pour les retrouver :
```shell
docker images
```
Il devrait renvoyer une liste comme cela :
  
  ```shell
  REPOSITORY                     TAG       IMAGE ID       CREATED         SIZE
lilpiggycaching-frontend-dev   latest    9b9815e07c6a   17 hours ago    335MB
lilpiggycaching-backend-dev    latest    54ab951cca97   17 hours ago    188MB
  ```
Ici nos deux images sont lilpiggycaching-backend-dev et lilpiggycaching-frontend-dev pour celle de développement et lilpiggycaching-backend et lilpiggycaching-frontend pour celle de production.

On peut retrouver le résultat suivant par exemple pour l'image lilpiggycaching-backend-dev :

```
2024-04-20T19:24:40.188+0200    INFO    Detected OS: alpine
2024-04-20T19:24:40.188+0200    WARN    This OS version is not on the EOL list: alpine 3.19
2024-04-20T19:24:40.188+0200    INFO    Detecting Alpine vulnerabilities...
2024-04-20T19:24:40.188+0200    INFO    Number of PL dependency files: 0
2024-04-20T19:24:40.188+0200    WARN    This OS version is no longer supported by the distribution: alpine 3.19.1
2024-04-20T19:24:40.188+0200    WARN    The vulnerability detection may be insufficient because security updates are not provided

lilpiggycaching-backend-dev (alpine 3.19.1)
===========================================
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)

```

# 3. Exécutez les conteneurs en tant que non-root

- Exécutez les conteneurs en tant que non-root
- Utilisez l'instruction USER dans votre Dockerfile. \
   Par défaut, l'utilisateur du container que vous créez dans votre Dockerfile est root. C'est pourquoi il est nécessaire dans la plupart des cas de modifier ce user dans votre Dockerfile.
  Voici un exemple ici du [Dockerfile](./API/Dockerfile) de backend:

  ```dockerfile
  FROM $RUN_IMAGE AS run-env

  # Copy from the build image app + dependencies

  COPY --from=build-env /usr/src/app /usr/src/app
  WORKDIR /usr/src/app
  EXPOSE 3000

  # Change user to a non-root user

  USER 1000

  CMD ["server.js"]
  ```

  La commande USER dans le Dockerfile permet de désigner l'utilisateur qui réalisera les commandes qui suiveront. Comme il n'y a pas de changement de USER avant le lancement de la dernière commande, l'application sera exécutée avec l'utilisateur 1000 (non-root). On choisit ici 1000 car cela permet d'utiliser un utilisateur non-root sans avoir à donner des commandes pour le créer.

### Problème rencontré:

Cette pratique n'est pas compatible avec les principes de hot-reload très pratique pour le développement des containers. Pour pouvoir continuer à utiliser le hot-reload nous avons donc séparé les environnements de développement et de production dans nos Dockerfiles. Ainsi, les [Dockerfile.dev](./API/Dockerfile.dev) sont lancés via un user **root**.
Pour faciliter la gestion de c'est deux environnement vous pouvez utiliser le script bash [deploy.sh](./bin/deploy.sh). Placez vous dans le répertoire `bin` puis:

```shell
# Pour le déploiement en développement
./deploy.sh dev up
# Pour le déploiement en production
./deploy.sh prod
```

# 4. Utilisation de constructions multi-étapes et d'images distroless

La construction d'image dite multi-staging (plusieurs niveau) aide à réduire la taille de l'image finale en ne gardant que le strict nécessaire. Cela permet également de séparer les étapes de compilation et de packaging. Ainsi, on réduit par ailleurs le nombre de dépendances de notre système. De plus, l'utilisation d'images distroless , qui ne contiennent que l'application et ses dépendances sans le système d'exploitation complet, permet de réduire encore la surface d'attaque.

Voici un exemple de Dockerfile utilisant ces bonnes pratiques :

```docker
# Build stage
FROM node:14-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
RUN npm run build
# Final stage
FROM gcr.io/distroless/nodejs20-debian11 # Img distroless par Google pour node
WORKDIR /app
COPY --from=build /app/dist .
CMD ["node", "server.js"]
```

**Attention:** les images distroless ne dispose pas de shell.Vous ne pourrez donc pas intéragir avec votre container pendant son execution et certaine commande pourrait ne pas fonctionner dans votre projet.

---
### Auteurs 
- [Gaspard Audoux]
- [Bastien Demorieux]
