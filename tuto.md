# Introduction
Nous avons choisi de travailler sur le projet dockerisé suivant : ![Site internet](https://github.com/SterennLeHir/Website). Il correspond à un site Internet recensant des associations rennaises avec leurs informations telles que les membres, les comptes rendus, etc.
Ce projet suit une architecture de micro-services (en mode production) avec : 

- une base de données
- un service du back-end
- un service de reverse-proxy (nginx) contenant les fichiers statiques du front-end

# Canary Testing

Pour le canary testing, on souhaite diviser les utilisateurs en plusieurs groupes. Chaque groupe va être dirigé sur une version particulière du projet.

# Fuzzing
