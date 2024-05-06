# SkyWalking
SkyWalking est un système APM (Application Performance Monitoring) open-source sous licence Apache 2.0. Cet outil permet d’observer les capacités des systèmes micro-servicisés, cloud et container-based. Il permet d’obtenir beaucoup d’informations sur la latence de l’application, l’utilisation de différents services, le temps de réponse, etc. 

Permet de metrics (=data sur les performances de l’application), logs (=enregistrement des messages d’erreur) and tracing (=on peut tracer une requête du début à la fin pour identifier les services défectueux et qui produisent des erreurs). 

Les avantages de SkyWalking c’est qu’on peut l’utiliser avec des architectures cloud native et des architectures traditionnelles. Très utile pour les très très grandes architectures (utilisé par Alibaba, Huawei, etc.). Permet aux équipes de dev, de ops et de maintenance de voir les différents trucs qui interagissent dans ce qu’ils font.  

<u>Tutoriel et mise en place :</u> 

Il est possible de mettre en place SkyWalking avec Kubernetes (avec un tutoriel disponible [ici](https://amjadhussain3751.medium.com/step-by-step-detailed-guide-to-setup-apache-skywalking-on-kubernetes-8369e3d93242).

Nous avons décidé d’implémenter SkyWalking sur Docker, nous avons donc pris les images UI et OAP ainsi que les fichiers docker-compose associés (trouvables [ici](https://github.com/apache/skywalking/tree/33402ce87d4ac8f378f92ab36544b6af18ff0bc6/docker) pour ajouter SkyWalking à notre projet. Nous avons ensuite lancé ces conteneurs avec les commandes suivantes : 

**Pour le conteneur OAP :** 
docker run -d --name skywalking-oap \
  -e SW_STORAGE=elasticsearch \
  -p 11800:11800 \
  -p 12800:12800 \
  apache/skywalking-oap-server:latest

**Pour le conteneur UI :** 
docker run -d --name skywalking-ui \
  -p 403:403 \
  -e SW_OAP_ADDRESS=<IP OAP> \
  -e SW_OAP_GRPC_PORT=11800 \
  -e SW_STORAGE=elasticsearch \
  apache/skywalking-ui:latest

<IP OAP> est l’adresse IP de notre conteneur OAP à trouver en inspectant le conteneur Docker pendant qu’il tourne. 

Pour setup l’outil pour le tester, nous avons simplement lancé le script startup.sh afin d’exposer les bons ports. Ce script n’est pas particulièrement complet, il n’est pas fait pour paramétrer l’outil dans un environnement réel ni pour tourner très longtemps mais cette configuration était suffisante pour nos tests. 

Et enfin, pour observer les données, nous avons simplement été sur http://localhost:403 pour visualiser les données. Nous avons ensuite utilisé Doodle afin d’observer les différentes métriques disponibles sur SkyWalking.




# Prometheus
Prometheus est un système de monitoring et d’alerte open-source sous licence Apache 2.0. Il y a plusieurs outils différents : le serveur Prometheus principal pour récupérer et stocker les données, un manager d’alertes, des outils de support, etc. 

Ça convient aussi bien pour du monitoring machine-centric mais aussi pour des architectures très dynamiques. Prometheus est à fond pour la fiabilité, les serveurs prometheus sont standalone et ne dépendent pas de stockage externe. On peut se baser dessus quand d’autres parties du système sont cassées.

Le souci c’est que ça peut perdre en précision, le système va préférer perdre un peu d'information plutôt que de faire casser tout le système. Il n’est donc pas du tout adapté pour les systèmes argent-related (activités bancaires). 

Tuto : 

Nous avons utilisé une image Docker pour mettre en place Prometheus (disponible [ici](https://prometheus.io/docs/prometheus/latest/installation/). Nous avons ensuite créé un fichier de configuration prometheus.yml. Nous avons pris le fichier d’exemple disponible sur le Github du projet Prometheus (disponible [ici](https://github.com/prometheus/prometheus/blob/main/documentation/examples/prometheus.yml). Nous avons ensuite lancé notre conteneur avec la commande suivante : 

docker run -d --name prometheus \
  -p 9090:9090 \
  -v ~/prometheus.yml:/etc/prometheus/prometheus.yml \ prom/prometheus
A la place de ~/prometheus.yml, il faut mettre le chemin d’accès complet vers le fichier de configuration. 

Puisque nous avons exposé le port 9090, nous pouvons accéder à Prometheus sur l’adresse http://localhost:9090. Nous avons ensuite utilisé Doodle et observé les métriques.


# Netflix Zuul
Zuul est un outil de monitoring, de routing dynamique et de sécurité open-source sous licence Apache 2.0. Il permet notamment de contrôler l’authentification nécessaire à certaines ressources et rejeter les requêtes qui ne les satisfont pas, de faire du routing dynamique, des tests de charge, de la résilience multi-région (entre différents serveurs d’AWS), etc. 

# OpenTracing
OpenTracing est un projet archivé puisqu’il a été fusionné avec le projet OpenCensus pour créer le projet OpenTelemetry. C’était un projet open-source permettant de faire du tracing distribué. Ce procédé permet de suivre une requête parmi les différents microservices, les services cloud, les bases de données, etc. Ce projet permettait donc de repérer des erreurs ou des latences dans le service.

# Datadog
Datadog est un très bon outil de suivi de données. Il fonctionne comme une ressource centrale, à laquelle nous n’avons pas besoin d’ajouter différents outils annexes. 
Cet outil étant payant, nous n’avons pas pu le tester.

# Kuberhealthy
Kuberhealthy est un outil fait pour Kubernetes. Il permet de monitoring et la vérification continue d’application et crée également des métriques pour Prometheus.  

Il fonctionne avec Kubernetes 1.16 ou supérieur. 


