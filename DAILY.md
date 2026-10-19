# DAILY.md — tonight's story

You publish **3h12**, a personal web app that releases **one horror story every night at 3:12 AM** (Toronto time). The app (`index.html`) is an engine that never changes: each story is **one JSON file** (`stories/YYYY-MM-DD.json`) plus an entry in `stories/index.json`. That file decides everything: the text, the layout, the colors, the fonts, the animated background, the illustration and the music.

**The owner supplies every story**: the owner gives you existing stories (often creepypastas) whose authors have given them permission. You publish each one **verbatim**, exactly as the author wrote it, in English (step 4), on the first nights not yet released, in the order the owner gave them. **You never write a story of your own**, not even to fill an empty night. Your job: lay out each story the owner gives you, stage it and dress it with care (scenes, gestures, events, layout, colors, fonts, background, illustrations, music), validate the files, publish.

**"One night ahead"**: a story dated D only appears in the app at **D, 3:12 AM** (even if it is already in the repo). So every night at 3:12 AM, you make sure that **two** stories exist, as long as the owner has supplied them:
1. **today's**, which comes out now (normally already prepared the night before; otherwise, if a story from the owner is waiting, you prepare it right away);
2. **tomorrow's**, which you prepare in advance from the next story the owner gave you: it stays hidden until tomorrow at 3:12 AM.

If no story from the owner is waiting for a night, you write **nothing** for it, and you tell the owner that the night is empty (step 9). Otherwise, you **send the full preview of tomorrow's story** in your final reply (step 9), so the owner can see it, and ask for changes, before it comes out.

**24 hours, no more**: in the app, a story only lives from its release (D, 3:12 AM) to the next one (D+1, 3:12 AM). No archives, no favorites, no saving. Each story must therefore stand on its own.

**The season**: one story per night from **October 20 to 31, 2026** (Halloween night is the last one). Before the 20th, the app shows a countdown; after the 31st, it announces that it's over. The stories from October 17 to 19 are **tests** (`"essai": true` in the index): readable only in DEV mode, ignored for numbering and variety. **Never** publish a story dated after October 31, 2026.

**Reader suggestions**: at the end of each story, the app offers a suggestion box for the next night (**no more multiple-choice question**: never add a `question` field). The suggestions go to the owner by email; the owner reads them and decides what to do with them. You never act on a reader suggestion unless the owner asks you to.

**Golden rules**
1. **Never** modify `index.html`, `SCHEMA.md`, `DAILY.md`. You only add `stories/YYYY-MM-DD.json` files and their entries in `stories/index.json` (keep its `moteur` field as it is).
2. One story per date. **Never** rewrite a story that is already out (date ≤ today). Only tomorrow's story can be revised, and only if the owner asks.
3. The files must pass validation (step 7) **without errors** before being pushed.
4. `SCHEMA.md` is the format reference: when in doubt, it is right.
5. **Never write a story of your own.** Every story comes from the owner and is published verbatim.

---

## Step 0 — Prepare

```bash
cd <repo root>                 # the folder that contains index.html
git checkout main 2>/dev/null || true
git pull --ff-only origin main
AUJOURDHUI=$(TZ=America/Toronto date +%F)
DEMAIN=$(python3 -c "import datetime,sys;d=datetime.date.fromisoformat(sys.argv[1]);print(d+datetime.timedelta(days=1))" "$AUJOURDHUI")
for D in "$AUJOURDHUI" "$DEMAIN"; do
  if [ -f "stories/$D.json" ]; then echo "$D: already exists"; else echo "$D: TO PREPARE"; fi
done
```

(`AUJOURDHUI` = today, `DEMAIN` = tomorrow.)

**Stories from the owner**: reread the conversation. Every story the owner gave you that is not published yet takes the **first night not yet released**, in the order the owner gave them. A story that is already out is never replaced.

For **each date marked "TO PREPARE"**, in order (**today first, then tomorrow**):
- if a story from the owner is waiting, do steps 1 to 8 with `DATE` set to that date (`DATE=$AUJOURDHUI`, then `DATE=$DEMAIN`). When you prepare tomorrow's story, today's story is "the previous story" (same layout forbidden, etc.);
- if no story from the owner is waiting: write **nothing** for that date (never a story of your own). On an empty night, the app shows its "radio silence" screen. Say so in your final reply (step 9).

If both already exist: nothing to prepare; go straight to step 9 (tomorrow's preview). If `DEMAIN` is after October 31, 2026: prepare nothing for it, and simply say that the season is over.

**The owner's instructions**: reread the owner's latest messages in the conversation (especially their replies to the previous night's preview). Any instruction for "the next story" or "the next ones" that has not been applied yet **applies to the one you are preparing now** (without breaking the rules of this file). Mention it in the status line of your final reply.

## Step 1 — Read the reference

Read `SCHEMA.md` **in full** (exact format, allowed values, layouts, effects, sounds, music layers), then `stories/index.json`, then the owner's story in full (the original text, as the owner gave it).

## Step 2 — Look at the last 30 stories

```bash
python3 - <<'EOF'
import json
h = [x for x in json.load(open('stories/index.json', encoding='utf-8'))['histoires'] if not x.get('essai')][-30:]
print('No. date        layout          background  music                   sub-genre / place')
for x in h:
    print(f"{x['numero']:<3} {x['date']}  {x['miseEnPage']:<15} {x.get('effetFond',''):<11} {x.get('tonique','')+' '+x.get('gamme',''):<23} {x['sousGenre']} / {x['lieu']}")
    print(f"      accent {x['palette']['accent']} · fonts {x.get('polices',{}).get('titre','')} / {x.get('polices',{}).get('texte','')} · format: {x.get('format','')}")
    print(f"      twist: {x.get('retournement', '?')}")
print(f"\nNext number: {max([x['numero'] for x in h] or [0]) + 1}")
EOF
```

The text, the sub-genre, the place, the era and the twist belong to the original: you only describe them. Variety is about the **dressing**. Avoid at all costs:
- the **same layout as the previous story** (forbidden), **except `recit`**: it is the only one where the text sits directly on the scenery, so the one where the environment (scenes, decors) shows best; it may come back. The others (paper, phone, screen) hide the scenery behind the object: choose them only when the document itself is frightening;
- a layout other than `recit` used in the **last 3** stories (strongly discouraged);
- a palette (especially the `accent` color), a tonic + scale combination or a background effect used recently.

## Step 3 — Describe the story and choose its layout

**Sub-genre** (`sousGenre`): the one closest to the original, for example: psychological horror · domestic horror · Québec folklore · cosmic horror · haunted house · survival · light body horror · technological horror · found footage · maritime horror · winter horror · cryptid · cult · dreams and sleep paralysis · liminal horror · disappearance · medical horror · cruel fairy tale.

**Place and era** (`lieu`, `epoque`): those of the original, said plainly ("A suburban house, in the United States", "today"). Don't invent details the original doesn't give.

**Layout** (one of the 10, see SCHEMA.md §6): `manuscrit`, `journal_intime`, `rapport`, `sms`, `transcription`, `forum`, `lettre`, `recit`, `terminal`, `coupure`. Choose the one that **serves the text**: `recit` for a first-person account (the most common case); another layout only when the original really is that kind of document (a report, text messages, a transcript, a forum thread, letters, a diary…).

**Format**: the human description of the document ("Account written in one sitting, in a motel room", "Transcript of a 911 call", "Letters from a schoolteacher to her sister, 1911").

## Step 4 — Lay out the owner's story (verbatim)

- **Verbatim, exactly as the author wrote it.** No translation, no rewriting, no corrections (not the spelling, not the punctuation, not the profanity), nothing cut, nothing added, the paragraphs in the same order. Keep the author's typography as it is. Write the file with `json.dump` (step 6): the author's quotation marks and backslashes are then escaped automatically.
- **No credit**: no author, no source site, no link, anywhere (the owner has the author's permission).
- Root field `"origine": "choisie"` (and the same in the index entry). The old value `"traduction"` is still accepted by the validator (older stories), but new stories use `"choisie"`. The length stays the original's: no word target.
- Title: the original title, verbatim.
- **One block per paragraph** of the original. A paragraph may be split into two blocks **only** to place a `scene`, a `geste` or an `evenement` between them, and only if the words stay identical and in the same order. A paragraph that is only a text message can be shown as an `sms` block (`"de": "eux"`), with the same words; when the character receives **several** texts or calls ("a flood of texts", "dozens of messages"), put them **all** in a burst `notification` (`messages`: word for word when the original quotes them; when it only describes them, short texts and missed calls that say nothing more than the original does, e.g. "Where are you?" for *all wondering where I'd gone*; `reste` for the rest of the flood). A few `pause` blocks at the story's big breaks (2 to 4).
- **Text that looks like markup**: `*text*` in italics is fine, and wanted, when the author used asterisks. But `##`, `||`, `%%`, `^^`, `__`, `~~` and `[...]` are read by the engine as effects (SCHEMA.md §11), and there is no way to escape them. If one of these sequences appears by accident in the original (rare), keep the author's text and accept the effect, or ask the owner. If the validator then reports an unclosed tag, don't change the text on your own: ask the owner.
- **3 to 14 events**, placed right after the sentence that justifies them (a noise, a door, a silence, a peeking face, texts arriving as notifications), **2 to 5 gestures with their view**, and **a scene at each change of place** (see step 4a, "Interactivity").
- `retournement`: describe the original's twist in a few words.
- Full dressing as usual (step 5): layout, palette, fonts, background, original illustration, music.
- `phraseFinale`: the very last sentence of the original, removed from the last block (so that it isn't repeated).
- Before validating, compare the blocks with the original, paragraph by paragraph: same words, same order, nothing missing.

## Step 4a — Staging guide (reference)

You never write the text. This guide describes what makes a 3h12 story frightening: use it to read the original well and to **stage** it (where the scenes, gestures and events go, and what the decors and views show), never to change or add words.

### What frightens

3h12 is **adult horror**, oppressive, that stays in your head when you turn off the light. No pumpkins, no cardboard witches, no cute winks in the dressing, ever (even in October).

- **A relentless build**: 0–25% unease (one detail is off, then another) · 25–60% the strangeness becomes undeniable · 60–85% confrontation, panic, no escape anymore · 85–100% **the twist**, then the final blow. Follow the original's build: few effects during the early unease, more as the strangeness becomes undeniable, the strongest ones for the confrontation and the twist.
- **The prolonged scene of dread** (the reader holds their breath over several blocks: the thing is there, very close, and nothing happens… yet): find it in the original and support it (an ambient sound, a `silence`, a gesture just before the reveal).
- **Precise sensory details**: smells (wet earth, copper, cellar), textures (fingers too long, skin too cold), sounds in the wrong place, familiar gestures made by the wrong person. The scenes, ambient sounds and decors echo the ones the original gives.

### The twist

A good twist **changes the meaning of everything before it**: the reader wants to reread from the start.
- Spot the twist and its 2 or 3 clues in the original: no effect, decor variant or view should give them away too early. Keep the strongest effects for the last quarter.
- Write the twist type in a few words in the `retournement` field (see SCHEMA.md). Usual types (vocabulary to describe it):
  1. the narrator is the monster, or the culprit;
  2. the one who helps or reassures is the threat;
  3. the threat was already inside: the safe place is the trap;
  4. the curse is passed on… and the reader has just received it;
  5. the one who comes back is not the one who left (double, replacement);
  6. time loop or time inversion: the end comes before the beginning;
  7. the victim was the bait, or the willing sacrifice;
  8. the group (family, village, coworkers, cult) was complicit from the start;
  9. the document itself is the weapon: reading it means taking part;
  10. what seemed supernatural is human, and that's worse (or the reverse);
  11. the danger wasn't outside but in the body;
  12. the narrator has been dead from the start.
- The validator refuses a `retournement` worded exactly like one of the last 10 stories: if that happens, word it your own way.

### Staging rules

- **Use the format for real**: when the original is a document, the layout and the `document` fields make it look like one (a report has an organization, a reference, fields; a transcript has timestamps; a diary has dates at the top of each section). Block types can follow the form (`titre` for a diary date, `dialogue` for a line of dialogue, `sms` for a text), as long as the words stay the original's.
- **Effects, sparingly**: block styles (`gros`, `agite`, `espace`, `lent`…) for one or two strong moments. Don't add text markup (SCHEMA.md §11) inside the author's text.
- **Music stays in the background**: a low drone and textures carry it; melodic layers (`piano`, `boite_a_musique`, `cloche`) are rare and quiet (`densite` 0.05 to 0.1, `volume` 0.08 to 0.16), otherwise they get annoying fast.
- **No jolt or black screen unless the text earns it**: `tremblement` (the scenery takes a jolt; the text never moves) only for an impact in the same room (a door slamming hard enough to rattle the mirror, a crash), never for a sound heard from another room (play only the sound). A fall is a sound (`chute`), not a black screen.
- **Music never loops**: the engine plays the music box as a short phrase now and then and the heartbeat in brief bursts; keep melodic layers sparse anyway (above).
- **Events** (`evenement`) at key moments, placed **right after** the sentence that justifies them (a knock right after "something knocked", a `silence` on the moment everything tips over, a `noir` before the end). No gratuitous jump scares.
- **Interactivity (mandatory)**: the reader must live the story, not just read it.
  - **The environment follows the story** (`scene` blocks, SCHEMA.md §9a): **every time the place changes** (kitchen, shower, car, motel…), a scene changes the scenery (the illustration crossfades), the tint, the animated background and the **looping ambient sound** (`frigo` fridge, `douche` shower, `gouttes` drips, `voiture` car, `horloge` clock, `vent` wind, `climatiseur` air conditioner, `neon` neon buzz, `mouches` flies, `souffle` breathing). And when the creature appears in the place, a **variant of the scenery where you can see it** (slow fade so that it appears gently; `"fondu": 0` so that it disappears at once). 4 to 20 scenes per story. The decors are drawn once in `fond.decors` and named.
  - **Transitions** (`"transition"` on a scene, SCHEMA.md §9a): a change of place goes **through black** (default). A person **never dissolves** in or out: hard cut (`"coupe"`) to a decor where she **moves in**, and another where she **moves out** when she leaves (snaps back behind the corner, scuttles off, glides out the door, drops out of sight). A discovery can be a slow camera push-in. No text windows (gaps in the text): the owner doesn't want them.
  - **The scenery acts out the text** (SVG animations in the decors, SCHEMA.md §9a): what the sentence describes, the decor does, slowly: she pulls her head back out of the shower and her blurry shape backs away, her face slides out from behind the island, the lid closes on her, drool runs down the glass, her body trembles with delight, her hands won't keep still. Draw **exactly what the text says** (a mouth that "hangs open" is drawn wide open, not as a closed grin).
  - **2 to 5 gestures** (`geste` block, SCHEMA.md §9) at moments when the character hesitates: "Look under the door", "Turn your head", "Open the closet", "Turn around", "Open the photo". **A gesture must always SHOW something**: every gesture has a `vue` (a full-screen image of what the character sees, with an `entree` of `baisser` (lean down), `tourner` (turn the head), `ouvrir` (open), `zoom`, `photo` or `fondu` (fade), and a sound). A button that only lets the reading continue is useless: no gesture without a `vue`. `"mode": "maintenir"` (hold the finger down, 1.5 to 2.5 s) for gestures the reader dreads; `"toucher"` (tap) for the others. The gesture goes **just before** the reveal; the text that follows tells what the view has just shown.
  - **`epier`** (a face peeks out from an edge of the screen, `cote`: `droite` right, `gauche` left, `bas` bottom, `haut` top): 1 to 3 times, **only when the text is talking about the creature (or about being watched) at that moment**. Never while the characters are looking for it without finding it: it spoils the discovery (same rule for the scenery variants where you can see it). The last one can be at the very end. **The face that comes out is the face of the story's creature**: draw it in `fond.decors.epieur` (`viewBox='0 0 160 260'`, the face centered, the hair fading out at the bottom, never a hard black block), otherwise the engine's default face appears. Variants (e.g. `epieur-beant`, mouth hanging open) are chosen per event with `"visage"`.
  - **`notification`** (text messages arriving at the top of the screen, with the phone sound: `"son": "texto"`): when a message arrives in the story. **When someone writes several times, put ALL of them** in `messages` (they stack up, an "and N more" counter climbs, `reste` adds a lot at once); `"app": "telephone"` for missed calls.
  - **`appel`** (the reader's phone rings, full screen, `titre` = who is calling; Decline or Accept, and if they accept, they hear breathing): when someone calls in the story. Once per story at most.
  - **`epier` with `"attaque": true`**: the face slowly comes closer; if the reader doesn't touch it in time, it leaps at the screen. Not before the middle of the story (the first `epier` must be harmless, so the reader learns), 1 to 3 times, often including the last one at the very end.
- **Never shown in the decors and views**: sexual violence, detailed torture, gratuitous gore, children explicitly hurt, real people, hateful content.
- **Everything you write yourself** (gesture labels, `resume`, `format`, `lieu`, `retournement`…) is in natural, clear, readable English: plain words, short sentences, easy to read on a phone at night; standard English typography.
- **October 31**: the Halloween story is the event of the year (the app shows a countdown). Give it the most careful staging of the month.

## Step 5 — Dress the story

All the dressing must **serve the story**. Details and allowed values: SCHEMA.md §4 to §8.

- **Palette** (5 colors `#rrggbb`): `fond` very dark and tinted (midnight blue, drowned green, cellar brown, bruise purple…), `surface` = the object (paper, screen), `texte` readable on `surface` (**contrast ≥ 7**), an `accent` that means something (stamp red, lamp amber, phosphor green, screen blue, rust). For `recit`, `surface` = `fond`.
- **Fonts**: only names from the verified list in SCHEMA.md §4.2 (or iPhone system fonts). The `texte` font must stay very readable on a phone.
- **Animated background**: a consistent `effet` (`neige` snow for winter, `brouillard` fog for the sea, `statique` static for technological horror, `poussiere` dust for a closed-up house, `cendres` ashes after a fire, `lucioles` fireflies for a summer that's too quiet, `etoiles` stars for cosmic horror, `bulles` bubbles under water…), `intensite` 0.3 to 0.6, `vignette` 0.6 to 0.85, `grain` 0.2 to 0.4, `scintillement` 0 to 0.3.
- **Original SVG illustration** (`fond.svg`, mandatory): `viewBox='0 0 400 860'`, `preserveAspectRatio='xMidYMax slice'`, **single quotes** for attributes, 5 to 15 KB, silhouettes and flat shapes, one or two lights, **one discreet unsettling detail** (a silhouette in a window, eyes in a field, a door ajar, a shape under the water). No `<text>`, no script, no external image. `svgOpacite` 0.35 to 0.5 (the text has a shadow that keeps it readable: the scenery must **show**); in scenes, `opacite` 0.45 to 0.56 when the creature is in the scenery. Tip: generate the SVG with a small Python script (loops for trees, waves, poles, stars).
- **Scene decors** (`fond.decors`, one SVG per place and per variant, same rules as `fond.svg`, 2 to 15 KB each): draw them with a Python script and reusable functions (one place = one function, with an `elle=True` parameter for the variant where the creature appears, in the same spot from one variant to the next). The creature is **small and discreet** in the scenery (at the corner of a wall, in the gap of a door, behind a window), and pale on a dark background so that it shows through the text. The **views** of the gestures (also in `fond.decors`) are full screen and can be more detailed: mostly black, a light on what matters, grain (`feTurbulence`), a little blur (`feGaussianBlur`). No "cartoon" faces: eyes wide open with white all around, eyebrows, dark circles, teeth in a continuous row attached to the gum, hair falling over the face.
- **Images in the text** (optional, `image` block): a sketch, a map, a piece of evidence that the original mentions, when it adds to the story (landscape `viewBox`, e.g. `0 0 320 200`, simple lines).
- **Music**: choose `gamme` and `tonique` according to the mood (melancholy: `dorien`; ancient/ritual: `phrygien`, `phrygien_dominant`; nightmare: `diminuee`, `locrien`; unreal/dream: `ton_par_ton`; corrupted innocence: `majeur` or `lydien` with `desaccord` 0.35+ and a `boite_a_musique`). 3 to 6 layers: a base (`drone` or `bruit`), a texture, and one or two layers with a `seuil` (0.5 to 0.8) that only come in when the tension rises. `evolution`: `monte` (rising) by default, `rupture` if the story tips over all at once (set `rupture` to the position of the break, e.g. 0.7). `finale`: `silence` for an abrupt ending, `fondu` (fade) otherwise.
- **document** (SCHEMA.md §5): fill in the fields useful to the chosen layout (organization, reference, contact, recipient, newspaper…), from what the original gives.

## Step 6 — Write the files

Recommended method (avoids any escaping error): a small Python script that builds the story as a dictionary and writes it with `json.dump`.

```bash
cat > /tmp/histoire.py <<'EOF'
import json, os
DATE = os.environ['DATE']
histoire = {
  "version": 1, "numero": 0, "date": DATE,            # numero = last number + 1
  "titre": "", "sousTitre": "", "sousGenre": "", "lieu": "", "epoque": "", "auQuebec": False,   # auQuebec: optional, true if the story is set in Québec
  "format": "", "resume": "", "avertissement": "", "miseEnPage": "",
  "retournement": "",                                  # twist type (see step 4a), e.g. "the one who reassures is the threat"
  "origine": "choisie",                                # every story is chosen by the owner and published verbatim
  "theme": {"palette": {"fond": "", "surface": "", "texte": "", "accent": "", "secondaire": ""},
            "polices": {"titre": "", "texte": "", "notes": ""}, "cssPersonnalise": ""},
  "document": {},
  "fond": {"effet": "", "intensite": 0.45, "vignette": 0.75, "grain": 0.3, "scintillement": 0.1, "svgOpacite": 0.42, "svg": "", "decors": {}},
  "musique": {"tempo": 56, "tonique": "", "gamme": "", "reverb": 0.7, "desaccord": 0.25, "evolution": "monte", "finale": "fondu", "couches": []},
  "blocs": [],
  "phraseFinale": ""
}
# ... fill in here (the original text, verbatim, in blocs; svg; decors) ...
json.dump(histoire, open(f'stories/{DATE}.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)

# Update the index (entry sorted by date)
idx = json.load(open('stories/index.json', encoding='utf-8'))
idx['histoires'] = [x for x in idx['histoires'] if x['date'] != DATE]
idx['histoires'].append({
  "date": DATE, "numero": histoire["numero"], "titre": histoire["titre"], "sousGenre": histoire["sousGenre"],
  "lieu": histoire["lieu"], "auQuebec": histoire.get("auQuebec", False), "epoque": histoire["epoque"], "format": histoire["format"],
  "miseEnPage": histoire["miseEnPage"], "tonique": histoire["musique"]["tonique"], "gamme": histoire["musique"]["gamme"],
  "effetFond": histoire["fond"]["effet"], "palette": histoire["theme"]["palette"],
  "polices": {k: v for k, v in histoire["theme"]["polices"].items() if k in ("titre", "texte", "notes")},
  "evenements": sum(1 for b in histoire["blocs"] if b["type"] == "evenement"),
  "retournement": histoire["retournement"],
  **({"origine": histoire["origine"]} if histoire.get("origine") in ("choisie", "traduction") else {}),
})
idx['histoires'].sort(key=lambda x: x['date'])
json.dump(idx, open('stories/index.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
print('Files written.')
EOF
DATE=$DATE python3 /tmp/histoire.py
```

The index entry must repeat **exactly** the story's values (the validator checks it).

## Step 7 — Validate (mandatory)

First, the JSON syntax:

```bash
python3 -m json.tool "stories/$DATE.json" > /dev/null && python3 -m json.tool stories/index.json > /dev/null && echo "JSON valid"
```

Then the full validator (format, allowed values, contrast, length, events, consistency with the index, variety). Copy it as is:

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
SONS = ['chute', 'crac', 'garage', 'coups_garage', 'video_colis', 'coup', 'craquement', 'chuchotement', 'respiration', 'porte', 'silence', 'pas', 'grattement', 'coeur', 'statique', 'cloche', 'vibration', 'goutte', 'texto', 'mouches', 'aucun']
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
        E(f'{ou}: must start with <svg'); return
    if "xmlns='http://www.w3.org/2000/svg'" not in svg and 'xmlns="http://www.w3.org/2000/svg"' not in svg:
        E(f"{ou}: xmlns='http://www.w3.org/2000/svg' missing")
    if not svg.strip().endswith('</svg>'): E(f'{ou}: must end with </svg>')
    if re.search(r'<script|<foreignObject|\son\w+\s*=|href\s*=\s*["\']https?:|<image', svg, re.I): E(f'{ou}: forbidden content (script, foreignObject, on… attribute, external link, image)')
    # (an animated decor carries its movements: a little more room)
    if len(svg) > 40000: E(f'{ou}: too big ({len(svg)} characters, max 40000)')
    elif len(svg) > 22000: W(f'{ou}: big ({len(svg)} characters), aim for under 22000')
    if svg.count('<') != svg.count('>'): W(f'{ou}: number of < and > differs, check the tags')
def verifier_balisage(t, ou):
    if not isinstance(t, str): return
    for b in ['##', '||', '%%', '^^', '__', '~~']:
        if t.count(b) % 2: E(f'{ou}: unclosed {b} tag')
chemin = f'stories/{date}.json'
if not re.fullmatch(r'\d{4}-\d{2}-\d{2}', date): print('Usage: python3 - YYYY-MM-DD'); sys.exit(2)
try:
    h = json.load(open(chemin, encoding='utf-8'))
except Exception as e:
    print(f'ERROR: {chemin} unreadable or invalid JSON: {e}'); sys.exit(1)
try:
    idx = json.load(open('stories/index.json', encoding='utf-8'))
except Exception as e:
    print(f'ERROR: stories/index.json unreadable or invalid JSON: {e}'); sys.exit(1)
liste = idx.get('histoires') if isinstance(idx, dict) else None
if not isinstance(liste, list): E('index.json: "histoires" must be an array'); liste = []
dates = [x.get('date') for x in liste if isinstance(x, dict)]
if len(dates) != len(set(dates)): E('index.json: a date appears twice')
if dates != sorted(dates): E('index.json: entries must be sorted by ascending date')
avant = [x for x in liste if isinstance(x, dict) and x.get('date', '') < date and not x.get('essai')]  # test stories don't count
entree = next((x for x in liste if isinstance(x, dict) and x.get('date') == date), None)
if entree is None: E(f'index.json: no entry for {date}')
if h.get('version') != 1: E('version must be 1')
if h.get('date') != date: E(f'date ({h.get("date")}) ≠ file name ({date})')
attendu = max([x.get('numero', 0) for x in avant] or [0]) + 1
if h.get('numero') != attendu: E(f'numero = {h.get("numero")}, expected {attendu}')
for c in ['titre', 'sousGenre', 'lieu', 'epoque', 'format', 'resume', 'phraseFinale', 'retournement']:
    if not isinstance(h.get(c), str) or not h.get(c).strip(): E(f'{c} missing or empty')
if 'question' in h: E('question: no more multiple-choice questions (the owner doesn’t want them anymore); remove the field')
choisie = h.get('origine') in ('choisie', 'traduction')  # story chosen by the owner, published verbatim ("traduction" = legacy value)
if not choisie: E('origine must be "choisie" (or the legacy "traduction"): every story comes from the owner, published verbatim')
if date > '2026-10-31': E('after October 31, 2026: the season is over, no story')
if date < '2026-10-20': E('before October 20, 2026: those are tests, write nothing')
if 'auQuebec' in h and not isinstance(h['auQuebec'], bool): E('auQuebec (optional) must be true or false')
mep = h.get('miseEnPage')
if mep not in MEPS: E(f'invalid miseEnPage: {mep}')
for c in ['titre', 'sousTitre', 'phraseFinale', 'avertissement']: verifier_balisage(h.get(c), c)
th = h.get('theme') or {}
pal = th.get('palette') or {}
for c in ['fond', 'surface', 'texte', 'accent', 'secondaire']:
    if not hexok(pal.get(c)): E(f'theme.palette.{c} must be a #rrggbb color')
if all(hexok(pal.get(c)) for c in ['fond', 'surface', 'texte', 'accent', 'secondaire']):
    ct = contraste(pal['texte'], pal['surface'])
    if ct < 4.5: E(f'text/surface contrast too low: {ct:.1f} (minimum 4.5, aim for 7)')
    elif ct < 7: W(f'text/surface contrast: {ct:.1f} (aim for 7 or more)')
    for c in ([] if mep == 'sms' else ['accent', 'secondaire']):  # in SMS these are bubble backgrounds
        cc = contraste(pal[c], pal['surface'])
        if cc < 3: W(f'{c}/surface contrast low: {cc:.1f} (aim for 3 or more)')
    if mep == 'recit' and pal['surface'] != pal['fond']: W('recit: surface should be equal to fond')
pol = th.get('polices') or {}
for c in ['titre', 'texte']:
    if not isinstance(pol.get(c), str) or not pol.get(c).strip(): E(f'theme.polices.{c} missing')
if 'cssPersonnalise' in th and not isinstance(th['cssPersonnalise'], str): E('theme.cssPersonnalise must be a string')
if '@import' in (th.get('cssPersonnalise') or ''): W('cssPersonnalise: @import is ignored')
f = h.get('fond') or {}
if f.get('effet') not in EFFETS_FOND: E(f'invalid fond.effet: {f.get("effet")}')
for c in ['intensite', 'vignette', 'grain', 'scintillement', 'svgOpacite']:
    if c in f and not num(f[c], 0, 1): E(f'fond.{c} must be a number between 0 and 1')
if 'couleur' in f and not hexok(f['couleur']): E('fond.couleur must be #rrggbb')
if not f.get('svg'): E('fond.svg missing: every story has its own original illustration')
else: verifier_svg(f['svg'], 'fond.svg')
decors = f.get('decors') or {}
if not isinstance(decors, dict): E('fond.decors must be an object { "name": "<svg…>" }'); decors = {}
for nom, svg in decors.items(): verifier_svg(svg, f'fond.decors.{nom}')
def verifier_ambiance(a, ou):
    for x in (a if isinstance(a, list) else [a]):
        if x not in AMBIANCES: E(f'{ou}: unknown ambiance "{x}" (values: {", ".join(AMBIANCES)})')
if 'ambiance' in f: verifier_ambiance(f['ambiance'], 'fond.ambiance')
utilises = set()
taille = len(json.dumps(h, ensure_ascii=False))
if taille > 450000: E(f'file too big ({taille // 1000} KB): lighten the decors')
elif taille > 350000: W(f'big file ({taille // 1000} KB)')
m = h.get('musique') or {}
if not num(m.get('tempo'), 30, 180): E('musique.tempo must be between 30 and 180')
if not re.fullmatch(r'(?i)(do|re|ré|mi|fa|sol|la|si|[a-g])(#|b)?', str(m.get('tonique', ''))): E(f'invalid musique.tonique: {m.get("tonique")}')
if m.get('gamme') not in GAMMES: E(f'invalid musique.gamme: {m.get("gamme")} (values: {", ".join(GAMMES)})')
if m.get('evolution') not in EVOL: E(f'invalid musique.evolution: {m.get("evolution")}')
for c in ['reverb', 'desaccord', 'volume']:
    if c in m and not num(m[c], 0, 1): E(f'musique.{c} must be between 0 and 1')
if 'finale' in m and m['finale'] not in ['fondu', 'silence', 'continuer']: E('invalid musique.finale')
couches = m.get('couches')
if not isinstance(couches, list) or not 2 <= len(couches) <= 8: E('musique.couches: 2 to 8 layers (6 at most recommended)')
else:
    for i, c in enumerate(couches):
        ou = f'musique.couches[{i}]'
        if not isinstance(c, dict): E(f'{ou} must be an object'); continue
        if c.get('type') not in COUCHES: E(f'invalid {ou}.type: {c.get("type")}')
        if 'onde' in c and c['onde'] not in ONDES: E(f'invalid {ou}.onde: {c["onde"]}')
        if 'octave' in c and not num(c['octave'], 0, 7): E(f'{ou}.octave between 0 and 7')
        if 'filtre' in c and not num(c['filtre'], 60, 18000): E(f'{ou}.filtre between 60 and 18000')
        for k in ['volume', 'densite', 'seuil']:
            if k in c and not num(c[k], 0, 1): E(f'{ou}.{k} between 0 and 1')
blocs = h.get('blocs')
mots, nb_ev, nb_gestes, nb_scenes = 0, 0, 0, 0
if not isinstance(blocs, list) or not blocs: E('blocs missing or empty'); blocs = []
for i, b in enumerate(blocs):
    ou = f'blocs[{i}]'
    if not isinstance(b, dict): E(f'{ou} must be an object'); continue
    t = b.get('type')
    if t not in TYPES: E(f'invalid {ou}.type: {t}'); continue
    if 'style' in b:
        st = b['style'].split() if isinstance(b['style'], str) else b['style']
        for s in st:
            if s not in STYLES: E(f'unknown {ou}.style: {s}')
    for k in ['texte', 'legende', 'source', 'personnage', 'signature']:
        verifier_balisage(b.get(k), f'{ou}.{k}')
        if isinstance(b.get(k), str) and k in ['texte', 'legende', 'source']: mots += len(brut(b[k]).split())
    if t == 'geste':
        nb_gestes += 1
        if b.get('mode', 'toucher') not in ['toucher', 'maintenir']: E(f'{ou} (geste): mode "toucher" or "maintenir"')
        if 'duree' in b and not num(b['duree'], 400, 6000): E(f'{ou} (geste): duree between 400 and 6000 ms')
        if b.get('son', 'aucun') not in SONS: E(f'{ou} (geste): invalid son')
        mots -= len(brut(b.get('texte', '')).split())  # the button label doesn't count as text
        v = b.get('vue')
        if not isinstance(v, dict): E(f'{ou} (geste): "vue" missing — a gesture must show something')
        else:
            if v.get('decor'):
                utilises.add(v['decor'])
                if v['decor'] not in decors: E(f'{ou} (geste): vue.decor "{v["decor"]}" not found in fond.decors')
            elif v.get('svg'): verifier_svg(v['svg'], f'{ou}.vue.svg')
            else: E(f'{ou} (geste): vue with neither decor nor svg')
            if v.get('entree', 'fondu') not in ENTREES: E(f'{ou} (geste): vue.entree must be one of {", ".join(ENTREES)}')
            if v.get('son', 'aucun') not in SONS: E(f'{ou} (geste): invalid vue.son')
            if 'duree' in v and not num(v['duree'], 1200, 30000): E(f'{ou} (geste): vue.duree between 1200 and 30000 ms')
    if t == 'scene':
        nb_scenes += 1
        if b.get('decor'):
            utilises.add(b['decor'])
            if b['decor'] not in decors: E(f'{ou} (scene): decor "{b["decor"]}" not found in fond.decors')
        elif 'svg' in b: verifier_svg(b['svg'], f'{ou}.svg')
        if 'effet' in b and b['effet'] not in EFFETS_FOND: E(f'{ou} (scene): invalid effet')
        for k in ['opacite', 'intensite', 'scintillement', 'vignette', 'teinteOpacite', 'volume']:
            if k in b and not num(b[k], 0, 1): E(f'{ou} (scene): {k} between 0 and 1')
        for k in ['couleur', 'teinte']:
            if k in b and not hexok(b[k]): E(f'{ou} (scene): {k} must be #rrggbb')
        if 'ambiance' in b: verifier_ambiance(b['ambiance'], f'{ou} (scene)')
        if 'transition' in b and b['transition'] not in ['noir', 'coupe', 'fondu']: E(f'{ou} (scene): transition = noir, coupe or fondu')
        if 'fenetre' in b: E(f'{ou} (scene): no "fenetre" (the owner does not want gaps in the text)')
        if 'fondu' in b and not num(b['fondu'], 0, 6000): E(f'{ou} (scene): fondu between 0 and 6000 ms')
    if t in ['paragraphe', 'titre', 'note', 'signature', 'citation', 'dialogue', 'sms', 'geste'] and not (isinstance(b.get('texte'), str) and b['texte'].strip()):
        E(f'{ou} ({t}): texte missing')
    if t == 'dialogue' and not b.get('personnage'): W(f'{ou} (dialogue): personnage missing')
    if t == 'sms' and b.get('de') not in ['moi', 'eux']: E(f'{ou} (sms): de must be "moi" or "eux"')
    if t == 'sms' and 'statut' in b and b['statut'] != 'non_distribue': E(f'{ou} (sms): statut must be "non_distribue"')
    if t == 'image': verifier_svg(b.get('svg'), f'{ou}.svg')
    if t == 'champs':
        if not isinstance(b.get('champs'), list) or not b['champs']: E(f'{ou} (champs): champs list is empty')
        else:
            for c in b['champs']: mots += len(brut(f'{c.get("nom", "")} {c.get("valeur", "")}').split())
    if t == 'tampon' and 'rotation' in b and not num(b['rotation'], -30, 30): E(f'{ou} (tampon): rotation between -30 and 30')
    if t == 'evenement':
        nb_ev += 1
        if b.get('effet', 'aucun') not in EFFETS_EV: E(f'{ou}: invalid effet: {b.get("effet")}')
        if b.get('son', 'aucun') not in SONS: E(f'{ou}: invalid son: {b.get("son")}')
        if b.get('effet', 'aucun') == 'aucun' and b.get('son', 'aucun') == 'aucun': E(f'{ou}: event with neither effet nor son')
        if 'duree' in b and not num(b['duree'], 100, 15000): E(f'{ou}: duree in milliseconds (100 to 15000)')
        if 'nombre' in b and not num(b['nombre'], 1, 12): E(f'{ou}: nombre between 1 and 12')
        if b.get('effet') == 'notification':
            msgs = b.get('messages')
            if msgs is not None and (not isinstance(msgs, list) or not msgs or len(msgs) > 40): E(f'{ou}: messages = 1 to 40 texts')
            if not msgs and not (isinstance(b.get('texte'), str) and b['texte'].strip()): E(f'{ou}: notification with neither texte nor messages')
            if b.get('son', 'aucun') == 'aucun': W(f'{ou}: silent notification (set "son": "texto")')
            if 'app' in b and b['app'] not in ['messages', 'telephone']: E(f'{ou}: app = "messages" or "telephone"')
            if 'intervalle' in b and not num(b['intervalle'], 150, 5000): E(f'{ou}: intervalle between 150 and 5000 ms')
            if 'reste' in b and not num(b['reste'], 0, 999): E(f'{ou}: reste between 0 and 999')
        if b.get('effet') == 'epier' and b.get('cote', 'droite') not in ['droite', 'gauche', 'bas', 'haut']: E(f'{ou}: cote = droite, gauche, bas or haut')
        if 'attaque' in b and not isinstance(b['attaque'], bool): E(f'{ou}: attaque = true or false')
        if b.get('effet') == 'epier' and b.get('visage'):
            utilises.add(b['visage'])
            if b['visage'] not in decors: E(f'{ou}: visage "{b["visage"]}" not found in fond.decors')
        if b.get('effet') == 'appel' and not (isinstance(b.get('titre'), str) and b['titre'].strip()): E(f'{ou}: appel without titre (the name of who is calling)')
mots += len(brut(h.get('phraseFinale', '')).split())
if not 3 <= nb_ev <= (14 if choisie else 8): E(f'{nb_ev} events (3 to {14 if choisie else 8} required)')
if nb_gestes < 2: E(f'{nb_gestes} gesture(s): at least 2 are required (interactivity)')
elif nb_gestes > 6: W(f'{nb_gestes} gestures: that’s a lot (2 to 5 recommended)')
if nb_scenes < 4: E(f'{nb_scenes} scene(s): at least 4 are required (the scenery follows the story, one place = one scene)')
elif nb_scenes > 40: W(f'{nb_scenes} scenes: that’s a lot')
for nom in decors:
    if nom not in utilises and nom != 'epieur' and decors[nom] != f.get('svg'): W(f'fond.decors.{nom} is not used by any scene or view')
if any(isinstance(b, dict) and b.get('effet') == 'epier' for b in blocs) and 'epieur' not in decors: W('epier without fond.decors.epieur: the engine’s face (Lynn) will come out, not the story’s creature')
if choisie:
    if mots < 300: W(f'{mots} words: incomplete text? (a chosen story is copied in full)')
elif mots < 850 or mots > 1700: E(f'{mots} words (900 to 1600 required)')
elif mots < 900 or mots > 1600: W(f'{mots} words (target: 900 to 1600)')
if entree:
    pairs = [('numero', h.get('numero')), ('titre', h.get('titre')), ('sousGenre', h.get('sousGenre')), ('lieu', h.get('lieu')),
             ('auQuebec', h.get('auQuebec', False)), ('epoque', h.get('epoque')), ('format', h.get('format')), ('miseEnPage', mep),
             ('tonique', m.get('tonique')), ('gamme', m.get('gamme')), ('effetFond', f.get('effet')), ('palette', pal),
             ('polices', {k: pol.get(k) for k in ['titre', 'texte', 'notes'] if k in pol}), ('evenements', nb_ev),
             ('retournement', h.get('retournement')), ('origine', h.get('origine'))]
    for k, v in pairs:
        ve = entree.get(k, False) if k == 'auQuebec' else entree.get(k)  # auQuebec is optional (default false)
        if ve != v: E(f'index.json: {k} = {json.dumps(ve, ensure_ascii=False)}, but the story says {json.dumps(v, ensure_ascii=False)}')
recentes = sorted(avant, key=lambda x: x.get('date', ''))[-30:]
if recentes:
    prec = recentes[-1]
    if prec.get('miseEnPage') == mep and mep != 'recit': E(f'same layout as the previous story ({mep}): forbidden')
    if prec.get('effetFond') == f.get('effet'): W('same background effect as the previous story')
    if h.get('sousGenre') in [x.get('sousGenre') for x in recentes[-7:]]: W(f'sub-genre "{h.get("sousGenre")}" already used in the last 7 stories')
    if h.get('lieu') in [x.get('lieu') for x in recentes]: W('same place as one of the last 30 stories')
    if (m.get('tonique'), m.get('gamme')) in [(x.get('tonique'), x.get('gamme')) for x in recentes[-5:]]: W('same tonique + gamme as one of the last 5 stories')
    if m.get('gamme') == prec.get('gamme'): W('same gamme as the previous story')
    if pal.get('accent') in [(x.get('palette') or {}).get('accent') for x in recentes[-10:]]: W('same accent color as one of the last 10 stories')
    if mep != 'recit' and mep in [x.get('miseEnPage') for x in recentes[-3:]]: W(f'layout "{mep}" used in the last 3 stories')
    if h.get('retournement') and h.get('retournement') in [x.get('retournement') for x in recentes[-10:]]: E('same twist type as one of the last 10 stories: choose another one')
print(f'Story {date}: No. {h.get("numero")} "{h.get("titre")}" · {mep} · {mots} words · {nb_ev} events · {nb_gestes} gestures · {nb_scenes} scenes · {taille // 1000} KB')
for a in avert: print('  WARNING:', a)
for e in erreurs: print('  ERROR:', e)
if erreurs:
    print(f'\n{len(erreurs)} error(s): fix them, then run the validation again.'); sys.exit(1)
print('\nVALID ✔' + (f' ({len(avert)} warning(s) to consider)' if avert else ''))
VALIDEUR
```

- If it shows **ERRORS**: fix the files (preferably by rerunning the corrected `/tmp/histoire.py`) and run the validation again **until VALID ✔**.
- **WARNINGS** don't block, but fix them if it's easy (especially variety).

## Step 8 — Publish (one commit per story)

```bash
git add "stories/$DATE.json" stories/index.json
git status --short          # must show only these two files
NUM=$(python3 -c "import json;print(json.load(open('stories/$DATE.json'))['numero'])")
TITRE=$(python3 -c "import json;print(json.load(open('stories/$DATE.json'))['titre'])")
git commit -m "3h12 — No. $NUM — $TITRE"
git push origin HEAD:main
```

- Commit message **exactly**: `3h12 — No. [number] — [title]` (em dashes "—").
- If the push is rejected because `main` has moved ahead: `git pull --rebase origin main`, validate again, push again.
- On a network error: retry up to 4 times (wait 2 s, 4 s, 8 s, 16 s).
- Push **to `main`**: it's the branch published by GitHub Pages. A story pushed anywhere else does not appear in the app. Tomorrow's story can be pushed right away: the app keeps it hidden until tomorrow at 3:12 AM.

## Step 9 — Send the preview of tomorrow's story

If tomorrow's story exists (`stories/$DEMAIN.json`), generate the text preview:

```bash
python3 - "$DEMAIN" <<'APERCU'
import json, re, sys, datetime
d = sys.argv[1]
h = json.load(open(f'stories/{d}.json', encoding='utf-8'))
def t(x):
    x = re.sub(r'\|\|(.+?)\|\|', r'▮\1▮', x or '', flags=re.S)
    return re.sub(r'\*|##|%%|\^\^|__|~~', '', x)
jours = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
mois = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
j = datetime.date.fromisoformat(d)
m, f, th = h['musique'], h['fond'], h['theme']
print(f"## No. {h['numero']} — {t(h['titre'])}")
print(f"Released **{jours[j.weekday()]}, {mois[j.month - 1]} {j.day} at 3:12 AM** · {h['sousGenre']} · {h['lieu']}, {h['epoque']}")
print(f"Format: {h['format']} · layout `{h['miseEnPage']}`")
print(f"Mood: background `{f['effet']}` · music {m['tonique']} {m['gamme']}, {len(m['couches'])} layers ({', '.join(c['type'] for c in m['couches'])}), evolution {m['evolution']} · palette {th['palette']['fond']} / {th['palette']['surface']} / {th['palette']['accent']} · fonts {th['polices']['titre']} + {th['polices']['texte']}")
print(f"Summary: {h['resume']}\n\n---\n")
blocs = h['blocs']
if h.get('origine') in ('choisie', 'traduction'):   # the owner already knows the text: the beginning and the end are enough
    print(f"*(Chosen story, published verbatim: {len(blocs)} blocks. Here are the beginning and the end.)*\n")
    scenes = [b.get('decor') for b in blocs if b['type'] == 'scene']
    print(f"*Places: {' → '.join(dict.fromkeys(scenes))}*\n")
    blocs = blocs[:4] + [{'type': 'pause'}] + blocs[-4:]
for b in blocs:
    ty = b['type']
    if ty == 'titre': print(f"\n**{t(b['texte'])}**\n")
    elif ty == 'paragraphe': print(t(b['texte']) + '\n')
    elif ty == 'dialogue': print(f"{(b.get('horodatage') + ' ') if b.get('horodatage') else ''}**{t(b.get('personnage', ''))}**: {t(b['texte'])}\n")
    elif ty == 'sms': print(f"[{'me' if b['de'] == 'moi' else (b.get('nom') or 'them')}{(' ' + b['heure']) if b.get('heure') else ''}] {t(b['texte'])}\n")
    elif ty == 'note': print(f"*(note: {t(b['texte'])})*\n")
    elif ty == 'citation': print(f"> {t(b['texte'])}{(' — ' + t(b['source'])) if b.get('source') else ''}\n")
    elif ty == 'pause': print('* * *\n')
    elif ty == 'evenement': print(f"`⚡ {b.get('effet', 'aucun')} / {b.get('son', 'aucun')}`{(' “' + t(b['texte']) + '”') if b.get('texte') else ''}\n")
    elif ty == 'image': print(f"*[illustration: {t(b.get('legende', ''))}]*\n")
    elif ty == 'signature': print(f"— {t(b['texte'])}\n")
    elif ty == 'champs': print('\n'.join(f"- {t(c.get('nom'))}: {t(c.get('valeur'))}" for c in b['champs']) + '\n')
    elif ty == 'tampon': print(f"[STAMP: {t(b['texte'])}]\n")
    elif ty == 'geste': print(f"👆 **[{t(b['texte'])}]** ({'hold' if b.get('mode') == 'maintenir' else 'tap'}) → view “{(b.get('vue') or {}).get('decor', '?')}”\n")
    elif ty == 'scene': print(f"`🎬 {b.get('decor') or 'base scenery'}{(' · ' + '+'.join(b['ambiance'] if isinstance(b['ambiance'], list) else [b['ambiance']])) if b.get('ambiance') else ''}`\n")
print(f"---\n\n**Final line:** {t(h['phraseFinale'])}")
APERCU
```

Your **final reply** (the message the owner will read) contains, in this order:
1. a status line: which stories were prepared tonight and pushed ("No. 4 released at 3:12 AM · No. 5 ready for tomorrow"), any owner instruction you applied, or the problem you ran into;
2. the **full preview** of tomorrow's story (the output of the script above, as is);
3. one sentence: "Reply here if you want any changes before tomorrow at 3:12 AM."

**Empty night**: if no story from the owner is waiting for tomorrow, there is no preview. Say it plainly in the status line instead, for example: "Tomorrow's night (Thursday, October 22) is empty: no story from you is waiting. Send me one and I'll publish it." (The same goes for tonight if today's story is missing.)

**New story from the owner**: when the owner sends a story, it takes the first night not yet released (step 0). Prepare it right away (steps 1 to 9) if that night is today or tomorrow; otherwise the 3:12 AM routine will prepare it on the night before.

**Revisions**: if the owner replies with changes, modify only `stories/$DEMAIN.json` (and its entry in the index if the title, the palette, etc. change), validate again (step 7), then commit `3h12 — No. N — Title (revision)` and push to `main`. The text itself stays the original's: revisions are about the layout, the staging and the dressing (unless the owner gives you a corrected text). Never revise a story that is already out.

---

## Final checklist

- [ ] Today's story present, or the owner told that tonight is empty; tomorrow's story prepared, or the owner told that tomorrow's night is empty; no story that is already out modified; no other file modified.
- [ ] No story of your own: every story comes from the owner, on the first nights not yet released, in the order given (`"origine": "choisie"`, no credit).
- [ ] The author's text, verbatim: no translation, no rewriting, no corrections, nothing cut, nothing added, paragraphs in the same order; original title; `phraseFinale` = the last sentence of the original, removed from the last block.
- [ ] The original's twist described in `retournement`; no effect, decor or view gives it away too early.
- [ ] No `question` field.
- [ ] No story after October 31, 2026.
- [ ] The owner's instructions (revisions, instructions for the next stories) applied if they were meant for this story.
- [ ] The layout serves the text (the document looks like the document); `document` filled in from what the original gives.
- [ ] 3 to 14 events, well placed; 2 to 5 gestures, **each with its view**; a scene at each change of place (decor + ambiance); `epier` when it serves the story; **every** burst of texts as a `notification` with `"son": "texto"`; block styles used sparingly, no markup added to the author's text.
- [ ] Everything you wrote yourself (gesture labels, `resume`, `format`…) in natural, clear, readable English.
- [ ] Layout different from the previous story (except `recit`, which can come back); varied palette, fonts, background and music.
- [ ] Text/surface contrast ≥ 7; fonts from the verified list.
- [ ] Original SVG illustration and scene decors, with a discreet unsettling detail; carefully drawn gesture views (no "cartoon" faces).
- [ ] Validation: **VALID ✔**.
- [ ] One commit "3h12 — No. N — Title" per story, pushed to `main`.
- [ ] Full preview of tomorrow's story (or "tomorrow's night is empty") in the final reply.

## To check visually (optional)

If a browser is available, you can serve the folder (`python3 -m http.server 8312`) and open `http://localhost:8312/`. In the app: Settings → tap the version number 3 times → code `BURGER` → DEV mode shows the raw JSON and the list of format errors, and lets you paste a JSON to preview it.
