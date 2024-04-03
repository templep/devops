# Les bots dans le Dev-Ops

## Résumé des différents types de bot
//TODO faire le résumé très brefs des différents types de bot

## Présentation pratique des bots mit en place

### Renovate
#### Presentation
//TODO presenter renovate
#### Installation du bot
//TODO procédure d'installation
#### Actions realisés par le bot
//TODO résumer les actions
#### Critiques et bilan
La mission de ce bot est de suggérer des mise à jout de dépendances et c'est ce qu'il fait. Le problème est qu'il reste au dévelopeur de s'assurer que les modifications proposés seront compatibles avec le code déjà en place et ne causeront pas de bug. Afin d'automatiser ce processus, nous avons mit en place [l'automatisation des tests à chaque pull request](#Mise-en-place-d'automatisation-pour-une-pleine-intégration-des-bots-dans-la-boucle-DevOps).

### Dependabot


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
