# Les bots dans le Dev-Ops

## Résumé des différents types de bot
//TODO faire le résumé très brefs des différents types de bot

## Présentation pratique des bots mit en place par nos soins 
//TODO
| Nom du bot  | Type de bot             | Gratuit | Mise en place réussite |
|-------------|-------------------------|---------|------------------------|
| Renovate    | Gestion des dépendances | Oui     | Oui                    |
|             |                         |         |                        |
|             |                         |         |                        |

### Renovate
#### Presentation
Renovate est un bot qui va venir scanner le code et vérifier les versions des dépendances. Il va ensuite générer des pull request pour suggérer les modificiations de dépendances par les dernières versions disponible. C'est ensuite au développeur de s'assurer que la modification fonctionne pour son programme et de l'accepter si besoin.
#### Installation du bot
//TODO procédure d'installation
#### Actions realisés par le bot
Le bot nous a généré des pull request suggérants de mettre à jour des dépendances.
Un exemple est ci-dessous :
![image](https://github.com/ESIR2-S8-DevOps/devops/assets/49646276/0863419e-f6f7-43c1-a711-b2fd470d75b1)

#### Critiques et bilan
La mission de ce bot est de suggérer des mise à jout de dépendances et c'est ce qu'il fait. Le problème est qu'il reste au dévelopeur de s'assurer que les modifications proposés seront compatibles avec le code déjà en place et ne causeront pas de bug. Afin d'automatiser ce processus, nous avons mit en place [l'automatisation des tests à chaque pull request](#Mise-en-place-d'automatisation-pour-une-pleine-intégration-des-bots-dans-la-boucle-DevOps).

### Dependabot
#### Qu'est-ce que Dependabot ?
Dependabot est un outil d'automatisation de gestion des dépendances dans les projets logiciels. Il est conçu pour aider les développeurs à maintenir leurs projets à jour en surveillant les mises à jour des dépendances et en pull requests pour les mettre à jour automatiquement.

#### Fonctionnalités principales

- **Surveillance des dépendances** : Dependabot surveille les fichiers de configuration des dépendances (comme `package.json`) pour détecter les mises à jour disponibles.

- **Ouverture de pull requests** : Lorsqu'une mise à jour est détectée, Dependabot ouvre automatiquement une demande de tirage (pull request) avec les modifications nécessaires pour mettre à jour les dépendances.

- **Gestion des conflits** : Dependabot est capable de gérer les conflits de fusion (merge conflicts) qui peuvent survenir lors de la mise à jour des dépendances, en proposant des solutions ou en les résolvant automatiquement si possible.

- **Compatibilité avec divers écosystèmes** : Il prend en charge une grande variété de gestionnaires de dépendances, y compris npm, Bundler, Composer, Maven, etc.

- **Personnalisation** : Les utilisateurs peuvent personnaliser le comportement de Dependabot en définissant des politiques spécifiques, comme la fréquence des vérifications de dépendances ou les actions à prendre en cas de mise à jour.

#### Avantages

- **Maintien à jour** : En automatisant la gestion des dépendances, Dependabot permet de garder les projets logiciels à jour avec les dernières versions des bibliothèques et des frameworks.

- **Réduction de la dette technique** : En évitant l'accumulation de dépendances obsolètes, Dependabot aide à réduire la dette technique et à maintenir un code base sain et sécurisé.

- **Gain de temps** : En automatisant les tâches de gestion des dépendances, Dependabot libère du temps pour les développeurs, qui peuvent se concentrer sur des tâches plus créatives et à plus forte valeur ajoutée.

#### Mise en place et paramétrage avec GitHub

Pour activer Dependabot et le configurer sur un dépôt GitHub, il faut :

1. **Activer Dependabot** :
   - Accédez aux paramètres du dépôt sur GitHub.
   - Dans la section "Options", cliquez sur "Security & analysis".
   - Activez Dependabot pour le dépôt en cliquant sur "Activer".

2. **Configurer les fichiers de dépendances** :
   - Assurez-vous que les fichiers de configuration des dépendances de votre projet sont présents dans le dépôt (par exemple, `package.json` pour Node.js).
   - Si votre fichier de configuration des dépendances est dans un répertoire spécifique (comme `back/package.json`), assurez-vous de spécifier le chemin dans les paramètres de Dependabot.

3. **Personnaliser les politiques** :
   - Dans les paramètres de Dependabot sur GitHub, explorez les options de personnalisation disponibles.
   - Vous pouvez définir la fréquence des vérifications de dépendances, les règles d'acceptation des mises à jour, les dépendances à ignorer, etc.
   - Pour des politiques plus avancées, vous pouvez également utiliser un fichier de configuration Dependabot directement dans votre dépôt.

4. **Gérer les notifications** :
   - Configurez les notifications pour être informé des actions de Dependabot.
   - Vous pouvez choisir de recevoir des notifications par e-mail, sur la plateforme GitHub, ou via des intégrations avec des services de messagerie comme Discord.

Une fois que Dependabot est activé et configuré sur votre dépôt GitHub, il commencera à surveiller les mises à jour des dépendances et à ouvrir des demandes de tirage (pull requests) lorsque des mises à jour sont détectées. Vous pouvez toujours ajuster les paramètres et les politiques selon les besoins spécifiques de votre projet.


## Mise en place d'automatisation pour une pleine intégration des bots dans la boucle DevOps
#### Contexte
//TODO contexte de la mise en place
#### Presentation de GitHub Action
//TODO presentation de GitHub action
#### Mise en place d'une action
1. Nous créons un fichier github-actions.yaml dans le repertoire .github/workflows
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
3. Nous faisons ensuite un commit de ce fichier dans une nouvelle branche de notre choix
4. Nous faisons une pull request de notre nouvelle branche
5. Dans l'interface de notre Repository nous cliquons sur le module *Actions*
6. Nous cliquons sur le menu de gauche, plus précisément sur *GitHub Actions Elie*
//TODO mettre pleins de jolies photos

#### critiques et bilan
//TODO expliquer
