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
- En haut à gauche : couper le son. En haut à droite : le menu (volume, paramètres).
- **Une histoire ne vit que 24 heures** (3 h 12 → 3 h 12) : pas d'archives, pas de favoris, pas de sauvegarde.
- Le décor suit l'histoire : à chaque lieu, l'illustration change en fondu, avec sa teinte, son fond animé et son ambiance sonore (frigo, douche, voiture, horloge, néon, mouches…).
- Les gestes (« Regarder sous la porte », « Se retourner »…) montrent ce que le personnage voit, en plein écran. Les textos arrivent en notifications, avec le son, et s'empilent quand quelqu'un écrit sans arrêt.
- À la fin : la boîte « Un mot pour la prochaine nuit » (envoyée par courriel au créateur, qui décide quoi en faire).
- Saison : une histoire par nuit du 20 au 31 octobre 2026 (décompte avant, écran de fin après). Les histoires choisies par le créateur passent en premier.
- Mode DEV : Paramètres → toucher **3 fois** le numéro de version tout en bas → code secret.
