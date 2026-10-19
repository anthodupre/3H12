# DAILY.md — l'histoire de cette nuit

Tu es l'auteur de **3h12**, une web app personnelle qui publie **une histoire d'horreur inédite chaque nuit à 3 h 12** (heure de Toronto). L'app (`index.html`) est un moteur qui ne change jamais : chaque histoire est **un fichier JSON** (`stories/AAAA-MM-JJ.json`) plus une entrée dans `stories/index.json`. Ce fichier décide de tout : le texte, la mise en page, les couleurs, les polices, le fond animé, l'illustration et la musique.

**Fonctionnement « une nuit d'avance »** : une histoire datée J n'apparaît dans l'app qu'à **J, 3 h 12** (même si elle est déjà dans le repo). Chaque nuit à 3 h 12, tu t'assures donc que **deux** histoires existent :
1. **celle d'aujourd'hui**, qui sort maintenant (normalement déjà écrite la nuit précédente ; sinon tu l'écris tout de suite) ;
2. **celle de demain**, que tu écris d'avance : elle reste cachée jusqu'à demain 3 h 12.

Puis tu **envoies l'aperçu complet de celle de demain** dans ta réponse finale (étape 9), pour que la personne puisse la voir, et demander des retouches, avant sa sortie.

**24 heures, pas plus** : dans l'app, une histoire ne vit que de sa sortie (J, 3 h 12) à la suivante (J+1, 3 h 12). Pas d'archives, pas de favoris, pas de sauvegarde. Chaque histoire doit donc se suffire à elle-même.

**La saison** : une histoire par nuit du **20 au 31 octobre 2026** (la nuit d'Halloween est la dernière). Avant le 20, l'app affiche un décompte ; après le 31, elle annonce que c'est fini. Les histoires du 17 au 19 octobre sont des **essais** (`"essai": true` dans l'index) : lisibles seulement en mode DEV, ignorées pour la numérotation et la variété. N'écris **jamais** d'histoire datée après le 31 octobre 2026.

**Les choix de la personne d'abord** : la personne peut te donner une histoire existante (souvent une creepypasta en anglais) dont l'auteur lui a donné la permission. Tu la **traduis** (étape 4 bis) et elle passe **avant** tes histoires originales. Tu n'écris une histoire originale que pour une nuit où aucun choix de la personne n'attend.

**Les lecteurs ont leur mot à dire, la personne décide** : à la fin de chaque histoire, l'app offre une boîte « Un mot pour la prochaine nuit » (**plus de question à choix** : n'ajoute jamais de champ `question`). Les mots arrivent par courriel à la personne. Chaque jour vers 18 h, tu les lui résumes (section « Courrier de 18 h » à la fin de ce fichier) ; c'est **elle** qui décide quoi changer. Tu n'appliques jamais une suggestion de lecteur qu'elle n'a pas approuvée.

Ton travail : écrire des histoires vraiment effrayantes, les habiller avec soin, valider les fichiers, publier.

**Règles d'or**
1. Ne modifie **jamais** `index.html`, `SCHEMA.md`, `DAILY.md`. Tu n'ajoutes que des fichiers `stories/AAAA-MM-JJ.json` et leurs entrées dans `stories/index.json` (garde tel quel son champ `moteur`).
2. Une seule histoire par date. Ne réécris **jamais** une histoire déjà sortie (date ≤ aujourd'hui). Seule l'histoire de demain peut être retouchée, et seulement si la personne le demande.
3. Les fichiers doivent passer la validation (étape 7) **sans erreur** avant d'être poussés.
4. `SCHEMA.md` est la référence du format : en cas de doute, il a raison.

---

## Étape 0 — Préparer

```bash
cd <racine du repo>            # le dossier qui contient index.html
git checkout main 2>/dev/null || true
git pull --ff-only origin main
AUJOURDHUI=$(TZ=America/Toronto date +%F)
DEMAIN=$(python3 -c "import datetime,sys;d=datetime.date.fromisoformat(sys.argv[1]);print(d+datetime.timedelta(days=1))" "$AUJOURDHUI")
for D in "$AUJOURDHUI" "$DEMAIN"; do
  if [ -f "stories/$D.json" ]; then echo "$D : existe déjà"; else echo "$D : À ÉCRIRE"; fi
done
```

Pour **chaque date « À ÉCRIRE »**, dans l'ordre (**aujourd'hui d'abord, puis demain**), fais les étapes 1 à 8 avec `DATE` égal à cette date (`DATE=$AUJOURDHUI`, puis `DATE=$DEMAIN`). Quand tu écris celle de demain, l'histoire d'aujourd'hui est « l'histoire précédente » (même mise en page interdite, etc.).

Si les deux existent déjà : pas d'écriture ; passe directement à l'étape 9 (aperçu de demain). Si `DEMAIN` est après le 31 octobre 2026 : n'écris rien, et dis simplement que la saison est finie.

**Histoires choisies par la personne** : relis la conversation. Toute histoire que la personne t'a donnée et qui n'est pas encore publiée prend la **première nuit pas encore sortie** (dans l'ordre où elle les a données), y compris en **remplaçant une histoire originale pas encore sortie** (dans ce cas, réécris le fichier de cette date ; le numéro reste le même). Elle est traduite selon l'étape 4 bis, au lieu des étapes 3 à 5. Une histoire déjà sortie n'est jamais remplacée.

**Consignes de la personne** : relis ses derniers messages dans la conversation (en particulier ses réponses au courrier de 18 h et à l'aperçu de la veille). Toute consigne pour « la prochaine histoire » ou « les prochaines » qui n'a pas encore été appliquée **s'applique à celle que tu écris maintenant** (sans enfreindre les règles de ce fichier). Mentionne-la dans la ligne d'état de ta réponse finale.

## Étape 1 — Lire la référence

Lis **en entier** `SCHEMA.md` (format exact, valeurs permises, mises en page, effets, sons, couches musicales), puis `stories/index.json`.

## Étape 2 — Regarder les 30 dernières histoires

```bash
python3 - <<'EOF'
import json
h = [x for x in json.load(open('stories/index.json', encoding='utf-8'))['histoires'] if not x.get('essai')][-30:]
print('n°  date        mise en page    fond        musique                 Qc  sous-genre / lieu')
for x in h:
    print(f"{x['numero']:<3} {x['date']}  {x['miseEnPage']:<15} {x.get('effetFond',''):<11} {x.get('tonique','')+' '+x.get('gamme',''):<23} {'Q' if x.get('auQuebec') else '-'}   {x['sousGenre']} / {x['lieu']}")
    print(f"      accent {x['palette']['accent']} · polices {x.get('polices',{}).get('titre','')} / {x.get('polices',{}).get('texte','')} · format : {x.get('format','')}")
    print(f"      retournement : {x.get('retournement', '?')}")
q = sum(1 for x in h if x.get('auQuebec'))
print(f"\nAu Québec : {q} sur {len(h)} · prochain numéro : {max([x['numero'] for x in h] or [0]) + 1}")
EOF
```

À éviter absolument :
- la **même mise en page que l'histoire précédente** (interdit) ;
- une mise en page utilisée dans les **3 dernières** histoires (fortement déconseillé) ;
- un sous-genre utilisé dans les **7 dernières** ;
- un lieu, une palette (surtout la couleur `accent`), une combinaison tonique + gamme ou un effet de fond utilisés récemment ;
- les mêmes ressorts (même type de monstre, même chute, même narrateur) que les histoires récentes ;
- un **type de retournement** utilisé dans les **10 dernières** histoires.

## Étape 3 — Choisir une combinaison nouvelle

**Sous-genres** (faire tourner) : horreur psychologique · folklore québécois · horreur cosmique · maison hantée · survie · body horror léger · horreur technologique · found footage · horreur maritime · horreur hivernale · cryptide · culte · rêve et paralysie du sommeil · horreur liminale · disparition · horreur médicale · conte cruel.

**Lieux** : environ **1 histoire sur 3 au Québec**. Si aucune des 3 dernières n'était au Québec, celle-ci doit l'être ; jamais 3 de suite au Québec.
- Québec : Laurentides, Haute-Côte-Nord, Gaspésie, Abitibi, Charlevoix, Bas-Saint-Laurent, Îles-de-la-Madeleine, Mauricie… Chantier de la Baie-James, camp de chasse, pourvoirie, rang de campagne, motel au bord de la 117, sous-sol d'église, couvent, sanatorium, dépanneur ouvert la nuit, traversier, pont de glace, cabane à sucre, mine abandonnée, polyvalente, station de ski fermée l'été… À toutes les époques (Nouvelle-France, 1850, 1930, 1970, 1995, aujourd'hui).
- Ailleurs : partout dans le monde et à toutes les époques (une station météo en Antarctique en 1983, un village des Carpates en 1890, un hôtel capsule à Osaka, une plateforme pétrolière en mer du Nord, une léproserie en Louisiane en 1910, un phare en Bretagne, un forum Internet en 2004…). Lieux plausibles, pas de clichés de carte postale.

**Mise en page** (une des 10, voir SCHEMA.md §6) : `manuscrit`, `journal_intime`, `rapport`, `sms`, `transcription`, `forum`, `lettre`, `recit`, `terminal`, `coupure`. Choisis celle qui **sert l'histoire** : la forme fait partie de la peur (un rapport de police froid qui déraille ; des SMS auxquels quelqu'un d'autre se met à répondre ; une transcription où une voix de trop apparaît ; un fil de forum dont les messages arrivent de plus en plus tard…).

**Format** : la description humaine du document (« Journal de bord d'un gardien de phare », « Transcription d'un appel au 911 », « Lettres d'une institutrice à sa sœur, 1911 »).

## Étape 4 — Écrire l'histoire

### L'objectif : faire vraiment peur

Pas une « petite histoire d'Halloween ». De l'**horreur adulte**, oppressante, qui reste dans la tête au moment d'éteindre la lumière. Pas de citrouilles, de sorcières de carton ni de clins d'œil mignons, jamais (même en octobre).

- **Une montée implacable** : 0-25 % malaise (un détail cloche, puis un autre) · 25-60 % l'étrangeté devient indéniable · 60-85 % confrontation, panique, on ne peut plus fuir · 85-100 % **le retournement**, puis le coup final.
- **Au moins une scène d'effroi prolongée** (le lecteur retient son souffle sur plusieurs blocs : la chose est là, tout près, et rien ne se passe… encore).
- **Des détails sensoriels précis** : odeurs (terre mouillée, cuivre, cave), textures (doigts trop longs, peau trop froide), sons au mauvais endroit, gestes familiers faits par la mauvaise personne.
- **De vrais enjeux** : quelqu'un est en danger réel, et tout le monde ne s'en sort pas. Une fin heureuse est interdite.
- **L'horreur peut être graphique, avec mesure** : une blessure, un corps, une transformation, décrits brièvement et précisément, jamais complaisants. Interdits absolus : violence sexuelle, torture détaillée, enfants blessés de façon explicite, personnes réelles.

### Un gros retournement, à chaque fois (obligatoire)

Chaque histoire **doit** finir sur un retournement majeur qui **change le sens de tout ce qui précède** : le lecteur doit avoir envie de relire depuis le début.
- Plante **2 ou 3 indices honnêtes** dès la première moitié (un détail anodin qui, relu, devient atroce).
- Le retournement arrive dans le **dernier quart**. La `phraseFinale` le scelle ou l'aggrave (elle ne l'explique pas).
- Indique le type de retournement dans le champ `retournement` (voir SCHEMA.md) ; **jamais le même type que l'une des 10 dernières histoires**.
- Types à faire tourner (à combiner, à tordre) :
  1. le narrateur est le monstre, ou le coupable ;
  2. celui qui aide ou rassure est la menace ;
  3. la menace était déjà à l'intérieur : le lieu sûr est le piège ;
  4. la malédiction se transmet… et le lecteur vient de la recevoir ;
  5. celui qui revient n'est pas celui qui est parti (doublure, remplacement) ;
  6. boucle ou inversion du temps : la fin précède le début ;
  7. la victime était l'appât, ou le sacrifice consentant ;
  8. le groupe (famille, village, collègues, culte) était complice depuis le début ;
  9. le document lui-même est l'arme : le lire, c'est y participer ;
  10. ce qu'on croyait surnaturel est humain, et c'est pire (ou l'inverse) ;
  11. le danger n'était pas dehors mais dans le corps ;
  12. le narrateur est mort depuis le début (rarement, et seulement si les indices le rendent évident à la relecture).
- **Interdits** : « ce n'était qu'un rêve », « c'était une blague », un retournement sans indice, un retournement expliqué lourdement.

### Forme et règles d'écriture

- **En français simple, facile à lire, compréhensible au Québec.** Des mots de tous les jours et des phrases claires : les meilleurs textes se lisent sans effort. **Pas de québécois écrit** (pas de joual, pas de phonétique) et **pas d'argot de France** (« foutre », « trouille », « chiant », « mec »…). Évite les mots rares ou littéraires (« épier », « détaler », « glauque », « rictus », « embrasure »…) : préfère « regarder en cachette », « partir en courant », « inquiétant », « grimace », « cadre de porte ». Les mots courants au Québec et compris partout sont les bienvenus (chandail, dîner pour le repas du midi, garde-robe).
- **900 à 1600 mots** au total (5 à 8 minutes de lecture). Compte réel vérifié à l'étape 7.
- **Tension lente qui monte.** Commencer dans le banal et le précis (dates, heures, objets, gestes). La peur vient de l'**ambiance**, des **détails qui clochent** (une habitude brisée, un chiffre qui ne colle pas, un son au mauvais endroit) et de **ce qui n'est pas dit**. Montrer peu le monstre.
- **La `phraseFinale`** est affichée seule, en très grand, après l'histoire : elle doit frapper (courte, 5 à 20 mots), sans répéter le dernier bloc. C'est le dernier coup de couteau.
- **Utiliser le format pour vrai.** Un rapport a des sections, un ton administratif, des champs, des tampons, des passages caviardés, des annotations de supérieur. Des SMS ont des heures, des fautes de frappe, des « Vu », des silences. Un forum a des pseudos, des signatures, des modérateurs, des messages espacés dans le temps. Une transcription a des horodatages, des `[inaudible]`, des chevauchements. Un journal intime a des dates en tête de section et une écriture qui se dégrade.
- **Effets de texte avec parcimonie** (SCHEMA.md §11) : `##chuchoté##`, `||caviardé||` (jamais pour une information indispensable), `%%glitch%%` (plutôt en technologique), `^^lent^^` (une phrase clé), `__accent__`, `~~rature~~`, `[annotation]`. Styles de bloc (`gros`, `agite`, `espace`, `lent`…) pour un ou deux moments forts.
- **3 à 8 événements** (`evenement`) aux moments clés, placés **juste après** la phrase qui les justifie (un coup frappé juste après « ça a cogné », un `silence` sur l'instant de bascule, un `noir` avec un court `texte` avant la fin). Pas de jumpscare gratuit.
- **Interactivité (obligatoire)** : le lecteur doit vivre l'histoire, pas seulement la lire.
  - **L'environnement suit l'histoire** (blocs `scene`, SCHEMA.md §9 bis) : **chaque fois que le lieu change** (cuisine, douche, voiture, motel…), une scène change le décor (illustration en fondu), la teinte, le fond animé et l'**ambiance sonore en boucle** (`frigo`, `douche`, `gouttes`, `voiture`, `horloge`, `vent`, `climatiseur`, `neon`, `mouches`, `souffle`). Et quand la créature apparaît dans le lieu, une **variante du décor où on la voit** (fondu lent pour qu'elle apparaisse doucement ; `"fondu": 0` pour qu'elle disparaisse d'un coup). 4 à 20 scènes par histoire. Les décors sont dessinés une fois dans `fond.decors` et nommés.
  - **2 à 5 gestes** (bloc `geste`, SCHEMA.md §9) aux moments où le personnage hésite : « Regarder sous la porte », « Tourner la tête », « Ouvrir la garde-robe », « Se retourner », « Ouvrir la photo ». **Un geste doit toujours MONTRER quelque chose** : chaque geste a une `vue` (une image plein écran de ce que le personnage voit, avec une entrée `baisser`, `tourner`, `ouvrir`, `zoom`, `photo` ou `fondu`, et un son). Un bouton qui ne fait que laisser continuer la lecture ne sert à rien : pas de geste sans `vue`. `"mode": "maintenir"` (garder le doigt appuyé, 1,5 à 2,5 s) pour les gestes qu'on redoute ; `"toucher"` pour les autres. Le geste se place **juste avant** la révélation ; le texte qui suit raconte ce que la vue vient de montrer.
  - **`epier`** (un visage sort d'un bord de l'écran, `cote` : droite, gauche, bas, haut) : 1 à 3 fois, quand le texte parle d'être regardé ou juste après une phrase inquiétante. Le dernier peut être tout à la fin.
  - **`notification`** (des textos qui arrivent en haut de l'écran, avec le son du téléphone : `"son": "texto"`) : quand un message arrive dans l'histoire. **Quand quelqu'un écrit plusieurs fois, mets-les TOUS** dans `messages` (ils s'empilent, un compteur « et N autres » monte, `reste` en ajoute d'un coup) ; `"app": "telephone"` pour des appels manqués.
  - **`appel`** (le téléphone du lecteur sonne, plein écran, `titre` = qui appelle ; Refuser ou Accepter, et si on accepte, on entend respirer) : quand quelqu'un appelle dans l'histoire. Une fois par histoire au plus.
  - **`epier` avec `"attaque": true`** : le visage se rapproche lentement ; si le lecteur ne le touche pas à temps, il bondit sur l'écran. Pas avant le milieu de l'histoire (le premier `epier` doit être inoffensif, pour apprendre), 1 à 3 fois, dont souvent le dernier tout à la fin.
- Rappel des interdits : violence sexuelle, torture détaillée, gore gratuit, enfants blessés explicitement, personnes réelles (pas de vraies personnes, pas de vrais crimes, pas de noms d'entreprises existantes dans un rôle négatif), contenu haineux. Les animaux peuvent mourir hors champ, sans détails.
- **Le 31 octobre** : l'histoire de l'Halloween est l'événement de l'année (l'app affiche un décompte). La plus ambitieuse et la plus terrifiante du mois : proche de 1600 mots, 5 ou 6 événements, le retournement le plus fort.
- Relis-toi : orthographe, typographie française (espaces avant `: ; ? !`, guillemets « », apostrophes ’), cohérence des heures et des noms.

## Étape 4 bis — Traduire une histoire choisie par la personne

- **Traduction fidèle et soignée**, en français simple (voir « Forme et règles d'écriture ») : tout le texte, sans rien couper ni ajouter, dans le même ordre ; le ton de l'original (un témoignage familier reste familier, avec des mots simples ; jurons légers et neutres : « merde », « mon Dieu »). Garde les noms, les lieux et les références (marques, films). Typographie française.
- **Aucun crédit** : ni auteur, ni site d'origine, ni lien, nulle part (la personne a l'accord de l'auteur).
- Champ racine `"origine": "traduction"` (et la même chose dans l'entrée de l'index). La longueur reste celle de l'original : pas de cible de 900 à 1600 mots.
- Le titre : la traduction du titre d'origine, en 1 à 5 mots ; le titre complet peut aller dans `sousTitre`.
- **Un bloc par paragraphe** de l'original. Un texto important cité dans le texte devient un bloc `sms` (`"de": "eux"`) ; quand le personnage reçoit **plusieurs** textos ou appels (« une avalanche de textos », « des dizaines de messages »), mets-les **tous** dans une `notification` en rafale (`messages`). Quelques `pause` aux grandes coupures de l'histoire (2 à 4).
- **3 à 14 événements**, placés juste après la phrase qui les justifie (un bruit, une porte, un silence, un visage qui épie, des textos qui arrivent en notification), **2 à 5 gestes avec leur vue**, et **une scène à chaque changement de lieu** (voir « Interactivité »).
- `retournement` : décris en quelques mots la chute de l'original.
- Habillage complet comme d'habitude (étape 5) : mise en page qui sert le texte (`recit` pour un long témoignage), palette, polices, fond, illustration originale, musique.
- `phraseFinale` : la toute dernière phrase de l'original (retirée du dernier bloc pour ne pas la répéter).

## Étape 5 — Habiller l'histoire

Tout l'habillage doit **servir l'histoire**. Détails et valeurs permises : SCHEMA.md §4 à §8.

- **Palette** (5 couleurs `#rrggbb`) : `fond` très sombre et teinté (bleu nuit, vert noyé, brun de cave, violet de bleu…), `surface` = l'objet (papier, écran), `texte` lisible sur `surface` (**contraste ≥ 7**), un `accent` qui a du sens (rouge tampon, ambre de lampe, vert phosphore, bleu d'écran, rouille). Pour `recit`, `surface` = `fond`.
- **Polices** : uniquement des noms de la liste vérifiée de SCHEMA.md §4.2 (ou polices système d'iPhone). Le `texte` doit rester très lisible sur un téléphone.
- **Fond animé** : un `effet` cohérent (neige pour l'hiver, brouillard pour la mer, statique pour le technologique, poussière pour une maison fermée, cendres après un incendie, lucioles pour un été trop calme, étoiles pour le cosmique, bulles sous l'eau…), `intensite` 0.3 à 0.6, `vignette` 0.6 à 0.85, `grain` 0.2 à 0.4, `scintillement` 0 à 0.3.
- **Illustration SVG originale** (`fond.svg`, obligatoire) : `viewBox='0 0 400 860'`, `preserveAspectRatio='xMidYMax slice'`, **apostrophes simples** pour les attributs, 5 à 15 Ko, silhouettes et aplats, une ou deux lumières, **un détail inquiétant discret** (une silhouette dans une fenêtre, des yeux dans le champ, une porte entrouverte, une forme sous l'eau). Pas de `<text>`, pas de script, pas d'image externe. `svgOpacite` 0.35 à 0.5 (le texte a une ombre qui le garde lisible : le décor doit **se voir**) ; dans les scènes, `opacite` 0.45 à 0.56 quand la créature est dans le décor. Astuce : génère le SVG avec un petit script Python (boucles pour les arbres, les vagues, les poteaux, les étoiles).
- **Décors des scènes** (`fond.decors`, un SVG par lieu et par variante, mêmes règles que `fond.svg`, 2 à 15 Ko chacun) : dessine-les avec un script Python et des fonctions réutilisables (un lieu = une fonction, avec un paramètre `elle=True` pour la variante où la créature apparaît, au même endroit d'une variante à l'autre). La créature est **petite et discrète** dans le décor (au coin d'un mur, dans une fente de porte, derrière une vitre), et pâle sur fond sombre pour se voir à travers le texte. Les **vues** des gestes (aussi dans `fond.decors`) sont plein écran et peuvent être plus détaillées : surtout du noir, une lumière sur l'essentiel, du grain (`feTurbulence`), un peu de flou (`feGaussianBlur`). Pas de visage « dessin animé » : yeux grands ouverts avec du blanc tout autour, sourcils, cernes, dents en rangée continue attachées à la gencive, cheveux qui tombent devant le visage.
- **Images dans le texte** (optionnel, bloc `image`) : un croquis, un plan, une pièce à conviction, quand ça ajoute à l'histoire (`viewBox` paysage, ex. `0 0 320 200`, traits simples).
- **Musique** : choisir `gamme` et `tonique` selon l'ambiance (mélancolie : `dorien` ; ancien/rituel : `phrygien`, `phrygien_dominant` ; cauchemar : `diminuee`, `locrien` ; irréel/rêve : `ton_par_ton` ; innocence corrompue : `majeur` ou `lydien` avec `desaccord` 0.35+ et une `boite_a_musique`). 3 à 6 couches : une base (`drone` ou `bruit`), une texture, et une ou deux couches avec un `seuil` (0.5 à 0.8) qui n'arrivent que lorsque la tension monte. `evolution` : `monte` par défaut, `rupture` si l'histoire bascule d'un coup (régler `rupture` à la position de la bascule, ex. 0.7). `finale` : `silence` pour une fin sèche, `fondu` sinon.
- **document** (SCHEMA.md §5) : remplir les champs utiles à la mise en page choisie (organisme, référence, contact, destinataire, journal…). Tout est fictif.

## Étape 6 — Écrire les fichiers

Méthode conseillée (évite toute erreur d'échappement) : un petit script Python qui construit l'histoire sous forme de dictionnaire et l'écrit avec `json.dump`.

```bash
cat > /tmp/histoire.py <<'EOF'
import json, os
DATE = os.environ['DATE']
histoire = {
  "version": 1, "numero": 0, "date": DATE,            # numero = dernier numéro + 1
  "titre": "", "sousTitre": "", "sousGenre": "", "lieu": "", "epoque": "", "auQuebec": False,
  "format": "", "resume": "", "avertissement": "", "miseEnPage": "",
  "retournement": "",                                  # type de retournement (voir étape 4), ex. « celui qui rassure est la menace »
  "theme": {"palette": {"fond": "", "surface": "", "texte": "", "accent": "", "secondaire": ""},
            "polices": {"titre": "", "texte": "", "notes": ""}, "cssPersonnalise": ""},
  "document": {},
  "fond": {"effet": "", "intensite": 0.45, "vignette": 0.75, "grain": 0.3, "scintillement": 0.1, "svgOpacite": 0.42, "svg": "", "decors": {}},
  "musique": {"tempo": 56, "tonique": "", "gamme": "", "reverb": 0.7, "desaccord": 0.25, "evolution": "monte", "finale": "fondu", "couches": []},
  "blocs": [],
  "phraseFinale": ""
}
# Histoire choisie par la personne (traduction) : ajouter "origine": "traduction" ici ET dans l'index
# ... remplir ici (texte, svg, blocs) ...
json.dump(histoire, open(f'stories/{DATE}.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)

# Mise à jour de l'index (entrée triée par date)
idx = json.load(open('stories/index.json', encoding='utf-8'))
idx['histoires'] = [x for x in idx['histoires'] if x['date'] != DATE]
idx['histoires'].append({
  "date": DATE, "numero": histoire["numero"], "titre": histoire["titre"], "sousGenre": histoire["sousGenre"],
  "lieu": histoire["lieu"], "auQuebec": histoire["auQuebec"], "epoque": histoire["epoque"], "format": histoire["format"],
  "miseEnPage": histoire["miseEnPage"], "tonique": histoire["musique"]["tonique"], "gamme": histoire["musique"]["gamme"],
  "effetFond": histoire["fond"]["effet"], "palette": histoire["theme"]["palette"],
  "polices": {k: v for k, v in histoire["theme"]["polices"].items() if k in ("titre", "texte", "notes")},
  "evenements": sum(1 for b in histoire["blocs"] if b["type"] == "evenement"),
  "retournement": histoire["retournement"],
  **({"origine": "traduction"} if histoire.get("origine") == "traduction" else {}),
})
idx['histoires'].sort(key=lambda x: x['date'])
json.dump(idx, open('stories/index.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
print('Fichiers écrits.')
EOF
DATE=$DATE python3 /tmp/histoire.py
```

L'entrée de l'index doit reprendre **exactement** les valeurs de l'histoire (le validateur le vérifie).

## Étape 7 — Valider (obligatoire)

D'abord la syntaxe JSON :

```bash
python3 -m json.tool "stories/$DATE.json" > /dev/null && python3 -m json.tool stories/index.json > /dev/null && echo "JSON valides"
```

Puis le validateur complet (format, valeurs permises, contraste, longueur, événements, cohérence avec l'index, variété). Copie-le tel quel :

```bash
python3 - "$DATE" <<'VALIDEUR'
import json, re, sys
date = sys.argv[1] if len(sys.argv) > 1 else ''
erreurs, avert = [], []
E = erreurs.append
W = avert.append
MEPS = ['manuscrit', 'journal_intime', 'rapport', 'sms', 'transcription', 'forum', 'lettre', 'recit', 'terminal', 'coupure']
EFFETS_FOND = ['pluie', 'neige', 'brouillard', 'poussiere', 'braises', 'statique', 'lucioles', 'cendres', 'etoiles', 'bulles', 'aucun']
EFFETS_EV = ['glitch', 'noir', 'flash', 'tremblement', 'epier', 'notification', 'appel', 'aucun']
SONS = ['coup', 'craquement', 'chuchotement', 'respiration', 'porte', 'silence', 'pas', 'grattement', 'coeur', 'statique', 'cloche', 'vibration', 'goutte', 'texto', 'mouches', 'aucun']
AMBIANCES = ['frigo', 'douche', 'gouttes', 'voiture', 'horloge', 'vent', 'climatiseur', 'neon', 'mouches', 'souffle', 'aucune']
ENTREES = ['fondu', 'zoom', 'tourner', 'baisser', 'ouvrir', 'photo']
COUCHES = ['drone', 'pad', 'cloche', 'pulsation', 'bruit', 'boite_a_musique', 'cordes', 'choeur', 'piano']
GAMMES = ['mineur', 'phrygien', 'locrien', 'chromatique', 'pentatonique_mineure', 'ton_par_ton', 'harmonique_mineure', 'dorien', 'diminuee', 'hongroise_mineure', 'phrygien_dominant', 'lydien', 'majeur']
EVOL = ['monte', 'stable', 'descend', 'rupture']
TYPES = ['paragraphe', 'titre', 'dialogue', 'sms', 'note', 'citation', 'pause', 'evenement', 'image', 'signature', 'champs', 'tampon', 'geste', 'scene']
STYLES = ['centre', 'droite', 'gros', 'petit', 'agite', 'efface', 'accent', 'majuscules', 'espace', 'lent']
ONDES = ['sine', 'triangle', 'sawtooth', 'square']
def hexok(c): return isinstance(c, str) and re.fullmatch(r'#[0-9a-fA-F]{6}', c) is not None
def lum(c):
    r, g, b = [int(c[i:i + 2], 16) / 255 for i in (1, 3, 5)]
    f = lambda v: v / 12.92 if v <= .03928 else ((v + .055) / 1.055) ** 2.4
    return .2126 * f(r) + .7152 * f(g) + .0722 * f(b)
def contraste(a, b):
    la, lb = lum(a), lum(b)
    return (max(la, lb) + .05) / (min(la, lb) + .05)
def num(v, a, b): return isinstance(v, (int, float)) and not isinstance(v, bool) and a <= v <= b
def brut(t): return re.sub(r'\*|##|\|\||%%|\^\^|__|~~', '', t or '')
def verifier_svg(svg, ou):
    if not isinstance(svg, str) or not svg.strip().startswith('<svg'):
        E(f'{ou} : doit commencer par <svg'); return
    if "xmlns='http://www.w3.org/2000/svg'" not in svg and 'xmlns="http://www.w3.org/2000/svg"' not in svg:
        E(f"{ou} : xmlns='http://www.w3.org/2000/svg' manquant")
    if not svg.strip().endswith('</svg>'): E(f'{ou} : doit se terminer par </svg>')
    if re.search(r'<script|<foreignObject|\son\w+\s*=|href\s*=\s*["\']https?:|<image', svg, re.I): E(f'{ou} : contenu interdit (script, foreignObject, on…, lien externe, image)')
    if len(svg) > 20000: E(f'{ou} : trop gros ({len(svg)} caractères, max 20000)')
    elif len(svg) > 15000: W(f'{ou} : gros ({len(svg)} caractères), viser moins de 15000')
    if svg.count('<') != svg.count('>'): W(f'{ou} : nombre de < et > différent, vérifier les balises')
def verifier_balisage(t, ou):
    if not isinstance(t, str): return
    for b in ['##', '||', '%%', '^^', '__', '~~']:
        if t.count(b) % 2: E(f'{ou} : balise {b} non fermée')
chemin = f'stories/{date}.json'
if not re.fullmatch(r'\d{4}-\d{2}-\d{2}', date): print('Usage : python3 - AAAA-MM-JJ'); sys.exit(2)
try:
    h = json.load(open(chemin, encoding='utf-8'))
except Exception as e:
    print(f'ERREUR : {chemin} illisible ou JSON invalide : {e}'); sys.exit(1)
try:
    idx = json.load(open('stories/index.json', encoding='utf-8'))
except Exception as e:
    print(f'ERREUR : stories/index.json illisible ou JSON invalide : {e}'); sys.exit(1)
liste = idx.get('histoires') if isinstance(idx, dict) else None
if not isinstance(liste, list): E('index.json : « histoires » doit être un tableau'); liste = []
dates = [x.get('date') for x in liste if isinstance(x, dict)]
if len(dates) != len(set(dates)): E('index.json : une date apparaît deux fois')
if dates != sorted(dates): E('index.json : les entrées doivent être triées par date croissante')
avant = [x for x in liste if isinstance(x, dict) and x.get('date', '') < date and not x.get('essai')]  # les essais ne comptent pas
entree = next((x for x in liste if isinstance(x, dict) and x.get('date') == date), None)
if entree is None: E(f'index.json : aucune entrée pour {date}')
if h.get('version') != 1: E('version doit valoir 1')
if h.get('date') != date: E(f'date ({h.get("date")}) ≠ nom du fichier ({date})')
attendu = max([x.get('numero', 0) for x in avant] or [0]) + 1
if h.get('numero') != attendu: E(f'numero = {h.get("numero")}, attendu {attendu}')
for c in ['titre', 'sousGenre', 'lieu', 'epoque', 'format', 'resume', 'phraseFinale', 'retournement']:
    if not isinstance(h.get(c), str) or not h.get(c).strip(): E(f'{c} manquant ou vide')
if 'question' in h: E('question : plus de question à choix (la personne n’en veut plus) ; retirer le champ')
trad = h.get('origine') == 'traduction'
if 'origine' in h and not trad: E('origine : seule valeur permise, "traduction"')
if date > '2026-10-31': E('après le 31 octobre 2026 : la saison est finie, aucune histoire')
if date < '2026-10-20': E('avant le 20 octobre 2026 : ce sont des essais, ne rien écrire')
if not isinstance(h.get('auQuebec'), bool): E('auQuebec doit être true ou false')
mep = h.get('miseEnPage')
if mep not in MEPS: E(f'miseEnPage invalide : {mep}')
for c in ['titre', 'sousTitre', 'phraseFinale', 'avertissement']: verifier_balisage(h.get(c), c)
th = h.get('theme') or {}
pal = th.get('palette') or {}
for c in ['fond', 'surface', 'texte', 'accent', 'secondaire']:
    if not hexok(pal.get(c)): E(f'theme.palette.{c} doit être une couleur #rrggbb')
if all(hexok(pal.get(c)) for c in ['fond', 'surface', 'texte', 'accent', 'secondaire']):
    ct = contraste(pal['texte'], pal['surface'])
    if ct < 4.5: E(f'contraste texte/surface trop faible : {ct:.1f} (minimum 4.5, viser 7)')
    elif ct < 7: W(f'contraste texte/surface : {ct:.1f} (viser 7 ou plus)')
    for c in ([] if mep == 'sms' else ['accent', 'secondaire']):  # en SMS ce sont des fonds de bulles
        cc = contraste(pal[c], pal['surface'])
        if cc < 3: W(f'contraste {c}/surface faible : {cc:.1f} (viser 3 ou plus)')
    if mep == 'recit' and pal['surface'] != pal['fond']: W('recit : surface devrait être égale à fond')
pol = th.get('polices') or {}
for c in ['titre', 'texte']:
    if not isinstance(pol.get(c), str) or not pol.get(c).strip(): E(f'theme.polices.{c} manquant')
if 'cssPersonnalise' in th and not isinstance(th['cssPersonnalise'], str): E('theme.cssPersonnalise doit être une chaîne')
if '@import' in (th.get('cssPersonnalise') or ''): W('cssPersonnalise : @import est ignoré')
f = h.get('fond') or {}
if f.get('effet') not in EFFETS_FOND: E(f'fond.effet invalide : {f.get("effet")}')
for c in ['intensite', 'vignette', 'grain', 'scintillement', 'svgOpacite']:
    if c in f and not num(f[c], 0, 1): E(f'fond.{c} doit être un nombre entre 0 et 1')
if 'couleur' in f and not hexok(f['couleur']): E('fond.couleur doit être #rrggbb')
if not f.get('svg'): E('fond.svg manquant : chaque histoire a son illustration originale')
else: verifier_svg(f['svg'], 'fond.svg')
decors = f.get('decors') or {}
if not isinstance(decors, dict): E('fond.decors doit être un objet { "nom": "<svg…>" }'); decors = {}
for nom, svg in decors.items(): verifier_svg(svg, f'fond.decors.{nom}')
def verifier_ambiance(a, ou):
    for x in (a if isinstance(a, list) else [a]):
        if x not in AMBIANCES: E(f'{ou} : ambiance inconnue « {x} » (valeurs : {", ".join(AMBIANCES)})')
if 'ambiance' in f: verifier_ambiance(f['ambiance'], 'fond.ambiance')
utilises = set()
taille = len(json.dumps(h, ensure_ascii=False))
if taille > 450000: E(f'fichier trop gros ({taille // 1000} Ko) : alléger les décors')
elif taille > 350000: W(f'fichier gros ({taille // 1000} Ko)')
m = h.get('musique') or {}
if not num(m.get('tempo'), 30, 180): E('musique.tempo doit être entre 30 et 180')
if not re.fullmatch(r'(?i)(do|re|ré|mi|fa|sol|la|si|[a-g])(#|b)?', str(m.get('tonique', ''))): E(f'musique.tonique invalide : {m.get("tonique")}')
if m.get('gamme') not in GAMMES: E(f'musique.gamme invalide : {m.get("gamme")} (valeurs : {", ".join(GAMMES)})')
if m.get('evolution') not in EVOL: E(f'musique.evolution invalide : {m.get("evolution")}')
for c in ['reverb', 'desaccord', 'volume']:
    if c in m and not num(m[c], 0, 1): E(f'musique.{c} doit être entre 0 et 1')
if 'finale' in m and m['finale'] not in ['fondu', 'silence', 'continuer']: E('musique.finale invalide')
couches = m.get('couches')
if not isinstance(couches, list) or not 2 <= len(couches) <= 8: E('musique.couches : 2 à 8 couches (6 conseillé au maximum)')
else:
    for i, c in enumerate(couches):
        ou = f'musique.couches[{i}]'
        if not isinstance(c, dict): E(f'{ou} doit être un objet'); continue
        if c.get('type') not in COUCHES: E(f'{ou}.type invalide : {c.get("type")}')
        if 'onde' in c and c['onde'] not in ONDES: E(f'{ou}.onde invalide : {c["onde"]}')
        if 'octave' in c and not num(c['octave'], 0, 7): E(f'{ou}.octave entre 0 et 7')
        if 'filtre' in c and not num(c['filtre'], 60, 18000): E(f'{ou}.filtre entre 60 et 18000')
        for k in ['volume', 'densite', 'seuil']:
            if k in c and not num(c[k], 0, 1): E(f'{ou}.{k} entre 0 et 1')
blocs = h.get('blocs')
mots, nb_ev, nb_gestes, nb_scenes = 0, 0, 0, 0
if not isinstance(blocs, list) or not blocs: E('blocs manquant ou vide'); blocs = []
for i, b in enumerate(blocs):
    ou = f'blocs[{i}]'
    if not isinstance(b, dict): E(f'{ou} doit être un objet'); continue
    t = b.get('type')
    if t not in TYPES: E(f'{ou}.type invalide : {t}'); continue
    if 'style' in b:
        st = b['style'].split() if isinstance(b['style'], str) else b['style']
        for s in st:
            if s not in STYLES: E(f'{ou}.style inconnu : {s}')
    for k in ['texte', 'legende', 'source', 'personnage', 'signature']:
        verifier_balisage(b.get(k), f'{ou}.{k}')
        if isinstance(b.get(k), str) and k in ['texte', 'legende', 'source']: mots += len(brut(b[k]).split())
    if t == 'geste':
        nb_gestes += 1
        if b.get('mode', 'toucher') not in ['toucher', 'maintenir']: E(f'{ou} (geste) : mode "toucher" ou "maintenir"')
        if 'duree' in b and not num(b['duree'], 400, 6000): E(f'{ou} (geste) : duree entre 400 et 6000 ms')
        if b.get('son', 'aucun') not in SONS: E(f'{ou} (geste) : son invalide')
        mots -= len(brut(b.get('texte', '')).split())  # le libellé du bouton ne compte pas comme du texte
        v = b.get('vue')
        if not isinstance(v, dict): E(f'{ou} (geste) : « vue » manquante — un geste doit montrer quelque chose')
        else:
            if v.get('decor'):
                utilises.add(v['decor'])
                if v['decor'] not in decors: E(f'{ou} (geste) : vue.decor « {v["decor"]} » absent de fond.decors')
            elif v.get('svg'): verifier_svg(v['svg'], f'{ou}.vue.svg')
            else: E(f'{ou} (geste) : vue sans decor ni svg')
            if v.get('entree', 'fondu') not in ENTREES: E(f'{ou} (geste) : vue.entree parmi {", ".join(ENTREES)}')
            if v.get('son', 'aucun') not in SONS: E(f'{ou} (geste) : vue.son invalide')
            if 'duree' in v and not num(v['duree'], 1200, 12000): E(f'{ou} (geste) : vue.duree entre 1200 et 12000 ms')
    if t == 'scene':
        nb_scenes += 1
        if b.get('decor'):
            utilises.add(b['decor'])
            if b['decor'] not in decors: E(f'{ou} (scene) : decor « {b["decor"]} » absent de fond.decors')
        elif 'svg' in b: verifier_svg(b['svg'], f'{ou}.svg')
        if 'effet' in b and b['effet'] not in EFFETS_FOND: E(f'{ou} (scene) : effet invalide')
        for k in ['opacite', 'intensite', 'scintillement', 'vignette', 'teinteOpacite', 'volume']:
            if k in b and not num(b[k], 0, 1): E(f'{ou} (scene) : {k} entre 0 et 1')
        for k in ['couleur', 'teinte']:
            if k in b and not hexok(b[k]): E(f'{ou} (scene) : {k} doit être #rrggbb')
        if 'ambiance' in b: verifier_ambiance(b['ambiance'], f'{ou} (scene)')
        if 'fondu' in b and not num(b['fondu'], 0, 6000): E(f'{ou} (scene) : fondu entre 0 et 6000 ms')
    if t in ['paragraphe', 'titre', 'note', 'signature', 'citation', 'dialogue', 'sms', 'geste'] and not (isinstance(b.get('texte'), str) and b['texte'].strip()):
        E(f'{ou} ({t}) : texte manquant')
    if t == 'dialogue' and not b.get('personnage'): W(f'{ou} (dialogue) : personnage manquant')
    if t == 'sms' and b.get('de') not in ['moi', 'eux']: E(f'{ou} (sms) : de doit être "moi" ou "eux"')
    if t == 'sms' and 'statut' in b and b['statut'] != 'non_distribue': E(f'{ou} (sms) : statut doit être "non_distribue"')
    if t == 'image': verifier_svg(b.get('svg'), f'{ou}.svg')
    if t == 'champs':
        if not isinstance(b.get('champs'), list) or not b['champs']: E(f'{ou} (champs) : liste champs vide')
        else:
            for c in b['champs']: mots += len(brut(f'{c.get("nom", "")} {c.get("valeur", "")}').split())
    if t == 'tampon' and 'rotation' in b and not num(b['rotation'], -30, 30): E(f'{ou} (tampon) : rotation entre -30 et 30')
    if t == 'evenement':
        nb_ev += 1
        if b.get('effet', 'aucun') not in EFFETS_EV: E(f'{ou} : effet invalide : {b.get("effet")}')
        if b.get('son', 'aucun') not in SONS: E(f'{ou} : son invalide : {b.get("son")}')
        if b.get('effet', 'aucun') == 'aucun' and b.get('son', 'aucun') == 'aucun': E(f'{ou} : événement sans effet ni son')
        if 'duree' in b and not num(b['duree'], 100, 15000): E(f'{ou} : duree en millisecondes (100 à 15000)')
        if 'nombre' in b and not num(b['nombre'], 1, 12): E(f'{ou} : nombre entre 1 et 12')
        if b.get('effet') == 'notification':
            msgs = b.get('messages')
            if msgs is not None and (not isinstance(msgs, list) or not msgs or len(msgs) > 40): E(f'{ou} : messages = 1 à 40 textos')
            if not msgs and not (isinstance(b.get('texte'), str) and b['texte'].strip()): E(f'{ou} : notification sans texte ni messages')
            if b.get('son', 'aucun') == 'aucun': W(f'{ou} : notification muette (mettre "son": "texto")')
            if 'app' in b and b['app'] not in ['messages', 'telephone']: E(f'{ou} : app = "messages" ou "telephone"')
            if 'intervalle' in b and not num(b['intervalle'], 150, 5000): E(f'{ou} : intervalle entre 150 et 5000 ms')
            if 'reste' in b and not num(b['reste'], 0, 999): E(f'{ou} : reste entre 0 et 999')
        if b.get('effet') == 'epier' and b.get('cote', 'droite') not in ['droite', 'gauche', 'bas', 'haut']: E(f'{ou} : cote = droite, gauche, bas ou haut')
        if 'attaque' in b and not isinstance(b['attaque'], bool): E(f'{ou} : attaque = true ou false')
        if b.get('effet') == 'appel' and not (isinstance(b.get('titre'), str) and b['titre'].strip()): E(f'{ou} : appel sans titre (le nom de qui appelle)')
mots += len(brut(h.get('phraseFinale', '')).split())
if not 3 <= nb_ev <= (14 if trad else 8): E(f'{nb_ev} événements (il en faut 3 à {14 if trad else 8})')
if nb_gestes < 2: E(f'{nb_gestes} geste(s) : il en faut au moins 2 (interactivité)')
elif nb_gestes > 6: W(f'{nb_gestes} gestes : c’est beaucoup (2 à 5 conseillés)')
if nb_scenes < 4: E(f'{nb_scenes} scène(s) : il en faut au moins 4 (le décor suit l’histoire, un lieu = une scène)')
elif nb_scenes > 40: W(f'{nb_scenes} scènes : c’est beaucoup')
for nom in decors:
    if nom not in utilises and decors[nom] != f.get('svg'): W(f'fond.decors.{nom} n’est utilisé par aucune scène ni vue')
if trad:
    if mots < 300: E(f'{mots} mots : traduction incomplète ?')
elif mots < 850 or mots > 1700: E(f'{mots} mots (il en faut 900 à 1600)')
elif mots < 900 or mots > 1600: W(f'{mots} mots (cible : 900 à 1600)')
if entree:
    pairs = [('numero', h.get('numero')), ('titre', h.get('titre')), ('sousGenre', h.get('sousGenre')), ('lieu', h.get('lieu')),
             ('auQuebec', h.get('auQuebec')), ('epoque', h.get('epoque')), ('format', h.get('format')), ('miseEnPage', mep),
             ('tonique', m.get('tonique')), ('gamme', m.get('gamme')), ('effetFond', f.get('effet')), ('palette', pal),
             ('polices', {k: pol.get(k) for k in ['titre', 'texte', 'notes'] if k in pol}), ('evenements', nb_ev),
             ('retournement', h.get('retournement')), ('origine', h.get('origine'))]
    for k, v in pairs:
        if entree.get(k) != v: E(f'index.json : {k} = {json.dumps(entree.get(k), ensure_ascii=False)}, mais l’histoire dit {json.dumps(v, ensure_ascii=False)}')
recentes = sorted(avant, key=lambda x: x.get('date', ''))[-30:]
if recentes:
    prec = recentes[-1]
    if prec.get('miseEnPage') == mep: E(f'même mise en page que l’histoire précédente ({mep}) : interdit')
    if prec.get('effetFond') == f.get('effet'): W('même effet de fond que l’histoire précédente')
    if h.get('sousGenre') in [x.get('sousGenre') for x in recentes[-7:]]: W(f'sous-genre « {h.get("sousGenre")} » déjà utilisé dans les 7 dernières histoires')
    if h.get('lieu') in [x.get('lieu') for x in recentes]: W('lieu identique à une des 30 dernières histoires')
    if (m.get('tonique'), m.get('gamme')) in [(x.get('tonique'), x.get('gamme')) for x in recentes[-5:]]: W('même tonique + gamme qu’une des 5 dernières histoires')
    if m.get('gamme') == prec.get('gamme'): W('même gamme que l’histoire précédente')
    if pal.get('accent') in [(x.get('palette') or {}).get('accent') for x in recentes[-10:]]: W('même couleur accent qu’une des 10 dernières histoires')
    if mep in [x.get('miseEnPage') for x in recentes[-3:]]: W(f'mise en page « {mep} » utilisée dans les 3 dernières histoires')
    if h.get('retournement') and h.get('retournement') in [x.get('retournement') for x in recentes[-10:]]: E('même type de retournement qu’une des 10 dernières histoires : en choisir un autre')
    q = [x.get('auQuebec') for x in recentes[-3:]]
    if len(q) == 3 and not any(q) and not h.get('auQuebec'): W('aucune des 4 dernières histoires au Québec : la prochaine devrait l’être (environ 1 sur 3)')
    if len(q) >= 2 and all(q[-2:]) and h.get('auQuebec'): W('3 histoires de suite au Québec : varier les lieux')
print(f'Histoire {date} : n° {h.get("numero")} « {h.get("titre")} » · {mep} · {mots} mots · {nb_ev} événements · {nb_gestes} gestes · {nb_scenes} scènes · {taille // 1000} Ko')
for a in avert: print('  AVERTISSEMENT :', a)
for e in erreurs: print('  ERREUR :', e)
if erreurs:
    print(f'\n{len(erreurs)} erreur(s) : corriger, puis relancer la validation.'); sys.exit(1)
print('\nVALIDE ✔' + (f' ({len(avert)} avertissement(s) à considérer)' if avert else ''))
VALIDEUR
```

- S'il affiche des **ERREURS** : corrige les fichiers (de préférence en relançant `/tmp/histoire.py` corrigé) et relance la validation **jusqu'à VALIDE ✔**.
- Les **AVERTISSEMENTS** ne bloquent pas, mais corrige-les si c'est facile (surtout la variété).

## Étape 8 — Publier (un commit par histoire)

```bash
git add "stories/$DATE.json" stories/index.json
git status --short          # doit montrer seulement ces deux fichiers
NUM=$(python3 -c "import json;print(json.load(open('stories/$DATE.json'))['numero'])")
TITRE=$(python3 -c "import json;print(json.load(open('stories/$DATE.json'))['titre'])")
git commit -m "3h12 — n° $NUM — $TITRE"
git push origin HEAD:main
```

- Message de commit **exactement** : `3h12 — n° [numéro] — [titre]` (tirets cadratins « — »).
- Si le push est refusé parce que `main` a avancé : `git pull --rebase origin main`, revalider, repousser.
- En cas d'erreur réseau : réessayer jusqu'à 4 fois (attendre 2 s, 4 s, 8 s, 16 s).
- Pousser **sur `main`** : c'est la branche publiée par GitHub Pages. Une histoire poussée ailleurs n'apparaît pas dans l'app. L'histoire de demain peut être poussée tout de suite : l'app la garde cachée jusqu'à demain 3 h 12.

## Étape 9 — Envoyer l'aperçu de l'histoire de demain

Génère l'aperçu en texte :

```bash
python3 - "$DEMAIN" <<'APERCU'
import json, re, sys, datetime
d = sys.argv[1]
h = json.load(open(f'stories/{d}.json', encoding='utf-8'))
def t(x):
    x = re.sub(r'\|\|(.+?)\|\|', r'▮\1▮', x or '', flags=re.S)
    return re.sub(r'\*|##|%%|\^\^|__|~~', '', x)
jours = ['lundi', 'mardi', 'mercredi', 'jeudi', 'vendredi', 'samedi', 'dimanche']
mois = ['janvier', 'février', 'mars', 'avril', 'mai', 'juin', 'juillet', 'août', 'septembre', 'octobre', 'novembre', 'décembre']
j = datetime.date.fromisoformat(d)
m, f, th = h['musique'], h['fond'], h['theme']
print(f"## N° {h['numero']} — {t(h['titre'])}")
print(f"Sort le **{jours[j.weekday()]} {j.day} {mois[j.month - 1]} à 3 h 12** · {h['sousGenre']} · {h['lieu']}, {h['epoque']}")
print(f"Format : {h['format']} · mise en page `{h['miseEnPage']}`")
print(f"Ambiance : fond `{f['effet']}` · musique {m['tonique']} {m['gamme']}, {len(m['couches'])} couches ({', '.join(c['type'] for c in m['couches'])}), évolution {m['evolution']} · palette {th['palette']['fond']} / {th['palette']['surface']} / {th['palette']['accent']} · polices {th['polices']['titre']} + {th['polices']['texte']}")
print(f"Résumé : {h['resume']}\n\n---\n")
blocs = h['blocs']
if h.get('origine') == 'traduction':   # la personne connaît déjà le texte : le début et la fin suffisent
    print(f"*(Traduction : {len(blocs)} blocs. Voici le début et la fin.)*\n")
    scenes = [b.get('decor') for b in blocs if b['type'] == 'scene']
    print(f"*Lieux : {' → '.join(dict.fromkeys(scenes))}*\n")
    blocs = blocs[:4] + [{'type': 'pause'}] + blocs[-4:]
for b in blocs:
    ty = b['type']
    if ty == 'titre': print(f"\n**{t(b['texte'])}**\n")
    elif ty == 'paragraphe': print(t(b['texte']) + '\n')
    elif ty == 'dialogue': print(f"{(b.get('horodatage') + ' ') if b.get('horodatage') else ''}**{t(b.get('personnage', ''))}** : {t(b['texte'])}\n")
    elif ty == 'sms': print(f"[{'moi' if b['de'] == 'moi' else (b.get('nom') or 'eux')}{(' ' + b['heure']) if b.get('heure') else ''}] {t(b['texte'])}\n")
    elif ty == 'note': print(f"*(note : {t(b['texte'])})*\n")
    elif ty == 'citation': print(f"> {t(b['texte'])}{(' — ' + t(b['source'])) if b.get('source') else ''}\n")
    elif ty == 'pause': print('* * *\n')
    elif ty == 'evenement': print(f"`⚡ {b.get('effet', 'aucun')} / {b.get('son', 'aucun')}`{(' « ' + t(b['texte']) + ' »') if b.get('texte') else ''}\n")
    elif ty == 'image': print(f"*[illustration : {t(b.get('legende', ''))}]*\n")
    elif ty == 'signature': print(f"— {t(b['texte'])}\n")
    elif ty == 'champs': print('\n'.join(f"- {t(c.get('nom'))} : {t(c.get('valeur'))}" for c in b['champs']) + '\n')
    elif ty == 'tampon': print(f"[TAMPON : {t(b['texte'])}]\n")
    elif ty == 'geste': print(f"👆 **[{t(b['texte'])}]** ({'maintenir' if b.get('mode') == 'maintenir' else 'toucher'}) → vue « {(b.get('vue') or {}).get('decor', '?')} »\n")
    elif ty == 'scene': print(f"`🎬 {b.get('decor') or 'décor de base'}{(' · ' + '+'.join(b['ambiance'] if isinstance(b['ambiance'], list) else [b['ambiance']])) if b.get('ambiance') else ''}`\n")
print(f"---\n\n**Phrase finale :** {t(h['phraseFinale'])}")
APERCU
```

Ta **réponse finale** (le message que la personne lira) contient, dans cet ordre :
1. une ligne d'état : quelles histoires ont été écrites cette nuit et poussées (« n° 4 sortie à 3 h 12 · n° 5 écrite pour demain »), ou le problème rencontré ;
2. l'**aperçu complet** de l'histoire de demain (la sortie du script ci-dessus, telle quelle) ;
3. une phrase : « Réponds ici si tu veux une retouche avant demain 3 h 12. »

**Retouches** : si la personne répond avec des changements, modifie uniquement `stories/$DEMAIN.json` (et son entrée dans l'index si le titre, la palette, etc. changent), revalide (étape 7), puis commit `3h12 — n° N — Titre (retouche)` et pousse sur `main`.

---

## Liste de vérification finale

- [ ] Histoire d'aujourd'hui présente ; histoire de demain écrite ; aucune histoire déjà sortie modifiée ; aucun autre fichier modifié.
- [ ] Histoire en français, québécois naturel dans les dialogues si elle se passe au Québec.
- [ ] Vraiment effrayante : montée implacable, une scène d'effroi prolongée, de vrais enjeux, pas de fin heureuse.
- [ ] **Gros retournement** dans le dernier quart, préparé par 2-3 indices, type différent des 10 dernières histoires, noté dans `retournement`.
- [ ] Histoire originale : 900 à 1600 mots, `phraseFinale` courte et glaçante.
- [ ] Pas de champ `question`. Histoires choisies par la personne publiées en premier (traduction fidèle, `origine`, aucun crédit).
- [ ] Aucune histoire après le 31 octobre 2026.
- [ ] Consignes de la personne (courrier de 18 h, retouches) appliquées si elles visaient cette histoire.
- [ ] Le format est utilisé pour vrai (le document ressemble au document).
- [ ] 3 à 8 événements (14 pour une traduction), bien placés ; 2 à 5 gestes, **chacun avec sa vue** ; une scène à chaque changement de lieu (décor + ambiance) ; `epier` quand il sert ; **toutes** les rafales de textos en `notification` avec `"son": "texto"` ; effets de texte avec parcimonie.
- [ ] Français simple et facile à lire, compréhensible au Québec, sans québécois écrit ni argot de France.
- [ ] Mise en page différente de l'histoire précédente ; sous-genre, lieu, palette et musique variés.
- [ ] Contraste texte/surface ≥ 7 ; polices de la liste vérifiée.
- [ ] Illustration SVG originale et décors des scènes, avec un détail inquiétant discret ; vues des gestes soignées (pas de visage « dessin animé »).
- [ ] Validation : **VALIDE ✔**.
- [ ] Un commit « 3h12 — n° N — Titre » par histoire, poussé sur `main`.
- [ ] Aperçu complet de l'histoire de demain envoyé dans la réponse finale.

## Pour vérifier visuellement (facultatif)

Si un navigateur est disponible, on peut servir le dossier (`python3 -m http.server 8312`) et ouvrir `http://localhost:8312/`. Dans l'app : Paramètres → toucher 3 fois le numéro de version → code `BURGER` → le mode DEV affiche le JSON brut et la liste des erreurs de format, et permet de coller un JSON pour le prévisualiser.

---

## Courrier de 18 h (suggestions des lecteurs)

Une deuxième routine te réveille chaque jour vers 18 h (heure de Toronto). Ce n'est **pas** une nuit d'écriture : tu n'écris aucune nouvelle histoire.

1. **Lis les suggestions** reçues depuis le dernier courrier : dans Gmail, cherche `from:formsubmit.co newer_than:1d` (objets « 3h12 — suggestion après le n° N »). Chaque courriel contient l'histoire et le mot du lecteur. Ignore les courriels de test et l'activation.
2. **Réponds dans la conversation**, court et clair :
   - le nombre de suggestions reçues ;
   - les mots libres, regroupés par thème (cite les plus parlants, tels quels) ; écarte le pourriel et tout ce qui est haineux ou viserait une vraie personne ;
   - un rappel : « L'histoire de demain (n° N, *Titre*) est déjà écrite. Dis-moi ce que je change : elle, ou les suivantes. »
3. **Rien à signaler** (aucun courriel) : une seule ligne, « Pas de suggestion aujourd'hui. Une consigne pour la prochaine nuit ? ».
4. **Gmail inaccessible** (connecteur déconnecté) : dis-le en une ligne et demande à la personne de regarder sa boîte (objet « 3h12 — suggestion ») et de te coller ce qu'elle veut garder.
5. **Quand la personne répond** :
   - une retouche de **l'histoire de demain** (pas encore sortie) : modifie `stories/$DEMAIN.json` (et son entrée dans l'index si besoin), valide (étape 7), commit `3h12 — n° N — Titre (retouche)`, pousse sur `main` ;
   - une consigne pour **les histoires suivantes** : retiens-la ; la routine de 3 h 12 l'appliquera (voir étape 0) ;
   - jamais de retouche d'une histoire déjà sortie.
