# Les bots dans le DevOps

## Résumé des différents types de bot
//TODO faire le résumé très bref des différents types de bot

## Présentation pratique des bots mis en place par nos soins 
//TODO
| Nom du bot  | Type de bot                | Gratuit | Mise en place réussie |
|-------------|----------------------------|---------|------------------------|
| Renovate    | Gestion des dépendances    | Oui     | Oui                    |
| Dependabot  | Mise a jours de dépendance | Oui     | Oui                    |
| Rultor      |                            | Oui     | Non                    |
| Errbot      | ChatOps                    | Oui     | Non                    |
| Repairnator | Réparation de bug          | Oui     | Non                    |
| Drone       | Test et déploiement        | Non     | Non                    |
| DevopsBot   | ChatOps                    | Oui     | Non                    |


### Renovate
#### Présentation
Renovate est un bot qui va venir scanner le code et vérifier les versions des dépendances. Il va ensuite générer des pull requests pour suggérer les modifications de dépendances par les dernières versions disponibles. C'est ensuite au développeur de s'assurer que la modification fonctionne pour son programme et de l'accepter si besoin.
#### Installation du bot
##### Ajout du bot à son dépôt GitHub
L'installation du bot s'effectue de manière simple via la procédure suivante :
1. Installer the Mend Renovate App sur son compte GitHub en allant sur [la page d'installation de Renovate App](https://github.com/apps/renovate) et cliquer sur "Install":
   ![image](https://user-images.githubusercontent.com/102745725/178965463-525a385e-d914-4309-aeb4-cb4358dc12bc.png)
2. Sur la page suivante choisir le dépôt sur lequel installer Renovate
![image](https://user-images.githubusercontent.com/102745725/178964980-df55dafd-f087-433a-90f7-986fa01c1ac0.png)
##### Configuration du bot
> **Note**
> Bien que le bot soit installé, il ne fonctionnera qu'après l'étape de configuration initiale.

1. Aller dans la section `Pull Requests` de votre dépôt GitHub, et ouvrir la PR : `Configure Renovate`

<img width="1500" alt="onboarding PR" src="https://user-images.githubusercontent.com/102745725/178965966-76aa3941-cac4-4df3-bd89-75b9f8002924.png">

<img width="935" alt="onboarding content" src="https://user-images.githubusercontent.com/102745725/178966039-b11315b8-8c75-416b-9f18-c8109c17d0ab.png">

> **Note**
> Si vous souhaitez faire des modifications de la configuration par défaut, vous pouvez le faire en vous appuyant sur la [documentation](https://github.com/renovatebot/renovate/tree/main/docs/usage/getting-started)
2. Cliquer sur `Merge pull request`

Le bot est désormais fonctionnel !

#### Actions réalisées par le bot
Le bot nous a généré des pull requests suggérant de mettre à jour des dépendances.
Un exemple est ci-dessous :
![image](https://github.com/ESIR2-S8-DevOps/devops/assets/49646276/0863419e-f6f7-43c1-a711-b2fd470d75b1)

#### Critiques et bilan
La mission de ce bot est de suggérer des mises à jour de dépendances et c'est ce qu'il fait. Le problème est qu'il reste au développeur de s'assurer que les modifications proposées seront compatibles avec le code déjà en place et ne causeront pas de bugs. Afin d'automatiser ce processus, nous avons mis en place [l'automatisation des tests à chaque pull request](#Mise-en-place-d'automatisation-pour-une-pleine-intégration-des-bots-dans-la-boucle-DevOps).

### Dependabot
#### Qu'est-ce que Dependabot ?
Dependabot est un outil d'automatisation de gestion des dépendances dans les projets logiciels. Il est conçu pour aider les développeurs à maintenir leurs projets à jour en surveillant les mises à jour des dépendances et en ouvrant des pull requests pour les mettre à jour automatiquement.

#### Fonctionnalités principales

- **Surveillance des dépendances** : Dependabot surveille les fichiers de configuration des dépendances (comme `package.json`) pour détecter les mises à jour disponibles.
![image](https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/securityAlert.png)
- **Ouverture de pull requests** : Lorsqu'une mise à jour est détectée, Dependabot ouvre automatiquement une demande de tirage (pull request) avec les modifications nécessaires pour mettre à jour les dépendances.
![image](https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/PullRequest.png)

- **Gestion des conflits** : Dependabot est capable de gérer les conflits de fusion (merge conflicts) qui peuvent survenir lors de la mise à jour des dépendances, en proposant des solutions ou en les résolvant automatiquement si possible.

- **Compatibilité avec divers écosystèmes** : Il prend en charge une grande variété de gestionnaires de dépendances, y compris npm, Bundler, Composer, Maven, etc.

- **Personnalisation** : Les utilisateurs peuvent personnaliser le comportement de Dependabot en définissant des politiques spécifiques, comme la fréquence des vérifications de dépendances ou les actions à prendre en cas de mise à jour.

#### Avantages

- **Maintien à jour** : En automatisant la gestion des dépendances, Dependabot permet de garder les projets logiciels à jour avec les dernières versions des bibliothèques et des frameworks.

- **Réduction de la dette technique** : En évitant l'accumulation de dépendances obsolètes, Dependabot aide à réduire la dette technique et à maintenir une codebase saine et sécurisée.

- **Gain de temps** : En automatisant les tâches de gestion des dépendances, Dependabot libère du temps pour les développeurs, qui peuvent se concentrer sur des tâches plus créatives et à plus forte valeur ajoutée.

#### Mise en place et paramétrage avec GitHub

Pour activer Dependabot et le configurer sur un dépôt GitHub, il faut :

1. **Activer Dependabot** :
   - Accédez aux paramètres du dépôt sur GitHub.
     
   ![https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/enableDependabot.png](https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/settingsButtun.png)

   - Dans la section "Options", cliquez sur "Security & analysis".

   ![image](https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/codeSecurityAnalysis.png)
   
   - Activez Dependabot pour le dépôt en cliquant sur "Activer".
     
  ![image](https://github.com/ESIR2-S8-DevOps/devops/blob/main/pictures/enableDependabot.png)


3. **Configurer les fichiers de dépendances** :
   - Assurez-vous que les fichiers de configuration des dépendances de votre projet sont présents dans le dépôt (par exemple, `package.json` pour Node.js).

4. **Personnaliser les politiques** :
   - Vous pouvez définir la fréquence des vérifications de dépendances, les règles d'acceptation des mises à jour, les dépendances à ignorer, etc.
   - Pour des politiques plus avancées, vous pouvez également utiliser un fichier de configuration Dependabot directement dans votre dépôt.

5. **Gérer les notifications** :
   - Configurez les notifications pour être informé des actions de Dependabot.
   - Vous pouvez choisir de recevoir des notifications par e-mail, sur la plateforme GitHub, ou via des intégrations avec des services de messagerie comme Discord.

Une fois que Dependabot est activé et configuré sur votre dépôt GitHub, il commencera à surveiller les mises à jour des dépendances et à ouvrir des demandes de tirage (pull requests) lorsque des mises à jour sont détectées.

#### Critiques et bilan
Nous avons ici mis en place un bot native à GitHub capable d'analyser les dépendances comme le bot précédent et de proposer par l'intermédiaire de pull request leur mise à jour en fonction des anomalies de sécurité détecter. Encore une fois ses mises à jour ne se font que si l'utilisateur les accepte pour éviter de générer des problèmes. De plus la sécurité est un véritable plus que ce bot apporte notamment pour détecter et corriger les éventuelles CVE qui ont été déployer.

## Mise en place d'automatisation pour une pleine intégration des bots dans la boucle DevOps
#### Contexte
Nous souhaitons mettre en place des automatisations pour compléter l'intégration de nos bots et augmenter le taux de résilience.
#### Présentation de GitHub Actions
GitHub Actions est une plateforme d'automatisation des workflows intégrée à GitHub, permettant aux développeurs d'automatiser diverses tâches de développement logiciel telles que les tests, les déploiements et les notifications. Ce qui nous intéresse dans ce cas est la possibilité de mettre en place l'exécution de nos fichiers de tests à chaque pull request.
#### Mise en place d'une action
1. Dans notre projet, nous créons un fichier github-actions.yaml dans le répertoire .github/workflows
2. Nous mettons le code ci-dessous dans le fichier
```yaml
name: GitHub Actions Elie
run-name: ${{ github.actor }} is testing out GitHub Actions 🚀
on: [pull_request]
jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event."
      - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
      - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."
      - name: Check out repository code
        uses: actions/checkout@v4
      - run: echo "💡 The ${{ github.repository }} repository has been cloned to the runner."
      - run: echo "🖥️ The workflow is now ready to test your code on the runner."
      - name: List files in the repository
        run: |
          ls ${{ github.workspace }}
      - name: Run tests
        run: |
          cd back
          sudo npm install
          sudo npm test
      - run: echo "🍏 This job's status is ${{ job.status }}."
```
3. Nous faisons ensuite un commit de ce fichier dans une nouvelle branche de notre choix.
4. Nous faisons une pull request de notre nouvelle branche.
5. Dans l'interface de notre dépôt, nous cliquons sur le module *Actions*.
   ![image](https://github.com/ESIR2-S8-DevOps/devops/assets/49646276/d6948d5f-eaeb-48db-a898-9cea26cfc3e5)

7. Nous cliquons sur le menu de gauche, plus précisément sur *GitHub Actions Elie*.
8. Nous visualisons maintenant l'ensemble des exécutions dues aux différentes pull requests.
9. Si nous cliquons sur une action, nous obtenons le détail de l'exécution.
   ![image](https://github.com/ESIR2-S8-DevOps/devops/assets/49646276/f17c5597-3ec6-45d0-af41-c298bf8a9e0f)

#### Critiques et bilan
La mise en place de ce GitHub workflow répond bien à nos attentes et nous permet également d'obtenir une visualisation du résultat des tests directement dans l'affichage des pull requests. Ceci simplifie grandement la prise de décision sur l'acceptation des pull requests générées par les bots.

![image](https://github.com/ESIR2-S8-DevOps/devops/assets/49646276/a5dae8ae-9e86-4eba-9279-a3846a72d12f)

## Rultor
#### Qu'est-ce que Rultor ?

Rultor est un bot DevOps conçu pour automatiser les tâches manuelles répétitives et optimiser vos processus de développement logiciel. Il scanne votre code, vérifie les versions des dépendances et génère des pull requests pour proposer les mises à jour nécessaires.

#### Rultor vous aide à :

- Réduire le temps et les efforts consacrés aux tâches routinières
- Diminuer les erreurs humaines
- Améliorer la qualité du code
- Accélérer les déploiements et les publications
- Favoriser une meilleure collaboration

#### Fonctionnalités clé de Rultor :

- **Merge de code simplifiée** : Rultor automatise le processus de merge du code, réduisant ainsi le temps et les efforts nécessaires pour déployer de nouvelles fonctionnalités.
- **Déploiement automatisé** : Rultor prend en charge le déploiement d'applications vers des environnements de production, garantissant des déploiements fluides et sans erreur.
- **Publication simplifiée des versions** : Rultor automatise le processus, vous permettant de mettre vos fonctionnalités à la disposition des utilisateurs en un clin d'œil.
- **Notifications pertinentes** : Rultor envoie des notifications aux parties prenantes concernées concernant les déploiements, les publications et autres événements clé.
- **Vérifications automatisées du code** : Avant que le code ne soit fusionné dans le référentiel principal, Rultor effectue des vérifications automatisées pour détecter les erreurs et garantir la qualité du code.
- **Validation rigoureuse du code** : Rultor s'assure que toutes les modifications du code respectent les normes établies par l'équipe, contribuant ainsi à un code de qualité supérieure et à une réduction des problèmes.

#### Critique

##### Problèmes:

- Nous ne sommes pas parvenus à mettre en place ce bot. Bien que de nombreuses explications soient présentes pour les différentes utilisations et fonctionnalité du bot, il y a un manque cruel de documentation et d'explication claire sur l'installation même du bot.
- Le bot est rattaché à de nombreux outils tiers (ex : Jenkins, Docker, Kubernetes, ...) augmentant la complexité de ce dernier.

##### Avantages :

- Le système de chat permettant à l'utilisateur de communiquer avec le bot, que ce soit pour lui donner des instructions ou recueillir des informations sur l'état du projet.
- Le grand nombre de fonctionnalités attribuées au bot, lui permettant d'être capable d'exécuter plus ou moins chaque tâche pouvant être demandé à un bot de devOps.

#### Bilan

Pour conclure, Rultor est probablement l'un des bot de DevOps les plus complets disponible aujourd'hui. Malheureusement, la complexité de son installation et la documentation limitée et éparpillée ne nous a pas permis de le mettre en place.

## Errbot
#### Qu'est-ce que Errbot ?

Errbot un bot open-source très polyvalent conçu pour être utilisé dans différents environnements de communication comme Slack, Discord, IRC, etc. Il est souvent utilisé dans des contextes de chatOps, de gestion de salons de discussion, et pour des applications de sécurité domestique.

#### Fonctionnalité : 

Intégration avec différents services de messagerie : Errbot peut être intégré à des plateformes populaires telles que Slack, Discord, IRC, HipChat, etc., ce qui lui permet d'interagir avec les utilisateurs sur ces canaux.

Gestion des commandes : Errbot peut être configuré pour répondre à des commandes spécifiques entrées par les utilisateurs. Par exemple, vous pouvez définir des commandes pour obtenir des informations, déclencher des actions ou interagir avec des services externes.

Extensions et plugins : Errbot dispose d'un système d'extensions et de plugins qui permet aux développeurs d'étendre ses fonctionnalités de manière modulaire. Cela permet d'ajouter des fonctionnalités personnalisées selon les besoins spécifiques de l'utilisateur.

Automatisation des tâches : En utilisant des scripts Python ou des plugins prédéfinis, Errbot peut automatiser des tâches telles que la gestion des déploiements, la surveillance des systèmes, l'envoi de notifications, etc.

Intégration avec des API externes : Errbot peut se connecter à des API externes pour récupérer des données en temps réel, effectuer des actions sur des services tiers, ou encore intégrer des fonctionnalités spécifiques à un service ou une application.

Sécurité et contrôle des accès : Errbot offre des fonctionnalités de sécurité avancées telles que la gestion des autorisations d'accès aux commandes, la gestion des rôles et des privilèges, et la sécurité des données échangées.


#### Critique :

##### Problèmes :

Difficulté à installé le bot dû à l'environnement python dans lequel il tourne. 
Documentation plutôt clair mais il nous manque quelques informations de compréhension et de fonctionnement global.

##### Avantages :

Polyvalent et compatible avec grand nombre d'environnements de communication.

#### Bilan :

Pour conclure, Errbot est un bot compatible avec grand nombre d'environnements de communication et permet d'effectuer des commandes aisément au sein de ces environnements. Cependant il manque un schéma global de la communication et du fonctionnement du bot.


## Repairnator : 
#### Qu'est ce que Repairnator ?

Repairnator est le premier repair bot capable de faire de la réparation automatique de bugs informatiques à grande échelle. il s'agit d'un logiciel efficace et autonome qui sauve des erreurs de code les autres logiciels. Une solution en open source mise à disposition de tous les développeurs.

#### Fonctionnalités clé de Repairnator :

Détection automatique de bugs :
Repairnator est conçu pour détecter automatiquement les bugs dans les projets open-source hébergés sur GitHub. Il surveille en permanence les dépôts de code source et identifie les problèmes signalés, les erreurs de compilation, les échecs de test ou d'autres problèmes similaires.

Création de Pull Requests (PR) :
Une fois qu'un bug est détecté, Repairnator génère automatiquement une Pull Request (PR) pour proposer une correction. Il utilise des techniques automatisées telles que le patching et le rétro-ingénierie du code pour proposer une solution au problème identifié.

Intégration avec les outils de CI/CD :
Repairnator peut être intégré dans des pipelines de livraison continue (CI/CD) pour automatiser le processus de détection et de correction des bugs. Il s'intègre souvent avec des outils tels que Travis CI, Jenkins, ou GitHub Actions pour gérer ce processus de manière fluide.

Analyse et reporting :
Repairnator fournit des analyses détaillées sur les problèmes détectés, les corrections proposées et les résultats des tests. Ces informations sont essentielles pour les développeurs et les équipes de qualité logicielle afin de comprendre les améliorations apportées par le système et les éventuelles améliorations à apporter.

Support des langages de programmation :
Repairnator prend en charge différents langages de programmation couramment utilisés dans les projets open-source, tels que Java, JavaScript, Python, etc. Il est capable de traiter divers types de bugs dans ces langages.

Communauté et développement ouvert :
Repairnator est un projet open-source soutenu par une communauté active de chercheurs et de développeurs. Cela signifie que ses fonctionnalités et son efficacité continuent de s'améliorer grâce aux contributions et aux retours d'expérience de la communauté.

#### Critique

##### Problèmes :

Projet complexe à mettre en place, comme pour Errbot il nous manque quelques informations de compréhension et de fonctionnement global.

##### Avantages :

Une fois installé permet au développeur de gagné en efficacité, car il est capable de réparer un projet.

#### Bilan :

Pour conclure, Repairnator offre une solution automatisée et intelligente pour détecter, corriger et améliorer les bugs dans les projets open-source, ce qui contribue à accélérer le processus de développement logiciel et à garantir la qualité du code produit.


## Drone :
#### Qu'est ce que Drone ?

Drone est un système open-source de CI/CD (Continuous Integration/Continuous Deployment) qui automatise les processus de construction, de test et de déploiement des applications logicielles. 

#### Fonctionnalités clé de Drone :

Automatisation du CI/CD :
Drone automatise les étapes de construction (build), de test et de déploiement de votre application à chaque modification du code source. Cela garantit une intégration continue et une livraison continue, ce qui permet de détecter rapidement les erreurs et de livrer des versions fonctionnelles de l'application de manière efficace.

Intégration avec les outils de versionnement :
Drone se connecte aux plateformes de gestion de versions comme GitHub, GitLab, Bitbucket, etc. Il surveille les changements dans les dépôts de code source et déclenche automatiquement les pipelines CI/CD en fonction de ces modifications.

Définition des pipelines avec YAML :
Les pipelines CI/CD dans Drone sont définis à l'aide de fichiers de configuration YAML (.drone.yml). Ces fichiers décrivent les étapes du pipeline, y compris les commandes de build, de test, de déploiement et les notifications à envoyer en cas de succès ou d'échec.

Support multi-langage et multi-environnement :
Drone prend en charge une variété de langages de programmation et d'environnements, ce qui le rend flexible pour les projets divers. Il offre des plugins pour des actions spécifiques telles que la construction de conteneurs Docker, le déploiement sur des serveurs, l'envoi de notifications, etc.

Parallélisme et scalabilité :
Drone peut exécuter des étapes de pipeline en parallèle, ce qui accélère le processus global. De plus, il peut être configuré pour s'intégrer à des environnements de cluster et à des services de conteneurisation pour une exécution scalable des pipelines.

Gestion des secrets et des variables d'environnement :
Drone offre des mécanismes sécurisés pour gérer les secrets (mots de passe, clés API, etc.) et les variables d'environnement nécessaires aux étapes du pipeline, assurant ainsi la sécurité des données sensibles.

#### Critique

##### Problèmes :

Projet complexe à mettre en place, comme pour Errbot il nous manque quelques informations de compréhension et de fonctionnement global. Mais surtout il est payant. Nous avons commencé l'installation, mais avons été bloqués dû au paiement.

##### Avantages :

Gain majeure en temps pour le test et le déploiement.

#### Bilan :

Pour conclure, Drone est un outil puissant pour l'automatisation des processus CI/CD dans les projets de développement logiciel. Ses fonctionnalités de configuration flexible, d'intégration avec les outils populaires et de gestion sécurisée en font un choix populaire parmi les équipes de développement modernes.

## DevopsBot :
#### Qu'est ce que DevopsBot ?

DevopsBot est un bot conçu pour automatiser et faciliter les processus de développement et d'exploitation (DevOps) dans les projets logiciels. Il s'intègre généralement à des plateformes de messagerie telles que Slack, Microsoft Teams ou Discord, permettant aux membres de l'équipe de collaborer et d'interagir avec lui via des commandes textuelles.

#### Fonctionnalités clé de DevopsBot :

Automatisation des tâches : 
DevopsBot peut automatiser diverses tâches de développement et d'exploitation, telles que le déploiement, la gestion des tickets, la surveillance des performances, la gestion des configurations, etc.

Intégration avec les outils DevOps : 
Il s'intègre avec les outils populaires de CI/CD, de gestion des versions, de surveillance, de gestion des tickets, etc., pour faciliter le flux de travail DevOps.

Notifications en temps réel : 
Il envoie des notifications en temps réel aux membres de l'équipe sur les événements importants tels que les déploiements réussis, les pannes détectées, les changements de configuration, etc.

Gestion des incidents : 
DevopsBot peut aider à gérer les incidents en fournissant des alertes, en orchestrant les interventions d'urgence, en mettant à jour les statuts d'incidents, etc.

#### Critique

##### Problèmes :


Pour DevopsBot, les problèmes rencontrées sont liées à la compréhension de son fonctionnement global et à sa configuration notamment avec peu de documentation.

##### Avantages :

Automatisation accrue : 
DevopsBot permet une automatisation plus poussée des processus DevOps, réduisant ainsi la charge de travail manuelle et les risques d'erreurs humaines.

Amélioration de la collaboration : 
Il facilite la collaboration entre les membres de l'équipe en fournissant un canal centralisé pour les discussions, les notifications et les actions, ce qui peut accélérer le temps de réponse aux incidents et aux changements.

Traçabilité et historique : 
DevopsBot enregistre souvent les interactions et les actions effectuées, ce qui permet une traçabilité complète des activités DevOps et facilite l'analyse rétrospective et l'amélioration continue.

#### Bilan :

Dans l'ensemble, DevopsBot offre un moyen puissant d'automatiser les processus DevOps, d'améliorer la collaboration et la traçabilité, et de réduire les délais de déploiement et de résolution des incidents. Cependant, il nécessite une configuration et une gestion appropriées pour maximiser ses avantages tout en minimisant les défis potentiels.








