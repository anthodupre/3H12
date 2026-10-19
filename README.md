# 3h12

Une histoire d'horreur inédite chaque nuit, à 3 h 12 (heure de Toronto).
Chaque histoire apporte son propre décor : mise en page, couleurs, polices, fond animé, illustration et musique synthétisée.

## Comment ça marche

- `index.html` : le moteur (HTML + CSS + JavaScript, sans dépendance). Il ne change jamais.
- `stories/AAAA-MM-JJ.json` : une histoire par jour. `stories/index.json` : la liste de toutes les histoires.
- `SCHEMA.md` : la référence complète du format JSON.
- `DAILY.md` : les instructions suivies chaque nuit (3 h 12) par l'automatisation. Elle garde **une nuit d'avance** : l'histoire du jour sort à 3 h 12, et celle du lendemain est déjà écrite (cachée dans l'app jusqu'à sa sortie), avec un aperçu envoyé dans la conversation Claude.

## Publication

Le site est servi par **GitHub Pages** depuis la branche `main` (dossier racine).
`.nojekyll` empêche GitHub de transformer les fichiers Markdown.

## Dans l'app

- Toucher l'écran d'entrée pour entrer (ça débloque aussi le son sur iPhone).
- En haut à gauche : couper le son. En haut à droite : le menu (volume, archives, paramètres).
- Paramètres → **Sauvegarder**, **Exporter** / **Importer** (code texte à garder en lieu sûr).
- Mode DEV : Paramètres → toucher **3 fois** le numéro de version tout en bas → code secret.
