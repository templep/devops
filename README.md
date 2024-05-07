**Software Bot in Software Engineering **


## **Introduction**

Nous allons vous présenter les différents bots que nous avons découverts et implémentés tout au long du cycle DevOps. Nous avons choisi d’implémenter ces bots sur le projet Web que nous avions réalisé au semestre précédent. Les bots dans le Software engineering ont pour but de faciliter ou automatiser des tâches qui peuvent être chronophage ou bien source d’erreur. Ainsi nous avons choisi deux bots que sont Dependabot et Mergify qui facilite respectivement la gestion des dépendances et des merge. 


## **Dependabot**

Ce guide explique comment mettre en place Dependabot dans votre projet, ainsi que son fonctionnement et comment configurer les fichiers YML associés.


## **Utilité de Dependabot**

Dépendabot offre plusieurs avantages pour la gestion des dépendances de votre projet :



* **Sécurité** : En surveillant régulièrement les mises à jour des dépendances, Dépendabot vous aide à détecter et à corriger rapidement les vulnérabilités de sécurité dans votre code. Cela réduit les risques liés aux failles de sécurité connues en maintenant vos dépendances à jour.
* **Fiabilité** : Les mises à jour régulières des dépendances contribuent à maintenir la stabilité et la fiabilité de votre application. En évitant l'utilisation de versions obsolètes ou périmées des bibliothèques, vous minimisez les risques de bogues et de comportements inattendus.
* **Conformité** : L'automatisation de la gestion des dépendances avec Dépendabot facilite la conformité aux exigences de sécurité des logiciels. Vous pouvez également suivre les recommandations de sécurité de l'industrie et des régulateurs en matière de gestion des vulnérabilités.
* **Productivité** : En automatisant la surveillance et la mise à jour des dépendances, Dépendabot libère du temps et des ressources pour se concentrer sur le développement de fonctionnalités et d'améliorations plutôt que sur la maintenance des dépendances.

En résumé, l'utilisation de Dépendabot contribue à renforcer la sécurité, la fiabilité, la conformité et la productivité de votre projet en automatisant la gestion des dépendances logicielles. En adoptant cette approche proactive, vous pouvez minimiser les risques associés aux dépendances obsolètes ou vulnérables, tout en maintenant votre code à jour avec les dernières fonctionnalités et correctifs de sécurité.


## **Mise en place de Dependabot**



1. **Activer Dependabot** : \
Pour activer Dependabot sur votre dépôt GitHub, suivez ces étapes :
    * Allez dans les paramètres de votre dépôt.
    * Cliquez sur "Security & analysis" dans la barre latérale.
    * Sélectionnez "Enable Dependabot alerts".
2. **Créer le fichier de configuration** :
    * Dans la racine de votre projet, créez un fichier `.github/dependabot.yml`.
    * Copiez et collez le contenu du fichier de configuration YML nécessaire pour votre projet. Vous pouvez trouver des exemples dans la documentation de Dependabot.
3. **Personnaliser la configuration** :
    * Adaptez le fichier de configuration YML en fonction des besoins spécifiques de votre projet. Vous pouvez définir les écosystèmes de packages à surveiller, la fréquence des mises à jour, les limites de tirage automatique, etc.
4. **Configurer les notifications** :
    * Configurez les notifications pour être informé des mises à jour de dépendances. Vous pouvez recevoir des notifications par e-mail ou via d'autres canaux.


## **Structure du fichier YML de configuration**

Le fichier de configuration YML de Dependabot suit une structure spécifique qui permet de définir les paramètres de surveillance et de mise à jour des dépendances. Voici un exemple de structure de fichier YML pour Dependabot :

yml


```
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "daily"

```



* `version`: Indique la version de la configuration Dependabot utilisée.
* `updates`: Liste des écosystèmes de packages à surveiller et à mettre à jour.
    * `package-ecosystem`: Écosystème de package à surveiller (par exemple, npm, docker, pip, etc.).
    * `directory`: Chemin vers le répertoire contenant les fichiers de configuration des dépendances.
    * `schedule`: Fréquence à laquelle Dependabot vérifie les mises à jour (par exemple, quotidien, hebdomadaire, etc.).


## **Rôle de Dependabot dans notre projet**

Dans notre projet, Dependabot est chargé de surveiller et de mettre à jour les dépendances des différents écosystèmes de packages que nous utilisons. Voici comment il fonctionne pour chaque écosystème spécifique :



* **npm (Frontend)** :
    * Dependabot vérifie régulièrement les mises à jour des dépendances npm dans le répertoire `/TP_AL-main/fr-administration-front`.
    * Il ouvre automatiquement des pull requests pour proposer les mises à jour nécessaires.
    * La fréquence des vérifications est définie sur une base quotidienne.
* **Docker (Frontend)** :
    * De même, Dependabot surveille les mises à jour des images Docker utilisées dans le répertoire `/TP_AL-main/fr-administration-front`.
    * Il propose des mises à jour via des pull requests selon une fréquence quotidienne.
* **npm (Backend)** :
    * Pour le backend, Dependabot effectue des vérifications similaires dans le répertoire `/TP_AL-main/fr-administration-back`.
    * Il ouvre des pull requests pour les mises à jour des dépendances npm.
    * La fréquence des vérifications est également quotidienne.
* **Docker (Backend)** :
    * Enfin, Dependabot surveille les images Docker utilisées dans le backend et propose des mises à jour via des pull requests.
    * Les vérifications sont effectuées quotidiennement.

En résumé, Dependabot automatise le processus de surveillance et de mise à jour des dépendances dans notre projet, garantissant ainsi que nos applications frontend et backend sont toujours à jour avec les dernières versions de packages et d'images Docker. Cela permet à notre équipe de rester concentrée sur le développement de fonctionnalités tout en assurant la sécurité et la stabilité de nos applications grâce à des dépendances à jour.

 \
**Mergify**

Nous avons pu intégrer le robot Mergify à notre projet.  est un puissant outil d'automatisation qui vous aide à gérer les pull requests dans votre projet en utilisant des règles prédéfinies. Voici comment configurer Mergify pour automatiser la gestion des PR dans votre projet GitHub.


## **Utilité de Mergify**

Voici quelques-uns des avantages et des fonctionnalités de Mergify :



1. **Automatisation des fusions** : Mergify permet de définir des règles de fusion personnalisées qui sont automatiquement appliquées à chaque pull request. Cela permet d'éliminer le besoin d'interventions manuelles pour fusionner les pull requests, ce qui accélère le processus de développement.
2. **Gestion des conflits** : Mergify offre des fonctionnalités avancées pour gérer les conflits de fusion automatiquement. Il peut résoudre les conflits de manière intelligente en utilisant des stratégies telles que la rébase automatique, la fusion avec une branche de destination spécifique, etc.
3. **Personnalisation des règles de fusion** : Les règles de fusion de Mergify sont hautement personnalisables. Vous pouvez définir des conditions basées sur des critères tels que les labels, les révisions approuvées, les vérifications de construction réussies, etc. Cela vous permet d'adapter le processus de fusion à vos besoins spécifiques.
4. **Actions automatisées** : En plus de la fusion automatique, Mergify prend en charge diverses actions automatisées telles que la suppression de la branche source après fusion, l'ajout de labels, la notification des contributeurs, etc. Cela permet d'automatiser encore plus les tâches répétitives liées à la gestion des pull requests.
5. **Suivi et reporting** : Mergify fournit des outils de suivi et de reporting avancés pour surveiller l'état des pull requests, les actions effectuées par Mergify et les performances globales du processus de fusion. Vous pouvez accéder à des tableaux de bord, des rapports et des statistiques pour évaluer l'efficacité de votre workflow de fusion.

En résumé, Mergify simplifie et rationalise le processus de gestion des pull requests sur GitHub en automatisant les fusions, en gérant les conflits de manière intelligente et en offrant une personnalisation avancée des règles de fusion. En adoptant Mergify, les équipes de développement peuvent accélérer leurs cycles de développement, réduire les erreurs humaines et améliorer la qualité du code grâce à des processus de fusion automatisés et fiables.


### **Installation de Mergify**

Pour commencer avec Mergify, vous devez d'abord l'ajouter à votre dépôt GitHub :



1. Accéder à GitHub Marketplace : Rendez-vous sur[ GitHub Marketplace](https://github.com/marketplace/mergify).
2. Choisir un plan : Sélectionnez un plan qui correspond à vos besoins. Mergify offre plusieurs niveaux de service, y compris un plan gratuit pour les projets open source ou de petite taille.
3. Installer Mergify : Cliquez sur "Set up a plan" et suivez les instructions pour installer Mergify sur votre repository. Vous devrez autoriser Mergify à accéder à votre dépôt.

Après l'installation, vous devez configurer les règles selon lesquelles Mergify opérera. Cela se fait en ajoutant un fichier `.mergify.yml` à la racine de votre dépôt ou dans le dossier `.github`.



1. Créer le fichier `.mergify.yml` : Ce fichier contient toutes les règles selon lesquelles les PR seront gérées.
2. Configurer les règles : Vous pouvez définir des règles pour automatiser la fusion, ajouter des labels, rebase, et plus encore. Voici un exemple de configuration 

Il est aussi possible de configurer mergify depuis le dashboard en ligne propre à chaque repository.


## **Structure du fichier <code>.mergify.yml</code></strong>

Le fichier `.mergify.yml` définit les règles de fusion spécifiques à votre projet. Voici un exemple de structure de fichier YAML pour Mergify :

yaml


```
pull_request_rules:
  - name: Automatic merge on approval
    conditions:
      - "#approved-reviews-by>=1"
    actions:
      merge: {}
      delete_head_branch: {}

```



* `pull_request_rules`: Liste des règles de fusion à appliquer.
    * `name`: Nom de la règle.
    * `conditions`: Conditions à vérifier pour appliquer la règle.
    * `actions`: Actions à effectuer si les conditions sont satisfaites.


### **Rôle de Mergify dans notre projet**


### Voici la configuration YAML personnalisée de notre projet pour Mergify. La première règle a été développée par nous même pour faire interagir Dependabot avec Mergify, tandis que les autres règles ont été extraites du site web de Mergify.


```
pull_request_rules:
  - name: Label 'automerge' to PR to merge
	description: Add a label to a pull request that has to be merged
	conditions:
  	- author=dependabot[bot]
	actions:
  	merge:
    	method: merge

  - name: Add a queue label when PR is queued
	description: Toggle the queue label when a pull request is (un)queued.
	conditions:
  	- queue-position > 0
	actions:
  	label:
    	toggle:
      	- merge-queued

  - name: Ping PR author when conflicting
	description: Warn the pull request author when their PR are conflicting
	conditions:
  	- conflict
  	- '-closed'
	actions:
  	comment:
    	message: |
      	👋 {{author}} your PR is conflicting and needs to be updated to be merged

  - name: Make sure PR are up to date before merging
	description: >-
  	This automatically updates PRs when they are out-of-date with the base branch
  	to avoid semantic conflicts (next step is using a merge queue).
	conditions: []
	actions:
  	update:

  - name: Label conflicting pull requests
	description: Add a label to a pull request with conflict to spot it easily
	conditions:
  	- conflict
  	- '-closed'
	actions:
  	label:
    	toggle:
      	- conflict

```



* **Label 'automerge' to PR to merge** :
    * Cette règle ajoute automatiquement le label "automerge" à une pull request pour la fusion si elle est soumise par Dependabot.
    * Elle est utile pour identifier les pull requests générées automatiquement par Dependabot et qui peuvent être fusionnées en toute confiance sans intervention manuelle.
* **Add a queue label when PR is queued** :
    * Lorsqu'une pull request est mise en file d'attente pour la fusion, cette règle active ou désactive automatiquement le label "merge-queued".
    * Cela permet de marquer les pull requests qui attendent d'être fusionnées dans une file d'attente spécifique, facilitant ainsi leur suivi.
* **Ping PR author when conflicting** :
    * Si une pull request rencontre des conflits de fusion avec la branche de base, cette règle envoie automatiquement un message au créateur de la pull request pour l'avertir.
    * Cela permet d'informer rapidement les contributeurs sur la nécessité de résoudre les conflits avant que leur pull request ne puisse être fusionnée.
* **Make sure PR are up to date before merging** :
    * Cette règle garantit que les pull requests sont toujours à jour par rapport à la branche de base avant d'être fusionnées.
    * Elle automatise la mise à jour des pull requests qui sont devenues obsolètes en raison de modifications apportées à la branche de base, réduisant ainsi les risques de conflits de fusion.
* **Label conflicting pull requests** :
    * Lorsqu'une pull request rencontre des conflits de fusion, cette règle ajoute automatiquement le label "conflict" à la pull request.
    * Cela permet de signaler facilement les pull requests en conflit pour une gestion et une résolution ultérieures.

Ces règles personnalisées permettent de rationaliser et d'automatiser efficacement le processus de fusion des pull requests dans votre projet, en garantissant une gestion cohérente et fiable des contributions des développeurs, ainsi qu'une communication transparente sur l'état des pull requests.

**Conclusion**

Notre expérience avec les software bots en DevOps nous a permis de constater l'impact positif de l'automatisation dans le processus de développement logiciel. Dans notre cas, la gestion des dépendances et des merges représente une source de problèmes, c’est pourquoi Dependabot et Mergify sont des outils très intéressants à connaître. L'exploration des diverses possibilités offertes par les bots peut aider les équipes de développement à optimiser leur processus et à atteindre leurs objectifs plus rapidement et plus efficacement.
