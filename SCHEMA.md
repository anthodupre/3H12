# SCHEMA.md — format des fichiers de « 3h12 »

Référence complète du format JSON lu par le moteur (`index.html`).
L'automatisation quotidienne doit suivre ce document **à la lettre**.

- Encodage : **UTF-8**. JSON **strict** : guillemets doubles, aucune virgule finale, aucun commentaire.
- Les clés sont en **camelCase français** (`miseEnPage`, `phraseFinale`, `sousGenre`…), exactement comme écrites ici.
- Le moteur est tolérant (un champ manquant ou invalide reçoit une valeur par défaut et un avertissement en mode DEV), mais **un fichier conforme est la norme** : ne compte pas sur la tolérance.
- Dates au format `AAAA-MM-JJ`, fuseau **America/Toronto**.
- Couleurs au format hexadécimal **`#rrggbb`** uniquement (pas de `rgb()`, pas de noms de couleurs).

Sommaire : [1. Fichiers](#1-fichiers) · [2. index.json](#2-storiesindexjson) · [3. Histoire : champs racine](#3-histoire--champs-racine) · [4. theme](#4-theme) · [5. document](#5-document) · [6. Mises en page](#6-mises-en-page-mise-en-page) · [7. fond](#7-fond) · [8. musique](#8-musique) · [9. blocs](#9-blocs) · [10. style](#10-style-des-blocs) · [11. Balisage](#11-balisage-du-texte) · [12. événements](#12-événements) · [13. Robustesse](#13-robustesse-et-mode-dev) · [14. Exemple complet](#14-exemple-complet)

---

## 1. Fichiers

| Fichier | Rôle |
|---|---|
| `stories/index.json` | Liste de toutes les histoires (une entrée par jour). |
| `stories/AAAA-MM-JJ.json` | Une histoire. Le nom du fichier **est** sa date (ex. `stories/2026-10-19.json`). |

**24 heures, pas plus** : une histoire datée J apparaît dans l'app à **J, 3 h 12** (heure de Toronto) et **disparaît à J+1, 3 h 12**, remplacée par la suivante. Il n'y a ni archives, ni favoris, ni sauvegarde : une histoire manquée est perdue. On peut pousser l'histoire du lendemain d'avance : elle reste cachée jusqu'à sa sortie. Si l'histoire de la nuit manque, l'app n'en montre **pas** une plus ancienne : elle affiche « Silence radio » (« Rien cette nuit. Reviens à 3 h 12. »). Les histoires passées et à venir ne s'ouvrent qu'en mode DEV (en aperçu).

---

## 2. `stories/index.json`

```json
{
  "version": 1,
  "histoires": [
    {
      "date": "2026-10-18",
      "numero": 1,
      "titre": "Sept Pâques",
      "sousGenre": "folklore québécois",
      "lieu": "Rang Saint-Isidore, Saint-Aurèle-des-Monts (Lanaudière)",
      "auQuebec": true,
      "epoque": "1987",
      "format": "Rapport d’événement de police, avec déclaration de témoin",
      "miseEnPage": "rapport",
      "tonique": "D",
      "gamme": "phrygien",
      "effetFond": "neige",
      "palette": { "fond": "#0a0d12", "surface": "#e7e3d6", "texte": "#1f1d1a", "accent": "#a3201c", "secondaire": "#27407a" },
      "polices": { "titre": "Special Elite", "texte": "Courier Prime", "notes": "Reenie Beanie" },
      "evenements": 6,
      "retournement": "la malédiction se transmet par la parole : le rapport lui-même la propage"
    }
  ]
}
```

| Champ | Type | Obligatoire | Description |
|---|---|---|---|
| `version` | nombre | oui | Toujours `1`. |
| `histoires` | tableau | oui | Une entrée par histoire, **triée par date croissante**, aucune date en double. |
| `date` | chaîne `AAAA-MM-JJ` | oui | Date de l'histoire = nom du fichier. |
| `numero` | entier | oui | Numéro de l'histoire (1, 2, 3…). Toujours le plus grand numéro existant + 1. |
| `titre` | chaîne | oui | Même titre que dans le fichier de l'histoire (balisage permis mais déconseillé). |
| `sousGenre` | chaîne | oui | Même valeur que dans l'histoire (voir la liste dans DAILY.md). |
| `lieu` | chaîne | oui | Même valeur que dans l'histoire. |
| `auQuebec` | booléen | oui | `true` si l'histoire se passe au Québec (sert à respecter « environ 1 sur 3 »). |
| `epoque` | chaîne | oui | Année ou période (« 1987 », « hiver 1847 », « aujourd'hui »). |
| `format` | chaîne | oui | Description humaine du document (« Journal intime d'une infirmière », « Fil de forum de 2004 »…). |
| `miseEnPage` | chaîne | oui | Une des 10 mises en page (section 6). |
| `tonique` | chaîne | oui | Tonique de la musique (section 8). |
| `gamme` | chaîne | oui | Gamme de la musique (section 8). |
| `effetFond` | chaîne | oui | Effet de fond (section 7). |
| `palette` | objet | oui | Copie exacte de `theme.palette` de l'histoire (5 couleurs). Sert à varier les palettes d'une nuit à l'autre. |
| `polices` | objet | oui | Copie de `theme.polices` (titre, texte, notes). |
| `evenements` | entier | oui | Nombre de blocs `evenement` dans l'histoire. |
| `retournement` | chaîne | oui | Même valeur que dans l'histoire (sert à varier les retournements). |

Ces champs servent aussi à l'automatisation pour éviter de se répéter : **ils doivent être exacts**.

---

## 3. Histoire : champs racine

Fichier `stories/AAAA-MM-JJ.json` :

| Champ | Type | Obligatoire | Défaut si absent | Description |
|---|---|---|---|---|
| `version` | nombre | oui | `1` | Toujours `1`. |
| `numero` | entier ≥ 1 | oui | `0` | Numéro de l'histoire (identique à l'index). |
| `date` | `AAAA-MM-JJ` | oui | nom du fichier | Doit être égal au nom du fichier. |
| `titre` | chaîne | oui | « Sans titre » | Court et marquant (1 à 5 mots, idéalement). |
| `sousTitre` | chaîne | non | — | Affiché sous le titre (carton d'ouverture, chapeau d'une coupure de journal). |
| `sousGenre` | chaîne | oui | — | Ex. « horreur maritime ». |
| `lieu` | chaîne | oui | — | Lieu précis (ville, région, pays). |
| `epoque` | chaîne | oui | — | Année ou période. |
| `auQuebec` | booléen | oui | — | Comme dans l'index. |
| `format` | chaîne | oui | — | Description humaine du document. |
| `resume` | chaîne | oui | — | Une phrase de résumé (sans divulgâcher la fin). |
| `avertissement` | chaîne | non | — | Court avertissement de contenu, affiché sur le carton d'ouverture (ex. « Contient la mort d'un animal. »). |
| `retournement` | chaîne | oui | — | Type du gros retournement final, en quelques mots (ex. « celui qui rassure est la menace ; transmission »). Jamais affiché dans l'app : sert à ne pas répéter le même retournement (voir DAILY.md). |
| `miseEnPage` | chaîne | oui | `recit` | Une des 10 mises en page (section 6). |
| `theme` | objet | oui | selon la mise en page | Palette, polices, CSS (section 4). |
| `document` | objet | selon la mise en page | — | Métadonnées du « document » affiché (section 5). |
| `fond` | objet | oui | aucun effet | Fond animé et illustration (section 7). |
| `musique` | objet | oui | drone + bruit | Musique synthétisée (section 8). |
| `blocs` | tableau | oui | — | Le texte, bloc par bloc (section 9). |
| `phraseFinale` | chaîne | oui | — | La dernière phrase, affichée seule, en grand, après l'histoire. Balisage permis. |
| `question` | objet | oui (DAILY) | aucune | La **question de la nuit**, posée au lecteur à la fin, au-dessus de la boîte à suggestions : `{ "texte": "…", "choix": ["…", "…"] }`, **2 à 4 choix** courts. Elle sert à orienter les prochaines histoires (une peur, un lieu, un format, une époque…) sans rien gâcher de celle-ci. Balisage permis. Absente ou invalide → seulement la boîte « Un mot pour la prochaine nuit ». |

**Fin de l'histoire** : après la `phraseFinale`, l'app affiche « Fin », la date de la prochaine histoire, « Cette histoire disparaît à 3 h 12. », puis la **boîte à suggestions** (la `question` avec ses choix, et un champ libre « Un mot pour la prochaine nuit »). Les réponses partent par courriel (FormSubmit) au créateur de 3h12, qui décide quoi en faire ; elles ne modifient rien automatiquement.

**Durée de lecture** : calculée automatiquement (190 mots/min) à partir de tous les textes. Visez **900 à 1600 mots**.

---

## 4. `theme`

```json
"theme": {
  "palette": { "fond": "#0a0d12", "surface": "#e7e3d6", "texte": "#1f1d1a", "accent": "#a3201c", "secondaire": "#27407a" },
  "polices": { "titre": "Special Elite", "texte": "Courier Prime", "notes": "Reenie Beanie" },
  "cssPersonnalise": ".rp-objet { letter-spacing: .02em; }"
}
```

### 4.1 `palette` (5 couleurs `#rrggbb`, toutes obligatoires)

| Clé | Rôle |
|---|---|
| `fond` | Couleur de la page, derrière tout (autour du papier, du téléphone…). Aussi le fond des menus, archives et paramètres. Presque toujours très sombre. |
| `surface` | Couleur de l'**objet** : papier, écran du téléphone, fenêtre du forum, écran cathodique. Pour `recit`, mettre la **même valeur que `fond`** (le texte est posé directement sur le fond). |
| `texte` | Couleur du texte **sur la surface** (l'encre sur le papier, le phosphore sur l'écran). |
| `accent` | Couleur forte : tampons, liens, `__accent__`, bulles « moi » en SMS, lettrine, horloge de l'écran d'entrée. |
| `secondaire` | Couleur d'appoint : notes manuscrites (stylo), bulles « eux » en SMS, bannière du forum, horodatages. |

**Règles de contraste (à respecter) :**
- `texte` sur `surface` : rapport de contraste **≥ 7** (minimum absolu 4,5).
- `accent` sur `surface` : ≥ 3. `secondaire` sur `surface` : ≥ 3.
- En SMS : `accent` et `secondaire` sont des fonds de bulles ; le moteur choisit automatiquement du texte blanc ou noir dessus.
- Le moteur corrige automatiquement une couleur illisible, mais le résultat est moins beau : choisissez bien.
- Le carton d'ouverture et la fin sont écrits sur `fond` : si `texte` y est illisible, le moteur utilise `surface` (le papier) comme couleur d'écriture.

### 4.2 `polices`

| Clé | Rôle | Défaut |
|---|---|---|
| `titre` | Titres, carton d'ouverture, phrase finale, liens de fin, menus. | selon la mise en page |
| `texte` | Corps du texte. Doit rester **très lisible** sur un iPhone. | selon la mise en page |
| `notes` | Notes manuscrites, signatures, adresse d'enveloppe. | selon la mise en page |
| `secours` | (optionnel) `"serif"`, `"sans-serif"`, `"monospace"` ou `"cursive"` : famille de secours si la police ne charge pas. Sinon devinée. | deviné |

Valeurs permises : le **nom exact** d'une police Google Fonts (sensible à la casse et aux espaces), ou une police système d'iPhone (`system-ui`, `Georgia`, `Verdana`, `Courier New`, `American Typewriter`, `Didot`, `Baskerville`, `Noteworthy`, `Bradley Hand`, `Snell Roundhand`, `Marker Felt`, `Menlo`, `Trebuchet MS`).

Polices Google **vérifiées** (toutes existent) :

- **Sérif / livre** : EB Garamond, Cormorant Garamond, Cormorant, IM Fell English, IM Fell English SC, IM Fell DW Pica, Libre Baskerville, Crimson Pro, Lora, Playfair Display, Old Standard TT, Spectral, Cardo, Vollkorn, Gelasio, Alegreya, Cinzel, Prata, Abril Fatface, Bodoni Moda, Literata, Newsreader, Fraunces, Source Serif 4, Libre Caslon Text, Sorts Mill Goudy
- **Gothique / ancien** : UnifrakturMaguntia, UnifrakturCook, Pirata One, Grenze Gotisch, Almendra, Uncial Antiqua, MedievalSharp
- **Machine à écrire / terminal** : Special Elite, Courier Prime, IBM Plex Mono, Space Mono, VT323, Share Tech Mono, Cutive Mono, Major Mono Display, Anonymous Pro, Inconsolata, Source Code Pro, JetBrains Mono, Press Start 2P, Silkscreen
- **Manuscrite** : Caveat, Kalam, Homemade Apple, Reenie Beanie, La Belle Aurore, Mrs Saint Delafield, Pinyon Script, Dawning of a New Day, Cedarville Cursive, Nothing You Could Do, Shadows Into Light, Gochi Hand, Patrick Hand, Covered By Your Grace, Just Another Hand, Zeyada, Sue Ellen Francisco, Waiting for the Sunrise, Rock Salt, Nanum Pen Script, Mynerve
- **Sans-sérif** : Inter, Work Sans, IBM Plex Sans, Archivo Narrow, Oswald, Bebas Neue, Anton, Barlow Condensed, Josefin Sans, Karla, DM Sans, Manrope, Space Grotesk, Syne
- **À éviter** (trop « Halloween ») : Creepster, Nosifer, Eater, Butcherman.

Conseils : une police manuscrite ou gothique **jamais** pour `texte` sur plus de quelques lignes (sauf `journal_intime` avec Kalam, Caveat ou Patrick Hand, qui restent lisibles). Préférez des polices qui ont l'italique.

### 4.3 `cssPersonnalise` (optionnel)

Chaîne de CSS injectée dans la page. **Chaque sélecteur est automatiquement préfixé par `.histoire`** : impossible de casser l'interface. `html`, `body` et `:root` deviennent `.histoire`. Les `@media`, `@supports` et `@keyframes` sont permis ; `@import` est retiré.

À utiliser avec parcimonie (petits ajustements), par exemple :
```css
.titre-bloc { letter-spacing: .3em; }
.bloc-paragraphe:nth-of-type(7) p { color: #8a0f0f; }
@keyframes pulse { from { opacity: .4 } to { opacity: 1 } }
.carton-titre { animation: pulse 3s infinite alternate; }
```

Classes utiles : `.carton`, `.carton-titre`, `.objet` (la feuille/le téléphone), `.bloc`, `.bloc-paragraphe`, `.bloc-dialogue`, `.titre-bloc`, `.note`, `.tampon`, `.bulle`, `.fin-phrase`, et le préfixe de mise en page `.mep-rapport`, `.mep-sms`, etc.

---

## 5. `document`

Métadonnées du document « physique » (en-tête du rapport, nom du contact SMS, journal de la coupure…). Toutes les clés sont des chaînes optionnelles, sauf `champs` (tableau).

| Clé | Utilisée par | Rôle |
|---|---|---|
| `organisme` | rapport, transcription, forum, terminal, coupure, lettre | Service de police, nom du site, nom du journal, nom du système… |
| `typeDocument` | rapport, transcription, terminal | « Rapport d'événement », « Transcription d'enregistrement »… |
| `reference` | rapport, transcription, forum, terminal, sms | N° de dossier, nom du fichier audio, n° de session ; en SMS : numéro de téléphone affiché sous le nom. |
| `classification` | rapport | Tampon en haut du rapport (« Confidentiel »). |
| `dateDocument` | rapport, transcription, terminal, journal_intime, lettre, coupure | Date affichée dans l'en-tête ; cachet de la poste (lettre). |
| `auteurDocument` | rapport, transcription, terminal, coupure, journal_intime | Rédigé par / transcrit par / signature de l'article / propriétaire du journal intime (« Journal de *auteurDocument* » : mettre un prénom). |
| `destinataire` | rapport, lettre | Destinataire ; en `lettre`, l'**adresse sur l'enveloppe** (lignes séparées par `\n`). Sans destinataire, pas d'enveloppe. |
| `contact` | sms | Nom affiché en haut de la conversation. Défaut : le `nom` des messages « eux ». |
| `sujet` | rapport, transcription, forum, coupure | Objet du rapport, titre du fil de forum, manchette du journal. Défaut : `titre`. |
| `section` | forum, coupure | Section du forum (« Témoignages »), rubrique du journal (« Faits divers »). |
| `slogan` | forum | Sous-titre du site. |
| `duree` | transcription | Durée de l'enregistrement (« 00:41:07 »). |
| `champs` | rapport, transcription | Lignes supplémentaires de l'en-tête : `[{ "nom": "Nature", "valeur": "Disparition" }]`. |

---

## 6. Mises en page (`miseEnPage`)

10 valeurs permises. Chaque mise en page a son objet visuel, ses couleurs et ses polices par défaut, et donne un sens particulier à certains blocs.

| Valeur | Objet visuel | Sens particulier des blocs | Polices par défaut |
|---|---|---|---|
| `manuscrit` | Page de papier jauni, taches, encre ; titre manuscrit souligné. | `note` = annotation à l'encre rouge dans la marge ; `~~rature~~` ondulée. | IM Fell English SC / IM Fell English / Homemade Apple |
| `journal_intime` | Cahier ligné, marge rouge, trous de reliure, étiquette « Journal de… ». | `titre` = **date en tête de section** (« Mardi 3 mars, 2 h du matin ») ; `note` = au crayon. | Caveat / Kalam / Caveat |
| `rapport` | Formulaire officiel : sceau, organisme, tableau de champs, tampon, sections numérotées. | `titre` = section numérotée (« 1. SOMMAIRE ») ; `dialogue` = interrogatoire « NOM : réplique » ; `note` = annotation au stylo d'un supérieur ; `\|\|caviardé\|\|` = barre noire ; `tampon`, `champs`, `signature`. | Special Elite / Courier Prime / Reenie Beanie |
| `sms` | Téléphone, en-tête du contact (reste en haut), bulles, heures, « Vu ». Les messages arrivent **un à un** avec « … » qui tape. | `sms` = bulles ; `dialogue` = bulle « eux » ; `paragraphe`/`note`/`titre` = texte système gris centré ; `pause` = « … » fantôme qui tape puis disparaît. | system-ui |
| `transcription` | Document de transcription : forme d'onde, « ● ENR », fichier, durée, liste des intervenants (automatique). | `dialogue` = ligne `[00:03:12] NOM` puis la réplique (utiliser `horodatage`) ; `paragraphe` = note du transcripteur en italique ; `[inaudible]` stylé ; `note` = « N.D.T. ». | IBM Plex Mono ×3 |
| `forum` | Vieux forum des années 2000 : bannière, fil d'Ariane, sujet verrouillé, messages avec avatar en pixels, rang, compteur. | `dialogue` = **un message** (champs `heure`, `rang`, `messages`, `signature`, `avatar`) ; `paragraphe` = avis de modération encadré ; `citation` = « X a écrit : » ; `pause` = changement de page ; `note` = « édité par… ». | Verdana (système) |
| `lettre` | Correspondance ancienne : papier crème plié en trois, enveloppe avec timbre et cachet si `document.destinataire`. | **chaque `pause` commence une nouvelle feuille** (une nouvelle lettre) ; `titre` = lieu et date en haut à droite ; `signature` = signature calligraphiée ; `note` = « P.-S. ». | Pinyon Script / Cormorant Garamond / Pinyon Script |
| `recit` | Livre classique : texte justifié, alinéas, **lettrine** automatique, chapitres en petites capitales. | `titre` = chapitre ; `pause` = ❦ ; `dialogue` = « Nom — réplique ». | Cormorant Garamond / EB Garamond |
| `terminal` | Écran cathodique vert : lignes de balayage, lueur, texte qui **se tape tout seul**, en-tête de session. | `paragraphe` = « > … » ; `titre` = commande « $ … » ; `dialogue` = « <Nom> … » ; `note` = commentaire « # … ». | VT323 ×3 |
| `coupure` | Coupure de journal : bords déchirés, ruban adhésif, nom du journal en gothique, manchette, chapeau, signature, lettrine. | **chaque `pause` commence une nouvelle coupure** ; `titre` = intertitre ; images en trame d'imprimerie. | Playfair Display / Old Standard TT |

Palettes par défaut (utilisées si `theme.palette` est absent ; à titre d'inspiration) :

| Mise en page | fond | surface | texte | accent | secondaire |
|---|---|---|---|---|---|
| manuscrit | #0e0b07 | #e3d6b4 | #2b1d10 | #8a2416 | #6b5a40 |
| journal_intime | #0b0d12 | #f1ede0 | #1f2a44 | #a3322a | #5d6b85 |
| rapport | #0a0c10 | #e8e5da | #1d1c1a | #a11d1d | #26407c |
| sms | #000000 | #0c0c0e | #f2f2f7 | #0a84ff | #2a2a2e |
| transcription | #08090b | #eeede8 | #1a1a1a | #b0221a | #5b5f66 |
| forum | #06070a | #15171d | #c9cdd6 | #e0a43c | #2d3a5c |
| lettre | #0d0a08 | #ece2c9 | #2a2018 | #7d1f2a | #5e4b37 |
| recit | #0b0a0a | #0b0a0a | #d9d2c5 | #b23a2e | #77706a |
| terminal | #020403 | #040b06 | #7dff9a | #e8ff7a | #3f9d55 |
| coupure | #0c0b0a | #e2dac6 | #1c1a17 | #7a1d12 | #5c564a |

---

## 7. `fond`

```json
"fond": {
  "effet": "neige", "intensite": 0.42, "vignette": 0.8, "grain": 0.35, "scintillement": 0.12,
  "svgOpacite": 0.24, "couleur": "#e8eef5",
  "svg": "<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 400 860'>…</svg>"
}
```

| Clé | Type | Défaut | Description |
|---|---|---|---|
| `effet` | chaîne | `aucun` | Effet animé plein écran (liste ci-dessous). |
| `intensite` | 0 à 1 | 0.5 | Quantité / force de l'effet. 0.3 à 0.6 = ambiance ; plus de 0.8 = envahissant. |
| `vignette` | 0 à 1 | 0.6 | Assombrissement des bords de l'écran. |
| `grain` | 0 à 1 | 0.25 | Grain de film animé. |
| `scintillement` | 0 à 1 | 0 | Baisses de lumière aléatoires (ampoule fatiguée). 0.1 à 0.3 = subtil. Adouci si l'utilisateur coupe les effets intenses. |
| `svg` | chaîne | — | **Illustration originale** affichée derrière le texte, semi-transparente (voir ci-dessous). |
| `svgOpacite` | 0 à 1 | 0.22 | Opacité de l'illustration pendant la lecture (elle est ~2× plus visible sur le carton d'ouverture). 0.15 à 0.3 conseillé. |
| `couleur` | `#rrggbb` | selon l'effet | (optionnel) Couleur des particules. |

**Effets** : `pluie`, `neige`, `brouillard`, `poussiere` (grains dans un rayon de lumière), `braises` (orange, montent), `statique` (neige de télé), `lucioles`, `cendres` (tombent en tournoyant, quelques-unes rougeoient), `etoiles` (scintillent, étoile filante), `bulles` (sous l'eau), `aucun`.

**Règles pour `svg`** (illustration de fond) :
- Un seul élément `<svg>` complet avec `xmlns='http://www.w3.org/2000/svg'`.
- `viewBox='0 0 400 860'` (format portrait de l'iPhone). Ajouter `preserveAspectRatio='xMidYMax slice'` pour ancrer le paysage en bas.
- **Utiliser des apostrophes simples `'` pour les attributs** (le SVG est dans une chaîne JSON entre guillemets doubles).
- Interdits : `<script>`, `<foreignObject>`, attributs `on…`, liens externes (`href` vers http), images matricielles, `<text>` (le texte n'a rien à faire dans le décor). Ils sont supprimés automatiquement de toute façon.
- Les `id` (dégradés, filtres) sont renommés automatiquement : aucun risque de conflit.
- Style : silhouettes, aplats sombres, dégradés, **une ou deux sources de lumière** (fenêtre, lune, phare), un détail inquiétant discret (silhouette, yeux, porte entrouverte). Pas de photoréalisme. Taille : **5 à 15 Ko**, maximum 20 Ko.
- Le ciel/fond du SVG doit aller avec `palette.fond`.

---

## 8. `musique`

Tout est synthétisé en direct (Web Audio). Aucun fichier audio.

```json
"musique": {
  "tempo": 54, "tonique": "D", "gamme": "phrygien",
  "reverb": 0.72, "desaccord": 0.28, "evolution": "monte", "rupture": 0.75, "finale": "fondu", "volume": 1,
  "couches": [
    { "type": "drone", "onde": "sawtooth", "octave": 2, "volume": 0.5, "filtre": 340, "densite": 0.6 },
    { "type": "bruit", "volume": 0.38, "filtre": 700, "densite": 0.75 },
    { "type": "pulsation", "onde": "sine", "octave": 2, "volume": 0.42, "filtre": 420, "densite": 0.5, "seuil": 0.72 }
  ]
}
```

| Clé | Type | Défaut | Description |
|---|---|---|---|
| `tempo` | 30 à 180 | 60 | Battements par minute (40-70 = lent et lourd ; 90+ = anxiété). |
| `tonique` | chaîne | `D` | Note de base : `C`, `C#`, `Db`, `D`, … `B` (ou Do, Ré, Mi, Fa, Sol, La, Si, avec `#`/`b`). |
| `gamme` | chaîne | `mineur` | Voir la liste ci-dessous. |
| `couches` | tableau | drone + bruit | **2 à 6 couches** (8 maximum). |
| `reverb` | 0 à 1 | 0.6 | Taille de la réverbération (0.8+ = cathédrale, caverne). |
| `desaccord` | 0 à 1 | 0.15 | Désaccord et « pleurage » de bande : 0.1 = propre ; 0.3 = malsain ; 0.6 = détraqué. |
| `evolution` | chaîne | `monte` | Comment l'intensité suit la lecture : `monte` (crescendo), `stable`, `descend`, `rupture` (monte puis s'effondre net à `rupture`). |
| `rupture` | 0.1 à 0.95 | 0.75 | Position (0 = début, 1 = fin) de la rupture si `evolution` = `rupture`. |
| `finale` | chaîne | `fondu` | À la phrase finale : `fondu` (baisse à 35 %), `silence` (s'éteint), `continuer`. |
| `volume` | 0 à 1 | 1 | Volume global de la musique de cette histoire. |

**Gammes** : `mineur`, `phrygien` (sombre, espagnol/ancien), `locrien` (le plus instable), `chromatique` (chaos), `pentatonique_mineure` (folklorique, simple), `ton_par_ton` (rêve, irréel), `harmonique_mineure` (gothique, oriental), `dorien` (mélancolique), `diminuee` (cauchemar, tension), `hongroise_mineure` (étrange, ancien), `phrygien_dominant` (rituel, désert), `lydien` (merveilleux faussé), `majeur` (innocence… qui dérange avec du désaccord, ex. boîte à musique).

**Couches** — chaque couche :

| Clé | Type | Défaut | Description |
|---|---|---|---|
| `type` | chaîne | — | Voir le tableau suivant. |
| `onde` | `sine`, `triangle`, `sawtooth`, `square` | selon le type | Timbre : sine = pur ; triangle = doux ; sawtooth = riche, râpeux ; square = creux. |
| `octave` | 0 à 7 | selon le type | 1-2 = grave ; 3-4 = médium ; 5-6 = aigu. |
| `volume` | 0 à 1 | 0.5 | Volume de la couche (équilibré automatiquement selon le type). |
| `filtre` | 60 à 18000 (Hz) | selon le type | Fréquence de coupure : bas = étouffé, lointain ; haut = brillant. S'ouvre avec l'intensité. |
| `densite` | 0 à 1 | 0.5 | Combien de notes jouent (ou d'activité). |
| `seuil` | 0 à 1 | 0 | La couche n'entre que lorsque l'intensité atteint ce seuil (ex. 0.7 : arrive vers la fin). Idéal pour construire la tension. |
| `pan` | -1 à 1 | 0 | Gauche / droite. |

| `type` | Rôle | Réglages typiques |
|---|---|---|
| `drone` | Notes tenues graves qui glissent rarement (la base de presque tout). Densité = nombre de voix (racine, quinte, octave, seconde dissonante). | sawtooth, octave 1-2, filtre 250-600 |
| `pad` | Accords lents qui changent toutes les deux mesures. | triangle, octave 3, filtre 900-2000 |
| `cloche` | Tintements métalliques épars (cloche d'église, carillon lointain). | octave 4-5, densite 0.1-0.3 |
| `pulsation` | Battement de cœur grave (« poum-poum »). Utiliser `seuil` pour qu'il arrive plus tard. | sine, octave 1-2, seuil 0.5-0.8 |
| `bruit` | Souffle continu avec rafales : vent, mer, grondement, ventilation. | filtre 400-1500, densite = force des rafales |
| `boite_a_musique` | Petite mélodie répétée et faussée, qui mute lentement. | octave 5-6, densite 0.4-0.7 ; avec `majeur` ou `lydien` + désaccord = très inquiétant |
| `cordes` | Section de cordes, longues notes ; **trémolo** automatique quand l'intensité dépasse 0.55. | sawtooth, octave 3, filtre 800-1600 |
| `choeur` | Voix sans paroles (« aaah », « ooouh »). Culte, église, cosmique. | octave 3, volume 0.15-0.3 |
| `piano` | Notes isolées d'un vieux piano désaccordé. | octave 3-5, densite 0.2-0.5 |

Recettes : **hivernal** = drone + bruit (vent) + cloche rare ; **cosmique** = drone + choeur + pad en `ton_par_ton`, reverb 0.9 ; **maison hantée** = boite_a_musique (`majeur`, desaccord 0.4) + drone + piano ; **technologique** = drone square + bruit filtre haut + pulsation ; **maritime** = bruit (filtre 500, densité 0.9) + cordes + cloche (bouée).

---

## 9. `blocs`

Tableau ordonné. Chaque bloc a un `type` et, optionnellement, un `style` (section 10). Tout champ texte accepte le **balisage** (section 11) et `\n` (retour à la ligne) ; dans `paragraphe`, `citation` et les répliques de forum/transcription, `\n\n` crée un nouveau paragraphe.

| `type` | Champs | Rendu |
|---|---|---|
| `paragraphe` | `texte` | Paragraphe(s) de narration. |
| `titre` | `texte` | Titre de section (sens selon la mise en page). |
| `dialogue` | `personnage`, `texte` ; optionnels : `horodatage` (transcription : « 00:03:12 »), `heure` (forum : date de publication ; sms : heure), `rang`, `messages`, `signature`, `avatar` (forum) | Réplique. |
| `sms` | `de` (`"moi"` ou `"eux"`), `texte` ; optionnels : `nom` (expéditeur « eux », affiché s'il y a plusieurs correspondants), `heure` (affichée quand elle change), `vu` (`true` ou « Vu à 03:14 »), `statut` (`"non_distribue"`) | Bulle de message. Fonctionne dans toutes les mises en page, mais c'est le cœur de `sms`. |
| `note` | `texte` | Note griffonnée (police `notes`, couleur secondaire). |
| `citation` | `texte`, `source` (optionnel) | Citation en retrait. |
| `pause` | `symbole` (optionnel) | Séparateur (⁂, ❦, — — —…). Avec `"symbole": "page"` : commence une nouvelle feuille, dans n'importe quelle mise en page. En `lettre` et `coupure`, **toute** pause commence une nouvelle feuille. |
| `evenement` | `effet`, `son`, `duree`, `nombre`, `texte` | Invisible ; se déclenche une seule fois au défilement (section 12). |
| `image` | `svg`, `legende` (optionnel) | Illustration SVG dans le texte (croquis, plan, photo de pièce à conviction…). `viewBox` paysage conseillé (ex. `0 0 320 200`). Utiliser `currentColor` ou les couleurs de la palette. Mêmes règles de sécurité que le SVG de fond. |
| `signature` | `texte` | Signature manuscrite alignée à droite. |
| `champs` | `titre` (optionnel), `champs` : `[{ "nom": "…", "valeur": "…" }]` | Tableau de champs officiels (formulaire, fiche). |
| `tampon` | `texte`, `rotation` (-30 à 30, optionnel) | Tampon encré (« REÇU », « CLASSÉ », « NE PAS DIFFUSER »). |

Exemples :
```json
{ "type": "titre", "texte": "Mardi 3 mars — 2 h 40" },
{ "type": "paragraphe", "texte": "Premier paragraphe.\n\nDeuxième paragraphe du même bloc." },
{ "type": "dialogue", "personnage": "MARIE", "texte": "T'as-tu entendu ça ?", "horodatage": "00:03:12" },
{ "type": "sms", "de": "eux", "nom": "Maman", "heure": "03:12", "texte": "Es-tu réveillé ?" },
{ "type": "sms", "de": "moi", "texte": "Oui. Pourquoi ?", "vu": "Vu à 03:13" },
{ "type": "dialogue", "personnage": "nightcrawler_77", "heure": "14 mars 2004, 03:12", "rang": "Membre", "messages": "212", "signature": "Ne réponds pas quand ça cogne.", "texte": "Quelqu'un d'autre l'a vue ?" },
{ "type": "champs", "titre": "Personne disparue", "champs": [{ "nom": "Nom", "valeur": "LAFOND, Gérard" }, { "nom": "Âge", "valeur": "52 ans" }] },
{ "type": "tampon", "texte": "Classé", "rotation": -8 },
{ "type": "pause" },
{ "type": "image", "legende": "Annexe A — plan du sous-sol.", "svg": "<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 320 200'>…</svg>" }
```

---

## 10. `style` des blocs

Optionnel, sur n'importe quel bloc : une chaîne de mots séparés par des espaces (ou un tableau).

| Style | Effet |
|---|---|
| `centre` / `droite` | Alignement. |
| `gros` / `petit` | Taille (×1,45 / ×0,82). |
| `agite` | Chaque mot tremble nerveusement (panique, écriture qui dérape). |
| `efface` | Très pâle, comme effacé. |
| `accent` | Tout le bloc en couleur accent. |
| `majuscules` | En majuscules espacées. |
| `espace` | Grand espace avant le bloc (pour isoler une phrase). |
| `lent` | Apparition très lente (2,8 s). |

Exemple : `{ "type": "paragraphe", "texte": "Il est encore là.", "style": "centre gros lent" }`

---

## 11. Balisage du texte

| Balisage | Effet |
|---|---|
| `*texte*` | *Italique*. |
| `##texte##` | Chuchoté : petit, pâle, chaque mot tremble légèrement. |
| `\|\|texte\|\|` | Caviardé : barre noire, révélé quand on le touche. Le lecteur peut ne jamais le voir : n'y cachez pas une information **indispensable**. |
| `%%texte%%` | Glitch : les caractères se brouillent par moments. |
| `^^texte^^` | Apparaît lettre par lettre, lentement (quand le bloc entre à l'écran). |
| `__texte__` | Couleur accent. |
| `~~texte~~` | Rature (barré ; ondulé en manuscrit et journal). |
| `[texte]` | Annotation entre crochets, en italique pâle (ex. `[inaudible]`, `[silence]`, `[rires]`). |
| `\n` | Retour à la ligne. `\n\n` = nouveau paragraphe (dans `paragraphe`). |

- Les balises ne se chevauchent pas ; `%%…%%` et `^^…^^` contiennent du texte simple (pas d'autre balise dedans).
- Chaque balise doit être **fermée** (nombre pair de `##`, `||`, `%%`, `^^`, `__`, `~~`).
- Parcimonie : quelques effets bien placés valent mieux que dix.

---

## 12. Événements

Bloc `evenement` : invisible, il se déclenche **une seule fois** quand il passe le milieu de l'écran pendant le défilement. Placez-le **juste après** la phrase qui le justifie.

```json
{ "type": "evenement", "effet": "noir", "son": "respiration", "duree": 3000, "texte": "##Ne te retourne pas.##" }
```

| Clé | Type | Défaut | Description |
|---|---|---|---|
| `effet` | chaîne | `aucun` | Effet visuel : `glitch` (écran qui se brouille, couleurs décalées), `noir` (écran noir complet), `flash` (éclair blanc), `tremblement` (l'écran tremble), `aucun`. |
| `son` | chaîne | `aucun` | Son synthétisé (liste ci-dessous). |
| `duree` | millisecondes | noir 1600, flash 700, glitch 800, tremblement 900 | Durée de l'effet **et** du son (pour les sons qui ont une durée). |
| `nombre` | 1 à 12 | selon le son | Répétitions pour `coup` (3), `pas` (5), `coeur` (5), `vibration` (2), `goutte` (3). |
| `texte` | chaîne | — | Avec `noir` : texte affiché sur l'écran noir (balisage permis). |

**Sons** :

| `son` | Description |
|---|---|
| `coup` | Coups frappés sur du bois (porte, mur). |
| `craquement` | Plancher ou porte qui grince. |
| `chuchotement` | Voix chuchotée inintelligible qui passe de gauche à droite. |
| `respiration` | Respiration proche (inspiration, expiration). ~2,7 s par cycle. |
| `porte` | Grincement puis porte qui claque. |
| `silence` | **Coupe la musique net** pendant `duree`, puis elle revient doucement. Très efficace. |
| `pas` | Pas qui s'approchent. |
| `grattement` | Quelque chose qui gratte (griffes sur un mur). |
| `coeur` | Battements de cœur qui accélèrent. |
| `statique` | Grésillement de radio avec une tonalité fantôme. |
| `cloche` | Un coup de cloche grave (accordé sur la tonique de la musique). |
| `vibration` | Téléphone qui vibre. |
| `goutte` | Gouttes d'eau qui tombent, avec écho. |
| `aucun` | Pas de son. |

Règles : **3 à 6 événements par histoire**, aux moments clés. Pas de jumpscare gratuit : un événement doit prolonger ce que le texte vient de dire. `flash` et `tremblement` deviennent très doux si l'utilisateur a désactivé les effets intenses.

---

## 13. Robustesse et mode DEV

- Champ manquant ou invalide → valeur par défaut + avertissement. Type de bloc inconnu avec un `texte` → affiché comme paragraphe. Bloc illisible → ignoré.
- JSON légèrement abîmé (virgule finale, commentaire) → réparé si possible, signalé comme erreur.
- Fichier de la nuit introuvable → écran de secours « Silence radio » (jamais une histoire plus ancienne). **Jamais d'écran blanc.**
- Police introuvable → police de secours (serif, sans-serif, monospace ou cursive).
- Mode DEV (Paramètres → toucher **3 fois** le numéro de version en bas → code `BURGER`) : lire la prochaine histoire (et les passées) en aperçu, JSON brut, liste des erreurs et avertissements de format, prévisualisation d'un JSON collé, test de chaque mise en page, fond, son et couche musicale, réglage de la boîte à suggestions.

---

## 14. Exemple complet

Exemple court mais complet et valide (une vraie histoire doit compter 900 à 1600 mots et 3 à 6 événements) :

```json
{
  "version": 1,
  "numero": 2,
  "date": "2026-10-19",
  "titre": "Le quart de nuit",
  "sousTitre": "Bouée 44, golfe du Saint-Laurent",
  "sousGenre": "horreur maritime",
  "lieu": "Phare de l'île aux Corneilles, golfe du Saint-Laurent",
  "epoque": "novembre 1958",
  "auQuebec": true,
  "format": "Journal de bord d'un gardien de phare",
  "resume": "Un gardien de phare note chaque nuit la lumière d'un bateau qui n'approche jamais.",
  "retournement": "inversion : ce n'est pas le bateau qui approche, c'est la côte qui s'éloigne",
  "avertissement": "",
  "miseEnPage": "journal_intime",
  "theme": {
    "palette": { "fond": "#05080c", "surface": "#ece6d4", "texte": "#1b2433", "accent": "#9c2a1e", "secondaire": "#4a5d78" },
    "polices": { "titre": "Caveat", "texte": "Patrick Hand", "notes": "Caveat" },
    "cssPersonnalise": ""
  },
  "document": { "auteurDocument": "Wilfrid", "dateDocument": "Novembre 1958" },
  "fond": {
    "effet": "brouillard", "intensite": 0.55, "vignette": 0.75, "grain": 0.3, "scintillement": 0.1, "svgOpacite": 0.22,
    "svg": "<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 400 860' preserveAspectRatio='xMidYMax slice'><defs><linearGradient id='c' x1='0' y1='0' x2='0' y2='1'><stop offset='0' stop-color='#02040a'/><stop offset='1' stop-color='#101a26'/></linearGradient><radialGradient id='f' cx='.5' cy='.5' r='.5'><stop offset='0' stop-color='#fff2c4' stop-opacity='.9'/><stop offset='1' stop-color='#fff2c4' stop-opacity='0'/></radialGradient></defs><rect width='400' height='860' fill='url(#c)'/><path d='M0 640 Q200 620 400 640 L400 860 L0 860 Z' fill='#03060a'/><polygon points='300,640 318,420 334,420 352,640' fill='#060a10'/><circle cx='326' cy='410' r='60' fill='url(#f)'/><circle cx='80' cy='600' r='3' fill='#ffd27a' opacity='.6'/></svg>"
  },
  "musique": {
    "tempo": 48, "tonique": "E", "gamme": "dorien", "reverb": 0.85, "desaccord": 0.2, "evolution": "monte", "finale": "silence",
    "couches": [
      { "type": "bruit", "volume": 0.5, "filtre": 500, "densite": 0.9 },
      { "type": "drone", "onde": "sawtooth", "octave": 1, "volume": 0.45, "filtre": 300, "densite": 0.5 },
      { "type": "cloche", "octave": 4, "volume": 0.2, "densite": 0.12 },
      { "type": "cordes", "octave": 3, "volume": 0.25, "filtre": 900, "densite": 0.4, "seuil": 0.6 }
    ]
  },
  "blocs": [
    { "type": "titre", "texte": "Lundi 10 novembre — minuit" },
    { "type": "paragraphe", "texte": "Brume épaisse. La corne de brume toutes les trente secondes. Une lumière au large, plein est. Elle ne bouge pas." },
    { "type": "evenement", "effet": "aucun", "son": "cloche" },
    { "type": "titre", "texte": "Mardi 11 novembre — 3 h 12" },
    { "type": "paragraphe", "texte": "La lumière est plus proche. ##Elle clignote au même rythme que moi.##" },
    { "type": "note", "texte": "Ne pas oublier : remonter les poids de l'horloge." },
    { "type": "evenement", "effet": "noir", "son": "silence", "duree": 2500, "texte": "La corne s'est tue." },
    { "type": "paragraphe", "texte": "Je n'ai pas allumé la lanterne ce soir. ^^Pourtant, le phare brille.^^", "style": "espace" }
  ],
  "phraseFinale": "Le bateau n'a jamais approché. __C'est la côte qui s'éloigne.__",
  "question": { "texte": "La prochaine nuit, tu veux avoir peur…", "choix": ["sur l'eau", "sous la terre", "dans une maison pleine de monde"] }
}
```
