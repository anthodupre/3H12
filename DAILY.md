# DAILY.md — l'histoire de cette nuit

Tu es l'auteur de **3h12**, une web app personnelle qui publie **une histoire d'horreur inédite chaque nuit à 3 h 12** (heure de Toronto). L'app (`index.html`) est un moteur qui ne change jamais : chaque histoire est **un fichier JSON** (`stories/AAAA-MM-JJ.json`) plus une entrée dans `stories/index.json`. Ce fichier décide de tout : le texte, la mise en page, les couleurs, les polices, le fond animé, l'illustration et la musique.

**Fonctionnement « une nuit d'avance »** : une histoire datée J n'apparaît dans l'app qu'à **J, 3 h 12** (même si elle est déjà dans le repo). Chaque nuit à 3 h 12, tu t'assures donc que **deux** histoires existent :
1. **celle d'aujourd'hui**, qui sort maintenant (normalement déjà écrite la nuit précédente ; sinon tu l'écris tout de suite) ;
2. **celle de demain**, que tu écris d'avance : elle reste cachée jusqu'à demain 3 h 12.

Puis tu **envoies l'aperçu complet de celle de demain** dans ta réponse finale (étape 9), pour que la personne puisse la voir, et demander des retouches, avant sa sortie.

Ton travail : écrire des histoires vraiment effrayantes, les habiller avec soin, valider les fichiers, publier.

**Règles d'or**
1. Ne modifie **jamais** `index.html`, `SCHEMA.md`, `DAILY.md`. Tu n'ajoutes que des fichiers `stories/AAAA-MM-JJ.json` et leurs entrées dans `stories/index.json`.
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

Si les deux existent déjà : pas d'écriture ; passe directement à l'étape 9 (aperçu de demain).

## Étape 1 — Lire la référence

Lis **en entier** `SCHEMA.md` (format exact, valeurs permises, mises en page, effets, sons, couches musicales), puis `stories/index.json`.

## Étape 2 — Regarder les 30 dernières histoires

```bash
python3 - <<'EOF'
import json
h = json.load(open('stories/index.json', encoding='utf-8'))['histoires'][-30:]
print('n°  date        mise en page    fond        musique                 Qc  sous-genre / lieu')
for x in h:
    print(f"{x['numero']:<3} {x['date']}  {x['miseEnPage']:<15} {x.get('effetFond',''):<11} {x.get('tonique','')+' '+x.get('gamme',''):<23} {'Q' if x.get('auQuebec') else '-'}   {x['sousGenre']} / {x['lieu']}")
    print(f"      accent {x['palette']['accent']} · polices {x.get('polices',{}).get('titre','')} / {x.get('polices',{}).get('texte','')} · format : {x.get('format','')}")
q = sum(1 for x in h if x.get('auQuebec'))
print(f"\nAu Québec : {q} sur {len(h)} · prochain numéro : {max([x['numero'] for x in h] or [0]) + 1}")
EOF
```

À éviter absolument :
- la **même mise en page que l'histoire précédente** (interdit) ;
- une mise en page utilisée dans les **3 dernières** histoires (fortement déconseillé) ;
- un sous-genre utilisé dans les **7 dernières** ;
- un lieu, une palette (surtout la couleur `accent`), une combinaison tonique + gamme ou un effet de fond utilisés récemment ;
- les mêmes ressorts (même type de monstre, même chute, même narrateur) que les histoires récentes.

## Étape 3 — Choisir une combinaison nouvelle

**Sous-genres** (faire tourner) : horreur psychologique · folklore québécois · horreur cosmique · maison hantée · survie · body horror léger · horreur technologique · found footage · horreur maritime · horreur hivernale · cryptide · culte · rêve et paralysie du sommeil · horreur liminale · disparition · horreur médicale · conte cruel.

**Lieux** : environ **1 histoire sur 3 au Québec**. Si aucune des 3 dernières n'était au Québec, celle-ci doit l'être ; jamais 3 de suite au Québec.
- Québec : Laurentides, Haute-Côte-Nord, Gaspésie, Abitibi, Charlevoix, Bas-Saint-Laurent, Îles-de-la-Madeleine, Mauricie… Chantier de la Baie-James, camp de chasse, pourvoirie, rang de campagne, motel au bord de la 117, sous-sol d'église, couvent, sanatorium, dépanneur ouvert la nuit, traversier, pont de glace, cabane à sucre, mine abandonnée, polyvalente, station de ski fermée l'été… À toutes les époques (Nouvelle-France, 1850, 1930, 1970, 1995, aujourd'hui).
- Ailleurs : partout dans le monde et à toutes les époques (une station météo en Antarctique en 1983, un village des Carpates en 1890, un hôtel capsule à Osaka, une plateforme pétrolière en mer du Nord, une léproserie en Louisiane en 1910, un phare en Bretagne, un forum Internet en 2004…). Lieux plausibles, pas de clichés de carte postale.

**Mise en page** (une des 10, voir SCHEMA.md §6) : `manuscrit`, `journal_intime`, `rapport`, `sms`, `transcription`, `forum`, `lettre`, `recit`, `terminal`, `coupure`. Choisis celle qui **sert l'histoire** : la forme fait partie de la peur (un rapport de police froid qui déraille ; des SMS auxquels quelqu'un d'autre se met à répondre ; une transcription où une voix de trop apparaît ; un fil de forum dont les messages arrivent de plus en plus tard…).

**Format** : la description humaine du document (« Journal de bord d'un gardien de phare », « Transcription d'un appel au 911 », « Lettres d'une institutrice à sa sœur, 1911 »).

## Étape 4 — Écrire l'histoire

- **En français.** Si l'histoire se passe au Québec, les dialogues sont en **québécois naturel** (« y'est pas là », « pantoute », « c'te nuit-là », « m'as aller voir », « icitte », « astheure », « pis »), la narration reste en bon français. Sans caricature ni accumulation de sacres.
- **900 à 1600 mots** au total (5 à 8 minutes de lecture). Compte réel vérifié à l'étape 7.
- **Tension lente qui monte.** Commencer dans le banal et le précis (dates, heures, objets, gestes). La peur vient de l'**ambiance**, des **détails qui clochent** (une habitude brisée, un chiffre qui ne colle pas, un son au mauvais endroit) et de **ce qui n'est pas dit**. Montrer peu le monstre.
- **Une fin marquante** : retournement, ambiguïté troublante, ou une dernière phrase qui glace. La `phraseFinale` est affichée seule, en très grand, après l'histoire : elle doit frapper (courte, 5 à 20 mots), sans répéter le dernier bloc.
- **Utiliser le format pour vrai.** Un rapport a des sections, un ton administratif, des champs, des tampons, des passages caviardés, des annotations de supérieur. Des SMS ont des heures, des fautes de frappe, des « Vu », des silences. Un forum a des pseudos, des signatures, des modérateurs, des messages espacés dans le temps. Une transcription a des horodatages, des `[inaudible]`, des chevauchements. Un journal intime a des dates en tête de section et une écriture qui se dégrade.
- **Effets de texte avec parcimonie** (SCHEMA.md §11) : `##chuchoté##`, `||caviardé||` (jamais pour une information indispensable), `%%glitch%%` (plutôt en technologique), `^^lent^^` (une phrase clé), `__accent__`, `~~rature~~`, `[annotation]`. Styles de bloc (`gros`, `agite`, `espace`, `lent`…) pour un ou deux moments forts.
- **3 à 6 événements** (`evenement`) aux moments clés, placés **juste après** la phrase qui les justifie (un coup frappé juste après « ça a cogné », un `silence` sur l'instant de bascule, un `noir` avec un court `texte` avant la fin). Pas de jumpscare gratuit.
- Interdits : gore excessif, violence sexuelle, personnes réelles (pas de vraies personnes, pas de vrais crimes, pas de noms d'entreprises existantes dans un rôle négatif), contenu haineux. Les animaux peuvent mourir hors champ, sans détails.
- Relis-toi : orthographe, typographie française (espaces avant `: ; ? !`, guillemets « », apostrophes ’), cohérence des heures et des noms.

## Étape 5 — Habiller l'histoire

Tout l'habillage doit **servir l'histoire**. Détails et valeurs permises : SCHEMA.md §4 à §8.

- **Palette** (5 couleurs `#rrggbb`) : `fond` très sombre et teinté (bleu nuit, vert noyé, brun de cave, violet de bleu…), `surface` = l'objet (papier, écran), `texte` lisible sur `surface` (**contraste ≥ 7**), un `accent` qui a du sens (rouge tampon, ambre de lampe, vert phosphore, bleu d'écran, rouille). Pour `recit`, `surface` = `fond`.
- **Polices** : uniquement des noms de la liste vérifiée de SCHEMA.md §4.2 (ou polices système d'iPhone). Le `texte` doit rester très lisible sur un téléphone.
- **Fond animé** : un `effet` cohérent (neige pour l'hiver, brouillard pour la mer, statique pour le technologique, poussière pour une maison fermée, cendres après un incendie, lucioles pour un été trop calme, étoiles pour le cosmique, bulles sous l'eau…), `intensite` 0.3 à 0.6, `vignette` 0.6 à 0.85, `grain` 0.2 à 0.4, `scintillement` 0 à 0.3.
- **Illustration SVG originale** (`fond.svg`, obligatoire) : `viewBox='0 0 400 860'`, `preserveAspectRatio='xMidYMax slice'`, **apostrophes simples** pour les attributs, 5 à 15 Ko, silhouettes et aplats, une ou deux lumières, **un détail inquiétant discret** (une silhouette dans une fenêtre, des yeux dans le champ, une porte entrouverte, une forme sous l'eau). Pas de `<text>`, pas de script, pas d'image externe. `svgOpacite` 0.18 à 0.28. Astuce : génère le SVG avec un petit script Python (boucles pour les arbres, les vagues, les poteaux, les étoiles).
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
  "theme": {"palette": {"fond": "", "surface": "", "texte": "", "accent": "", "secondaire": ""},
            "polices": {"titre": "", "texte": "", "notes": ""}, "cssPersonnalise": ""},
  "document": {},
  "fond": {"effet": "", "intensite": 0.45, "vignette": 0.75, "grain": 0.3, "scintillement": 0.1, "svgOpacite": 0.22, "svg": ""},
  "musique": {"tempo": 56, "tonique": "", "gamme": "", "reverb": 0.7, "desaccord": 0.25, "evolution": "monte", "finale": "fondu", "couches": []},
  "blocs": [],
  "phraseFinale": ""
}
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
EFFETS_EV = ['glitch', 'noir', 'flash', 'tremblement', 'aucun']
SONS = ['coup', 'craquement', 'chuchotement', 'respiration', 'porte', 'silence', 'pas', 'grattement', 'coeur', 'statique', 'cloche', 'vibration', 'goutte', 'aucun']
COUCHES = ['drone', 'pad', 'cloche', 'pulsation', 'bruit', 'boite_a_musique', 'cordes', 'choeur', 'piano']
GAMMES = ['mineur', 'phrygien', 'locrien', 'chromatique', 'pentatonique_mineure', 'ton_par_ton', 'harmonique_mineure', 'dorien', 'diminuee', 'hongroise_mineure', 'phrygien_dominant', 'lydien', 'majeur']
EVOL = ['monte', 'stable', 'descend', 'rupture']
TYPES = ['paragraphe', 'titre', 'dialogue', 'sms', 'note', 'citation', 'pause', 'evenement', 'image', 'signature', 'champs', 'tampon']
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
avant = [x for x in liste if isinstance(x, dict) and x.get('date', '') < date]
entree = next((x for x in liste if isinstance(x, dict) and x.get('date') == date), None)
if entree is None: E(f'index.json : aucune entrée pour {date}')
if h.get('version') != 1: E('version doit valoir 1')
if h.get('date') != date: E(f'date ({h.get("date")}) ≠ nom du fichier ({date})')
attendu = max([x.get('numero', 0) for x in avant] or [0]) + 1
if h.get('numero') != attendu: E(f'numero = {h.get("numero")}, attendu {attendu}')
for c in ['titre', 'sousGenre', 'lieu', 'epoque', 'format', 'resume', 'phraseFinale']:
    if not isinstance(h.get(c), str) or not h.get(c).strip(): E(f'{c} manquant ou vide')
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
    for c in ['accent', 'secondaire']:
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
mots, nb_ev = 0, 0
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
    if t in ['paragraphe', 'titre', 'note', 'signature', 'citation', 'dialogue', 'sms'] and not (isinstance(b.get('texte'), str) and b['texte'].strip()):
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
mots += len(brut(h.get('phraseFinale', '')).split())
if not 3 <= nb_ev <= 6: E(f'{nb_ev} événements (il en faut 3 à 6)')
if mots < 850 or mots > 1700: E(f'{mots} mots (il en faut 900 à 1600)')
elif mots < 900 or mots > 1600: W(f'{mots} mots (cible : 900 à 1600)')
if entree:
    pairs = [('numero', h.get('numero')), ('titre', h.get('titre')), ('sousGenre', h.get('sousGenre')), ('lieu', h.get('lieu')),
             ('auQuebec', h.get('auQuebec')), ('epoque', h.get('epoque')), ('format', h.get('format')), ('miseEnPage', mep),
             ('tonique', m.get('tonique')), ('gamme', m.get('gamme')), ('effetFond', f.get('effet')), ('palette', pal),
             ('polices', {k: pol.get(k) for k in ['titre', 'texte', 'notes'] if k in pol}), ('evenements', nb_ev)]
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
    q = [x.get('auQuebec') for x in recentes[-3:]]
    if len(q) == 3 and not any(q) and not h.get('auQuebec'): W('aucune des 4 dernières histoires au Québec : la prochaine devrait l’être (environ 1 sur 3)')
    if len(q) >= 2 and all(q[-2:]) and h.get('auQuebec'): W('3 histoires de suite au Québec : varier les lieux')
print(f'Histoire {date} : n° {h.get("numero")} « {h.get("titre")} » · {mep} · {mots} mots · {nb_ev} événements')
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
for b in h['blocs']:
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
- [ ] 900 à 1600 mots, tension lente, fin marquante, `phraseFinale` courte et glaçante.
- [ ] Le format est utilisé pour vrai (le document ressemble au document).
- [ ] 3 à 6 événements, bien placés ; effets de texte avec parcimonie.
- [ ] Mise en page différente de l'histoire précédente ; sous-genre, lieu, palette et musique variés.
- [ ] Contraste texte/surface ≥ 7 ; polices de la liste vérifiée.
- [ ] Illustration SVG originale, avec un détail inquiétant discret.
- [ ] Validation : **VALIDE ✔**.
- [ ] Un commit « 3h12 — n° N — Titre » par histoire, poussé sur `main`.
- [ ] Aperçu complet de l'histoire de demain envoyé dans la réponse finale.

## Pour vérifier visuellement (facultatif)

Si un navigateur est disponible, on peut servir le dossier (`python3 -m http.server 8312`) et ouvrir `http://localhost:8312/`. Dans l'app : Paramètres → toucher 3 fois le numéro de version → code `BURGER` → le mode DEV affiche le JSON brut et la liste des erreurs de format, et permet de coller un JSON pour le prévisualiser.
