# Tuturiel : Nginx

---

## Introduction

Nginx est un serveur web offrant des fonctionnalités diverses comme le reverse proxy. C'est un logiciel gratuit et open source avec de bonnes performances. Sa création avait pour but de battre les serveurs Apache, qui depuis à rattraper son retard. Il existe une version payante de Nginx (Nginx Plus) qui rajoute des fonctionnalités.

## Mise en place de Nginx

Nginx est soit téléchargeable directement sur leur page ou via une image docker qui facilite son déploiement.
La configuration du serveur Nginx passe par un fichier `nginx.conf` qui spécifie le comportement du serveur.

## Amélioration de la sécurité

Nginx est un outil qui se met en place assez facilement. Cependant, il ne faut pas s'arrêter au fonctionnement de base et mettre en place les sécurités que nginx propose.

### Reverse proxy

Le reverse proxy permet de faire communiquer le front avec les différents micro-services du backend sans avoir besoin d'exposer des ports supplémentaire de la machine.
Sa mise en place est définis le fichier config de nginx.
Cela consiste à rediriger les requêtes du front aux services présents dans le backend sans avoir à rendre leurs ports disponible de l'extérieur. 

### Modules

La version de base de nginx comprend un grand de modules.
Il est possible de désactiver ceux non nécessaires afin de garantir une meilleure sécurité.
Pour les désactiver, il faut recompiler nginx à la main.

### Certificat SSL

Un certificat SSL permet de chiffrer les échanges entre le serveur et le client.
Pour le mettre en place, on doit renseigner les champs `ssl_certificate` et `ssl_certificate_key` pour le certificat ainsi que la clé.
On doit aussi dire à Nginx d'écouter sur le port 443 prévu pour les connections sécurisées.
Cela désactive aussi les connections HTTP (non chiffrées).

De plus, on peut désactiver les protocoles TLS pouvant conduire à des attaques (TLSv1 et TLSv1.1 par exemple).
Il suffit pour cela de rajouter la ligne suivante dans le fichier de configuration nginx.conf pour sélectionner les protocoles à utiliser :

`ssl_protocols TLSv1.2 TLSv1.3;` 

## Conlusion

Nginx est un outil très utilisé lors de la mise en place d'un serveur web.
Cependant il faut faire attention à sa configuration afin de profiter pleinement des fonctionnalités que propose l'outil.



## Authors

- Noam GEFFROY
- Pol JAOUEN
- Stevan METAYER
- Fanny SHEHABI
