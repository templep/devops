# ESIR2 - DevOps -Cloud Native and Microservices
*Ethan GAUTHIER & Roland KOFFI & Kechiedou MEDA*

*Vous pouvez visiter le git complet : * https://github.com/degrize/pullRequestDevopsCloudNative/tree/main


Ce projet vise à convertir une application monolithique en une architecture de microservices, tout en intégrant divers outils de DevOps pour une approche Cloud Native.

## Notre travail
Notre apport sur ce projet a été d'ajouter plusieurs services :
 Monitoring : Prometheus et grafana (poour la visualisation) et cAdvisor pour récupérer les métriques des containers
 Build pour automatisation de test : Jenkins
 Analyse statique de code : SonarQube

Schéma de notre architecture microservices
![image](https://github.com/degrize/pullRequestDevopsCloudNative/assets/133746978/e3bc9aed-8a45-4e4a-a3c9-31129886464f)

## Lancement de l'application
Pour utiliser l’application, il suffit de lancer Quarkus d’utiliser la commande docker compose
sur le fichier prod ou dev afin de lancer l’application en mode production ou en
mode développement.
Ensuite, il suffit de se rendre sur http://localhost pour pouvoir accéder au
site. Le site sera distribué par Nginx en production et par Angular en
développement.
Chaque donnée sera affichée suite à un appel au backend NestJS qui
lui-même communique avec la base de données PostgreSQL.
Pour lancer une notification, il faut être connecté, puis aller sur le détail
d’une association de laquelle on est président et cliquer sur le bouton Envoyer
Notification. Une requête sera alors envoyée au backend qui traitera la notification
avant de la mettre dans la file RabbitMQ, puis Quarkus videra la file RabbitMQ, et
affichera la notification dans la console.

## *QUarkus*
```
./mvnw -f . quarkus:dev
```

# FICHIERS DE CONFIGURATION

## *DockerFile Back :*

```
FROM node:18-alpine
RUN mkdir -p /app
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["node", "dist/main.js"]
```




## *DockerFile Front :*
```
#### *Stage 1:* Compile and Build angular codebase

 Use official node image as the base image
FROM node:18-alpine as build

 Set the working directory
WORKDIR /usr/local/app

 Add the source code to app
COPY ./ /usr/local/app/

 Install all the dependencies
RUN npm install

 Generate the build of the application
RUN npm run build --prod


#### *Stage 2:* Serve app with nginx server

 Use official nginx image as the base image
FROM nginx:latest

 Copy the build output to replace the default nginx contents.
 
COPY --from=build /usr/local/app/dist/fr-administration-front /usr/share/nginx/html

COPY ./nginx.conf /etc/nginx/nginx.conf

 Expose port 80
EXPOSE 80
```


### *DockerCompose :*
```Dockerfile
version: '3'

services:

  frontend:

    build:
      context: ./front
      dockerfile: Dockerfile
    ports:
      - "8080:80"
    restart: always
    

  backend:
    build:
      context: ./back
      dockerfile: Dockerfile
    ports:
      - "3001:3000"
    depends_on:
      - database
      - rabbitmq
    environment:
      - DATABASE_HOST=database
      - DATABASE_PORT=5432
      - DATABASE_USERNAME=postgres
      - DATABASE_PASSWORD=admin
      - DATABASE_NAME=mydatabase
    restart: always
    
  database:
    image: postgres:latest
    ports:
      - "5433:5432"
    volumes:
      - postgres-data:/var/lib/postgres
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=admin
      - POSTGRES_DB=mydatabase
    restart: always

  rabbitmq:
    image: rabbitmq:latest
    ports:
      - "5672:5672"
    volumes:
      - rabbitmq-data:/var/lib/rabbitmq

  jenkins:
    image: jenkins/jenkins:lts
    ports:
      - "8081:8080"  # Port pour l'interface utilisateur Jenkins
      - "50000:50000"  # Port pour les agents Jenkins (JNLP)
    volumes:
      - jenkins-data:/var/jenkins_home  # Stockage des données Jenkins pour la persistance
    environment:
      JAVA_OPTS: "-Djenkins.install.runSetupWizard=false"  # Désactive le Wizard de setup initial si souhaité
    restart: always

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    environment:
      - GF_INSTALL_PLUGINS=grafana-clock-panel
    ports:
      - '3001:3000'
    volumes:
      - ./grafana:/etc/grafana/provisioning/datasources

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - 9090:9090
    restart: unless-stopped
    volumes:
      - ./prometheus:/etc/prometheus
      - prom_data:/prometheus
    depends_on:
      - cadvisor
    
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    ports:
      - 8082:8080
    depends_on:
      - redis
    
  redis:
    image: redis:latest
    container_name: redis
    ports:
      - 6379:6379


volumes:
  postgres-data:
  rabbitmq-data:
  jenkins-data:  # Défini pour la persistance des données Jenkins
  grafana-storage: 
  prom_data:
```

## Prometheus.yml

```
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s
alerting:
  alertmanagers:
  - static_configs:
    - targets: []
    scheme: http
    timeout: 10s
    api_version: v1
scrape_configs:
- job_name: prometheus
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  static_configs:
  - targets:
    - localhost:9090
- job_name: cadvisor
  scrape_interval: 5s
  static_configs:
  - targets:
    - cadvisor:8080
```

## *Datasource.yml pour Grafana :*
```
apiVersion: 1

datasources:
- name: Prometheus
  type: prometheus
  url: http://prometheus:9090 
  isDefault: true
  access: proxy
  editable: true
```

## Configuration grafana :
*1. Page d'accueil :*
username : admin
password : admin
![Capture d’écran du 2024-05-08 00-28-34](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/e11f4989-a5a6-4adc-b680-0ba5f935e9a5)

*2. Aller dans "Dashboard"*
![image](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/07c73dad-fb2f-43de-ad47-aa283f481030)

*3. Puis cliquer sur "Add visualization"*
![Capture d’écran du 2024-05-08 00-30-46](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/2e433645-5062-4755-9cb3-dc722416894a)

*4. Puis cliquer sur *Configure a new datasource"*
![Capture d’écran du 2024-05-08 00-30-58](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/78821433-c7b8-4abf-8134-9decb4cb8304)

*5. Choisir sur "Prometheus"*
![Capture d’écran du 2024-05-08 00-45-44](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/69bd420e-49bd-4db2-862d-e73106ce06e9)

*6. Entrer l'url : "http://localhost:9090"*
![Capture d’écran du 2024-05-08 00-31-18](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/f714f236-59d5-4b7a-ac0b-d53a2e46dc63)

*7. En bas de la page cliquer sur "Save and test"*
![Capture d’écran du 2024-05-08 00-31-26](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/573c6551-0a66-4363-9768-1cf97bd7ee65)

*8. Revenir à 3. puis choisir "Prometheus"*
![Capture d’écran du 2024-05-08 00-45-15](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/16c6003b-4b32-4b62-93db-33a1d66ab899)

*9. Choisir la métrique qu'on veut visualiser puis sur "Run queries*
![Capture d’écran du 2024-05-08 00-39-38](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/584b063e-c1c9-44c3-b49d-f5570a96afef)

*10. Visualiser*
![Capture d’écran du 2024-05-08 00-41-30](https://github.com/degrize/pullRequestDevopsCloudNative/assets/87711309/6e572b15-293f-4546-bd2e-b52908461765)





