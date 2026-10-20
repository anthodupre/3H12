# SCHEMA.md — the "3h12" file format

Complete reference of the JSON format read by the engine (`index.html`).
The daily automation must follow this document **to the letter**.

- Encoding: **UTF-8**. **Strict** JSON: double quotes, no trailing comma, no comments.
- Keys are in **French camelCase** (`miseEnPage`, `phraseFinale`, `sousGenre`…), exactly as written here. Keys and enumerated values (layouts, effects, sounds, scales…) are identifiers read by the engine: never translate them. The story text itself is in English.
- The engine is tolerant (a missing or invalid field gets a default value and a warning in DEV mode), but **a compliant file is the norm**: don't rely on the tolerance.
- Dates in `YYYY-MM-DD` format, **America/Toronto** time zone.
- Colors in hexadecimal **`#rrggbb`** format only (no `rgb()`, no color names).

Contents: [1. Files](#1-files) · [2. index.json](#2-storiesindexjson) · [3. Story: root fields](#3-story-root-fields) · [4. theme](#4-theme) · [5. document](#5-document) · [6. Layouts](#6-layouts-miseenpage) · [7. fond](#7-fond) · [8. musique](#8-musique) · [9. blocs](#9-blocs) · [10. style](#10-block-style) · [11. Markup](#11-text-markup) · [12. Events](#12-events) · [13. Robustness](#13-robustness-and-dev-mode) · [14. Full example](#14-full-example)

---

## 1. Files

| File | Role |
|---|---|
| `stories/index.json` | List of all the stories (one entry per day). |
| `stories/YYYY-MM-DD.json` | One story. The file name **is** its date (e.g. `stories/2026-10-19.json`). |

**24 hours, no more**: a story dated D appears in the app at **D, 3:12 AM** (Toronto time) and **disappears at D+1, 3:12 AM**, replaced by the next one. There are no archives, no favorites, no saving: a missed story is lost. The next day's story can be pushed in advance: it stays hidden until its release. If tonight's story is missing (an empty night: the owner supplied no story for it), the app does **not** show an older one: it shows a "radio silence" screen (nothing tonight, come back at 3:12). Past and upcoming stories only open in DEV mode (as a preview).

**Season** (`CONFIG.premiereNuit` and `CONFIG.derniereNuit` in `index.html`): from October 20 to 31, 2026. Before the first night, the entry screen shows a live countdown to 3:12 AM; after the last one, it announces that it's over. Test stories (`"essai": true` in the index) only open in DEV mode.

**Who writes the stories**: the owner supplies every story (an existing story whose author has given permission), published verbatim, exactly as the author wrote it, in English. The automation never writes a story of its own: it lays the story out, stages it and dresses it (see DAILY.md).

---

## 2. `stories/index.json`

```json
{
  "moteur": "1.6",
  "version": 1,
  "histoires": [
    {
      "date": "2026-10-18",
      "numero": 1,
      "titre": "Seven Easters",
      "sousGenre": "Québec folklore",
      "lieu": "Rang Saint-Isidore, Saint-Aurèle-des-Monts (Lanaudière)",
      "auQuebec": true,
      "epoque": "1987",
      "format": "Police incident report, with a witness statement",
      "miseEnPage": "rapport",
      "tonique": "D",
      "gamme": "phrygien",
      "effetFond": "neige",
      "palette": { "fond": "#0a0d12", "surface": "#e7e3d6", "texte": "#1f1d1a", "accent": "#a3201c", "secondaire": "#27407a" },
      "polices": { "titre": "Special Elite", "texte": "Courier Prime", "notes": "Reenie Beanie" },
      "evenements": 6,
      "retournement": "the curse spreads through speech: the report itself spreads it",
      "origine": "choisie"
    }
  ]
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `version` | number | yes | Always `1`. |
| `moteur` | string | no | Minimum engine version (`index.html`) the stories require. If the phone has kept an older engine in its cache, the app reloads itself once. Don't change it in the routines. |
| `histoires` | array | yes | One entry per story, **sorted by ascending date**, no duplicate dates. |
| `date` | string `YYYY-MM-DD` | yes | Date of the story = file name. |
| `numero` | integer | yes | Story number (1, 2, 3…). Always the highest existing number + 1. |
| `titre` | string | yes | Same title as in the story file (markup allowed but not recommended). |
| `sousGenre` | string | yes | Same value as in the story (see the list in DAILY.md). |
| `lieu` | string | yes | Same value as in the story. |
| `auQuebec` | boolean | no | `true` if the story is set in Québec. Optional, default `false`. Same value as in the story. |
| `epoque` | string | yes | Year or period ("1987", "winter 1847", "today"). |
| `format` | string | yes | Human description of the document ("A nurse's diary", "A forum thread from 2004"…). |
| `miseEnPage` | string | yes | One of the 10 layouts (section 6). |
| `tonique` | string | yes | Tonic of the music (section 8). |
| `gamme` | string | yes | Scale of the music (section 8). |
| `effetFond` | string | yes | Background effect (section 7). |
| `palette` | object | yes | Exact copy of the story's `theme.palette` (5 colors). Used to vary the palettes from one night to the next. |
| `polices` | object | yes | Copy of `theme.polices` (titre, texte, notes). |
| `evenements` | integer | yes | Number of `evenement` blocks in the story. |
| `retournement` | string | yes | Same value as in the story. |
| `origine` | string | yes (new stories) | `"choisie"`: a story chosen by the owner and published verbatim (same value as in the story). The legacy value `"traduction"` is still accepted. |
| `essai` | boolean | no | `true`: test story, readable only in DEV mode, ignored for numbering and variety. |

The automation also uses these fields to avoid repeating itself: **they must be exact**.

---

## 3. Story: root fields

File `stories/YYYY-MM-DD.json`:

| Field | Type | Required | Default if missing | Description |
|---|---|---|---|---|
| `version` | number | yes | `1` | Always `1`. |
| `numero` | integer ≥ 1 | yes | `0` | Story number (same as in the index). |
| `date` | `YYYY-MM-DD` | yes | file name | Must be equal to the file name. |
| `titre` | string | yes | "Untitled" | The original title, verbatim. |
| `sousTitre` | string | no | — | Standfirst of a newspaper clipping (`coupure` layout). The opening card only shows the number and the title. |
| `sousGenre` | string | yes | — | E.g. "maritime horror". |
| `lieu` | string | yes | — | Precise place (town, region, country). |
| `epoque` | string | yes | — | Year or period. |
| `auQuebec` | boolean | no | `false` | As in the index (optional). |
| `format` | string | yes | — | Human description of the document. |
| `resume` | string | yes | — | A one-sentence summary (without spoiling the ending). |
| `avertissement` | string | no | — | Short content warning (e.g. "Contains the death of an animal."), kept in the file; no longer shown in the app. |
| `retournement` | string | yes | — | The original's big final twist, in a few words (e.g. "the one who reassures is the threat; transmission"). Never shown in the app; the validator refuses the exact same wording as one of the last 10 stories (see DAILY.md). |
| `miseEnPage` | string | yes | `recit` | One of the 10 layouts (section 6). |
| `theme` | object | yes | depends on the layout | Palette, fonts, CSS (section 4). |
| `document` | object | depends on the layout | — | Metadata of the displayed "document" (section 5). |
| `fond` | object | yes | no effect | Animated background and illustration (section 7). |
| `musique` | object | yes | drone + noise | Synthesized music (section 8). |
| `blocs` | array | yes | — | The text, block by block (section 9). |
| `phraseFinale` | string | yes | — | The last sentence, shown alone, large, after the story. For a story from the owner: the very last sentence of the original, removed from the last block. Markup allowed. |
| `origine` | string | yes (new stories) | — | `"choisie"`: an existing story chosen by the owner and published verbatim, exactly as the author wrote it (its original length is kept; 3 to 40 events). Every story is one now. The legacy value `"traduction"` is still accepted. Never displayed. |
| `question` | object | **no longer used** | none | Old multiple-choice "question of the night" (`{ "texte", "choix" }`). The owner doesn't want it anymore: don't add it. The engine can still display it. |

**End of the story**: after the `phraseFinale`, the app shows the end, the date of the next story, a line saying that this story disappears at 3:12 AM, then the **suggestion box** (a free-text field for the next night). The notes are sent by email (FormSubmit) to the owner of 3h12, who reads them and decides what to do with them; they change nothing automatically.

**Reading time**: computed automatically (190 words/min) from all the texts. The length is the original's.

---

## 4. `theme`

```json
"theme": {
  "palette": { "fond": "#0a0d12", "surface": "#e7e3d6", "texte": "#1f1d1a", "accent": "#a3201c", "secondaire": "#27407a" },
  "polices": { "titre": "Special Elite", "texte": "Courier Prime", "notes": "Reenie Beanie" },
  "cssPersonnalise": ".rp-objet { letter-spacing: .02em; }"
}
```

### 4.1 `palette` (5 `#rrggbb` colors, all required)

| Key | Role |
|---|---|
| `fond` | Page color, behind everything (around the paper, the phone…). Also the background of the menus, archives and settings. Almost always very dark. |
| `surface` | Color of the **object**: paper, phone screen, forum window, CRT screen. For `recit`, use the **same value as `fond`** (the text sits directly on the background). |
| `texte` | Color of the text **on the surface** (ink on paper, phosphor on the screen). |
| `accent` | Strong color: stamps, links, `__accent__`, "me" bubbles in SMS, drop cap, entry-screen clock. |
| `secondaire` | Supporting color: handwritten notes (pen), "them" bubbles in SMS, forum banner, timestamps. |

**Contrast rules (mandatory):**
- `texte` on `surface`: contrast ratio **≥ 7** (absolute minimum 4.5).
- `accent` on `surface`: ≥ 3. `secondaire` on `surface`: ≥ 3.
- In SMS: `accent` and `secondaire` are bubble backgrounds; the engine automatically picks white or black text on them.
- The engine automatically fixes an unreadable color, but the result is less pretty: choose carefully.
- The opening card and the end are written on `fond`: if `texte` is unreadable there, the engine uses `surface` (the paper) as the text color.

### 4.2 `polices`

| Key | Role | Default |
|---|---|---|
| `titre` | Titles, opening card, final sentence, end links, menus. | depends on the layout |
| `texte` | Body text. Must stay **very readable** on an iPhone. | depends on the layout |
| `notes` | Handwritten notes, signatures, envelope address. | depends on the layout |
| `secours` | (optional) `"serif"`, `"sans-serif"`, `"monospace"` or `"cursive"`: fallback family if the font doesn't load. Otherwise guessed. | guessed |

Allowed values: the **exact name** of a Google Fonts font (case- and space-sensitive), or an iPhone system font (`system-ui`, `Georgia`, `Verdana`, `Courier New`, `American Typewriter`, `Didot`, `Baskerville`, `Noteworthy`, `Bradley Hand`, `Snell Roundhand`, `Marker Felt`, `Menlo`, `Trebuchet MS`).

**Verified** Google fonts (they all exist):

- **Serif / book**: EB Garamond, Cormorant Garamond, Cormorant, IM Fell English, IM Fell English SC, IM Fell DW Pica, Libre Baskerville, Crimson Pro, Lora, Playfair Display, Old Standard TT, Spectral, Cardo, Vollkorn, Gelasio, Alegreya, Cinzel, Prata, Abril Fatface, Bodoni Moda, Literata, Newsreader, Fraunces, Source Serif 4, Libre Caslon Text, Sorts Mill Goudy
- **Gothic / old**: UnifrakturMaguntia, UnifrakturCook, Pirata One, Grenze Gotisch, Almendra, Uncial Antiqua, MedievalSharp
- **Typewriter / terminal**: Special Elite, Courier Prime, IBM Plex Mono, Space Mono, VT323, Share Tech Mono, Cutive Mono, Major Mono Display, Anonymous Pro, Inconsolata, Source Code Pro, JetBrains Mono, Press Start 2P, Silkscreen
- **Handwritten**: Caveat, Kalam, Homemade Apple, Reenie Beanie, La Belle Aurore, Mrs Saint Delafield, Pinyon Script, Dawning of a New Day, Cedarville Cursive, Nothing You Could Do, Shadows Into Light, Gochi Hand, Patrick Hand, Covered By Your Grace, Just Another Hand, Zeyada, Sue Ellen Francisco, Waiting for the Sunrise, Rock Salt, Nanum Pen Script, Mynerve
- **Sans-serif**: Inter, Work Sans, IBM Plex Sans, Archivo Narrow, Oswald, Bebas Neue, Anton, Barlow Condensed, Josefin Sans, Karla, DM Sans, Manrope, Space Grotesk, Syne
- **Avoid** (too "Halloween"): Creepster, Nosifer, Eater, Butcherman.

Tips: **never** a handwritten or gothic font for `texte` over more than a few lines (except `journal_intime` with Kalam, Caveat or Patrick Hand, which stay readable). Prefer fonts that have an italic.

### 4.3 `cssPersonnalise` (optional)

A CSS string injected into the page. **Every selector is automatically prefixed with `.histoire`**: the interface cannot be broken. `html`, `body` and `:root` become `.histoire`. `@media`, `@supports` and `@keyframes` are allowed; `@import` is removed.

Use sparingly (small adjustments), for example:
```css
.titre-bloc { letter-spacing: .3em; }
.bloc-paragraphe:nth-of-type(7) p { color: #8a0f0f; }
@keyframes pulse { from { opacity: .4 } to { opacity: 1 } }
.carton-titre { animation: pulse 3s infinite alternate; }
```

Useful classes: `.carton`, `.carton-titre`, `.objet` (the sheet / the phone), `.bloc`, `.bloc-paragraphe`, `.bloc-dialogue`, `.titre-bloc`, `.note`, `.tampon`, `.bulle`, `.fin-phrase`, and the layout prefix `.mep-rapport`, `.mep-sms`, etc.

---

## 5. `document`

Metadata of the "physical" document (report header, name of the SMS contact, newspaper of the clipping…). All keys are optional strings, except `champs` (array).

| Key | Used by | Role |
|---|---|---|
| `organisme` | rapport, transcription, forum, terminal, coupure, lettre | Police department, site name, newspaper name, system name… |
| `typeDocument` | rapport, transcription, terminal | "Incident report", "Recording transcript"… |
| `reference` | rapport, transcription, forum, terminal, sms | File number, audio file name, session number; in SMS: phone number shown under the name. |
| `classification` | rapport | Stamp at the top of the report ("Confidential"). |
| `dateDocument` | rapport, transcription, terminal, journal_intime, lettre, coupure | Date shown in the header; postmark (lettre). |
| `auteurDocument` | rapport, transcription, terminal, coupure, journal_intime | Written by / transcribed by / byline of the article / owner of the diary (the diary's label shows this name: use a first name). |
| `destinataire` | rapport, lettre | Recipient; in `lettre`, the **address on the envelope** (lines separated by `\n`). No recipient, no envelope. |
| `contact` | sms | Name shown at the top of the conversation. Default: the `nom` of the "them" messages. |
| `sujet` | rapport, transcription, forum, coupure | Subject of the report, title of the forum thread, newspaper headline. Default: `titre`. |
| `section` | forum, coupure | Forum section ("Testimonies"), newspaper section ("Local news"). |
| `slogan` | forum | Site tagline. |
| `duree` | transcription | Length of the recording ("00:41:07"). |
| `champs` | rapport, transcription | Extra header lines: `[{ "nom": "Nature", "valeur": "Disappearance" }]`. |

---

## 6. Layouts (`miseEnPage`)

10 allowed values. Each layout has its own visual object, default colors and default fonts, and gives a particular meaning to some blocks.

| Value | Visual object | Particular meaning of the blocks | Default fonts |
|---|---|---|---|
| `manuscrit` | Yellowed paper page, stains, ink; handwritten underlined title. | `note` = red-ink annotation in the margin; wavy `~~strikethrough~~`. | IM Fell English SC / IM Fell English / Homemade Apple |
| `journal_intime` | Ruled notebook, red margin, binding holes, a name label. | `titre` = **date at the top of a section** ("Tuesday, March 3, 2 AM"); `note` = in pencil. | Caveat / Kalam / Caveat |
| `rapport` | Official form: seal, organization, table of fields, stamp, numbered sections. | `titre` = numbered section ("1. SUMMARY"); `dialogue` = interrogation "NAME: line"; `note` = a superior's pen annotation; `\|\|redacted\|\|` = black bar; `tampon`, `champs`, `signature`. | Special Elite / Courier Prime / Reenie Beanie |
| `sms` | Phone, contact header (stays at the top), bubbles, times, "Read". Messages arrive **one by one** with a typing "…". | `sms` = bubbles; `dialogue` = "them" bubble; `paragraphe`/`note`/`titre` = gray centered system text; `pause` = a ghost "…" that types then disappears. | system-ui |
| `transcription` | Transcript document: waveform, "● REC", file, duration, list of speakers (automatic). | `dialogue` = line `[00:03:12] NAME` then the line spoken (use `horodatage`); `paragraphe` = transcriber's comment in italics; `[inaudible]` styled; `note` = transcriber's note. | IBM Plex Mono ×3 |
| `forum` | Old 2000s forum: banner, breadcrumb, locked topic, posts with pixel avatar, rank, post count. | `dialogue` = **a post** (fields `heure`, `rang`, `messages`, `signature`, `avatar`); `paragraphe` = boxed moderation notice; `citation` = "X wrote:"; `pause` = page change; `note` = "edited by…". | Verdana (system) |
| `lettre` | Old correspondence: cream paper folded in three, envelope with stamp and postmark if `document.destinataire`. | **each `pause` starts a new sheet** (a new letter); `titre` = place and date at the top right; `signature` = calligraphic signature; `note` = "P.S.". | Pinyon Script / Cormorant Garamond / Pinyon Script |
| `recit` | Classic book: justified text, indents, automatic **drop cap**, chapters in small caps. | `titre` = chapter; `pause` = ❦; `dialogue` = "Name — line". | Cormorant Garamond / EB Garamond |
| `terminal` | Green CRT screen: scan lines, glow, text that **types itself**, session header. | `paragraphe` = `> …`; `titre` = command `$ …`; `dialogue` = `<Name> …`; `note` = comment `# …`. | VT323 ×3 |
| `coupure` | Newspaper clipping: torn edges, tape, newspaper name in blackletter, headline, standfirst, byline, drop cap. | **each `pause` starts a new clipping**; `titre` = subheading; images in halftone print. | Playfair Display / Old Standard TT |

Default palettes (used if `theme.palette` is missing; for inspiration):

| Layout | fond | surface | texte | accent | secondaire |
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
  "svg": "<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 400 860'>…</svg>",
  "decors": {
    "cuisine": "<svg …>…</svg>",
    "cuisine-elle": "<svg …>…</svg>",
    "vue-sous-lit": "<svg …>…</svg>"
  }
}
```

| Key | Type | Default | Description |
|---|---|---|---|
| `effet` | string | `aucun` | Full-screen animated effect (list below). |
| `intensite` | 0 to 1 | 0.5 | Amount / strength of the effect. 0.3 to 0.6 = atmosphere; above 0.8 = overwhelming. |
| `vignette` | 0 to 1 | 0.6 | Darkening of the screen edges. |
| `grain` | 0 to 1 | 0.25 | Animated film grain. |
| `scintillement` | 0 to 1 | 0 | Random dips in the light (a tired bulb). 0.1 to 0.3 = subtle. Softened if the user turns off intense effects. |
| `svg` | string | — | **Original illustration** shown behind the text, semi-transparent (see below). |
| `svgOpacite` | 0 to 1 | 0.22 | Opacity of the illustration while reading (it is ~2× more visible on the opening card). 0.15 to 0.3 without scenes; **0.35 to 0.5 with scenes** (the text then gets a shadow that keeps it readable, and the scenery lights up for a few seconds at each change of place). |
| `couleur` | `#rrggbb` | depends on the effect | (optional) Color of the particles. |
| `decors` | object | `{}` | **Named decors**: `{ "name": "<svg…>" }`. Used by the `scene` blocks (the place changes) and by the `vue` of the gestures (what the character sees). Same rules as `svg`. One decor = one place; a variant (`cuisine-elle`, the kitchen with her) = the same place, drawn the same way, with the creature added. The reserved name **`epieur`** (`viewBox='0 0 160 260'`) replaces the face that peeks out from the screen edges (`epier`) and that leaps. |
| `teinte`, `teinteOpacite` | `#rrggbb`, 0 to 0.8 | none, 0.25 | (optional) Color laid over the scenery, under the text, at the start of the story. |
| `ambiance`, `volumeAmbiance` | string or array, 0 to 1 | none, 0.6 | (optional) Ambient sound at the start (see `scene`). |

**Effects**: `pluie` (rain), `neige` (snow), `brouillard` (fog), `poussiere` (dust motes in a beam of light), `braises` (embers: orange, rising), `statique` (TV static), `lucioles` (fireflies), `cendres` (ashes: fall swirling, a few glow), `etoiles` (stars: twinkle, a shooting star), `bulles` (bubbles: under water), `aucun` (none).

**Rules for `svg`** (background illustration):
- A single complete `<svg>` element with `xmlns='http://www.w3.org/2000/svg'`.
- `viewBox='0 0 400 860'` (iPhone portrait format). Add `preserveAspectRatio='xMidYMax slice'` to anchor the landscape at the bottom.
- **Use single quotes `'` for attributes** (the SVG sits in a JSON string between double quotes).
- Forbidden: `<script>`, `<foreignObject>`, `on…` attributes, external links (`href` to http), raster images, `<text>` (text has nothing to do in the scenery). They are removed automatically anyway.
- The `id`s (gradients, filters) are renamed automatically: no risk of conflict.
- Style: silhouettes, dark flat shapes, gradients, **one or two light sources** (window, moon, lighthouse), a discreet unsettling detail (silhouette, eyes, door ajar). No photorealism. Size: **5 to 15 KB**, 20 KB maximum.
- The sky/background of the SVG must match `palette.fond`.

---

## 8. `musique`

Everything is synthesized live (Web Audio). No audio files.

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

| Key | Type | Default | Description |
|---|---|---|---|
| `tempo` | 30 to 180 | 60 | Beats per minute (40–70 = slow and heavy; 90+ = anxiety). |
| `tonique` | string | `D` | Root note: `C`, `C#`, `Db`, `D`, … `B` (or the solfège names Do, Ré, Mi, Fa, Sol, La, Si, with `#`/`b`). |
| `gamme` | string | `mineur` | See the list below. |
| `couches` | array | drone + noise | **2 to 6 layers** (8 maximum). |
| `reverb` | 0 to 1 | 0.6 | Size of the reverb (0.8+ = cathedral, cave). |
| `desaccord` | 0 to 1 | 0.15 | Detuning and tape "wow": 0.1 = clean; 0.3 = unhealthy; 0.6 = broken. |
| `evolution` | string | `monte` | How the intensity follows the reading: `monte` (crescendo), `stable`, `descend` (decreasing), `rupture` (rises then collapses sharply at `rupture`). |
| `rupture` | 0.1 to 0.95 | 0.75 | Position (0 = start, 1 = end) of the break if `evolution` = `rupture`. |
| `finale` | string | `fondu` | At the final sentence: `fondu` (drops to 35%), `silence` (goes quiet), `continuer` (keeps going). |
| `volume` | 0 to 1 | 1 | Overall music volume for this story. |

**Scales** (`gamme`): `mineur` (minor), `phrygien` (Phrygian: dark, Spanish/ancient), `locrien` (Locrian: the most unstable), `chromatique` (chromatic: chaos), `pentatonique_mineure` (minor pentatonic: folk, simple), `ton_par_ton` (whole-tone: dream, unreal), `harmonique_mineure` (harmonic minor: gothic, Eastern), `dorien` (Dorian: melancholic), `diminuee` (diminished: nightmare, tension), `hongroise_mineure` (Hungarian minor: strange, ancient), `phrygien_dominant` (Phrygian dominant: ritual, desert), `lydien` (Lydian: warped wonder), `majeur` (major: innocence… that becomes disturbing with detuning, e.g. a music box).

**Layers** — each layer:

| Key | Type | Default | Description |
|---|---|---|---|
| `type` | string | — | See the next table. |
| `onde` | `sine`, `triangle`, `sawtooth`, `square` | depends on the type | Timbre: sine = pure; triangle = soft; sawtooth = rich, rasping; square = hollow. |
| `octave` | 0 to 7 | depends on the type | 1–2 = low; 3–4 = mid; 5–6 = high. |
| `volume` | 0 to 1 | 0.5 | Volume of the layer (balanced automatically according to the type). |
| `filtre` | 60 to 18000 (Hz) | depends on the type | Cutoff frequency: low = muffled, distant; high = bright. Opens up with the intensity. |
| `densite` | 0 to 1 | 0.5 | How many notes play (or how much activity). |
| `seuil` | 0 to 1 | 0 | The layer only comes in when the intensity reaches this threshold (e.g. 0.7: arrives near the end). Ideal for building tension. |
| `pan` | -1 to 1 | 0 | Left / right. |

| `type` | Role | Typical settings |
|---|---|---|
| `drone` | Low held notes that rarely glide (the base of almost everything). Density = number of voices (root, fifth, octave, dissonant second). | sawtooth, octave 1–2, filtre 250–600 |
| `pad` | Slow chords that change every two bars. | triangle, octave 3, filtre 900–2000 |
| `cloche` | Sparse metallic chimes (bell): church bell, distant carillon. | octave 4–5, densite 0.1–0.3 |
| `pulsation` | Low heartbeat pulse ("ba-dum"). Use `seuil` so that it arrives later. | sine, octave 1–2, seuil 0.5–0.8 |
| `bruit` | Continuous noise with gusts: wind, sea, rumble, ventilation. | filtre 400–1500, densite = strength of the gusts |
| `boite_a_musique` | Music box: a small repeated, warped melody that slowly mutates. | octave 5–6, densite 0.4–0.7; with `majeur` or `lydien` + desaccord = very unsettling |
| `cordes` | String section, long notes; automatic **tremolo** when the intensity goes above 0.55. | sawtooth, octave 3, filtre 800–1600 |
| `choeur` | Choir: wordless voices ("aaah", "oooh"). Cult, church, cosmic. | octave 3, volume 0.15–0.3 |
| `piano` | Isolated notes from an old out-of-tune piano. | octave 3–5, densite 0.2–0.5 |

Recipes: **winter** = drone + bruit (wind) + rare cloche; **cosmic** = drone + choeur + pad in `ton_par_ton`, reverb 0.9; **haunted house** = boite_a_musique (`majeur`, desaccord 0.4) + drone + piano; **technological** = square drone + bruit with a high filtre + pulsation; **maritime** = bruit (filtre 500, densite 0.9) + cordes + cloche (buoy).

---

## 9. `blocs`

Ordered array. Each block has a `type` and, optionally, a `style` (section 10). Every text field accepts **markup** (section 11) and `\n` (line break); in `paragraphe`, `citation` and forum/transcript lines, `\n\n` starts a new paragraph.

| `type` | Fields | Rendering |
|---|---|---|
| `paragraphe` | `texte` | Narration paragraph(s). |
| `titre` | `texte` | Section title (meaning depends on the layout). |
| `dialogue` | `personnage`, `texte`; optional: `horodatage` (transcript: "00:03:12"), `heure` (forum: posting date; sms: time), `rang`, `messages`, `signature`, `avatar` (forum) | A line of dialogue. |
| `sms` | `de` (`"moi"` = me or `"eux"` = them), `texte`; optional: `nom` (sender of a "them" message, shown when there are several correspondents), `heure` (shown when it changes), `vu` (`true` or "Read at 03:14"), `statut` (`"non_distribue"` = not delivered) | Message bubble. Works in every layout, but it's the heart of `sms`. |
| `note` | `texte` | Scribbled note (`notes` font, secondary color). |
| `citation` | `texte`, `source` (optional) | Indented quotation. |
| `pause` | `symbole` (optional) | Separator (⁂, ❦, — — —…). With `"symbole": "page"`: starts a new sheet, in any layout. In `lettre` and `coupure`, **every** pause starts a new sheet. |
| `evenement` | `effet`, `son`, `duree`, `nombre`, `texte` | Invisible; fires only once while scrolling (section 12). |
| `image` | `svg`, `legende` (optional) | SVG illustration in the text (sketch, map, photo of a piece of evidence…). Landscape `viewBox` recommended (e.g. `0 0 320 200`). Use `currentColor` or the palette colors. Same safety rules as the background SVG. |
| `signature` | `texte` | Handwritten signature aligned to the right. |
| `champs` | `titre` (optional), `champs`: `[{ "nom": "…", "valeur": "…" }]` | Table of official fields (form, record card). |
| `tampon` | `texte`, `rotation` (-30 to 30, optional) | Ink stamp ("RECEIVED", "FILED", "DO NOT RELEASE"). |
| `geste` | `texte` (the button), `mode` (`"toucher"` = tap or `"maintenir"` = hold), `duree` (hold: 400 to 6000 ms, default 1600), `tension` (optional, hold only: `true` = a swell of dread rises while the finger is down, cut dead when the view opens), `son` (optional, played when done), `aide` (optional, small text under the button), **`vue`** (mandatory, see §9b) | **The reader must act**: a button in the style of the story. Everything that follows stays hidden until they tap it (or keep their finger on it: a gauge fills up, letting go too early starts over). Then the **view** opens full screen: you see what the character sees. Only then does the rest appear. In SMS, the conversation waits for the gesture. |
| `scene` | `decor` (name in `fond.decors`) or `svg`; optional: `opacite`, `effet`, `intensite`, `couleur`, `scintillement`, `vignette`, `teinte`, `teinteOpacite`, `ambiance`, `volume`, `fondu`, `transition` | Invisible. **The place changes** when the reader gets there (§9a). |

Examples:
```json
{ "type": "titre", "texte": "Tuesday, March 3 — 2:40 AM" },
{ "type": "paragraphe", "texte": "First paragraph.\n\nSecond paragraph of the same block." },
{ "type": "dialogue", "personnage": "MARIE", "texte": "Did you hear that?", "horodatage": "00:03:12" },
{ "type": "sms", "de": "eux", "nom": "Mom", "heure": "03:12", "texte": "Are you awake?" },
{ "type": "sms", "de": "moi", "texte": "Yes. Why?", "vu": "Read at 03:13" },
{ "type": "dialogue", "personnage": "nightcrawler_77", "heure": "March 14, 2004, 03:12", "rang": "Member", "messages": "212", "signature": "Don't answer when it knocks.", "texte": "Has anyone else seen her?" },
{ "type": "champs", "titre": "Missing person", "champs": [{ "nom": "Name", "valeur": "LAFOND, Gérard" }, { "nom": "Age", "valeur": "52" }] },
{ "type": "tampon", "texte": "Filed", "rotation": -8 },
{ "type": "pause" },
{ "type": "image", "legende": "Appendix A — basement floor plan.", "svg": "<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 320 200'>…</svg>" },
{ "type": "scene", "decor": "cuisine", "ambiance": "frigo", "teinte": "#14263a", "teinteOpacite": 0.22 },
{ "type": "geste", "texte": "Open the closet", "mode": "maintenir", "duree": 2000, "son": "craquement",
  "vue": { "decor": "vue-garde-robe", "entree": "ouvrir", "son": "mouches", "duree": 4500 } }
```

### 9a. `scene`: the environment follows the story

A `scene` block goes **just before** the paragraph that arrives in a new place (or that changes the place: the creature appears, the light goes out, the shower stops). When that paragraph reaches 60% of the screen, the environment changes: the **illustration** crossfades to the new decor, the **animated background**, the **tint** and the **vignette** follow, and the **ambient sound** (looping) crossfades to the new one. If the reader scrolls back up, the previous place comes back. Before the first scene: the story's `fond`.

A missing field takes the value of the story's `fond` (**not** the previous scene's): each scene describes its place in full.

| Key | Type | Default | Description |
|---|---|---|---|
| `decor` | string | `fond.svg` | Name of a decor in `fond.decors`. (Or `svg`: the SVG directly.) |
| `opacite` | 0 to 1 | `fond.svgOpacite` | Opacity of the decor behind the text (a bit more, 0.45 to 0.56, when the creature is in the decor). |
| *(creature scenes)* | — | — | A scene **where you can see the creature** is never skipped, even when the reader scrolls fast: it plays before the next one and stays on screen at least its fade-in + 3.6 s (other scenes: fade-in + 0.7 s; `tenir` in ms can ask for more). The engine recognizes it by its decor name (`…-elle`, `…-part`, `…-silhouette`, `…-bave`, `…-epie`, `…-creature`) or an `opacite` of 0.5 or more: name the variants that way. |
| `effet`, `intensite`, `couleur`, `scintillement`, `vignette` | | those of `fond` | Animated background of the place (`brouillard` for the steam of a shower, `statique`, `poussiere`…). |
| `teinte`, `teinteOpacite` | `#rrggbb`, 0 to 0.8 | none, 0.25 | Color of the place (cold blue of a fridge, red of a neon sign, green of a rotting house). 0.12 to 0.25. |
| `ambiance` | string or array (3 at most) | none | Looping sounds: `frigo` (fridge hum), `douche` (running shower water), `gouttes` (an occasional drip, every 5 to 11 s, quiet and dry; never a loop), `voiture` (engine and road), `horloge` (clock ticking), `vent` (wind), `climatiseur` (air conditioner), `neon` (neon buzz), `mouches` (flies), `souffle` (someone breathing very close, in the dark), `soir` (a suburban evening: a low far-off traffic hum, a car passing far away now and then; nothing high-pitched), `tension` (a low dread drone that beats, a faint high note now and then: for the build-up before a scare), `pouls` (his heartbeat, slowly speeding up), `aucune` (none). |
| `volume` | 0 to 1 | 0.6 | Volume of the ambient sound. |
| `fondu` | 0 to 6000 ms | 1800 | Duration of the crossfade to the new decor. **Slow** (3000 or more) so that she appears gently; **`0`** for a hard cut ("she was gone at once"). |
| `transition` | string | `noir` | How the decor changes: `noir` (the old scene goes dark, then the new one lights up: every change of place), `coupe` (hard cut: the creature appears or leaves **by moving**, see below), `fondu` (crossfade, rarely: only for light or time passing on the same view). Never dissolve a person in or out: it looks cheap. |

**Animated decors.** A person never fades in or out of a scene: cut (`"transition": "coupe"`) to a decor where she **moves in** (leans out from behind a corner, slides out from behind a counter, steps into a doorway, a curtain parts) and, when she leaves, cut to a decor where she **moves out** (snaps back behind the corner, scuttles away, drops out of sight). A discovery can be a camera move (a slow push-in on her face). A decor can act out the text with SVG animations (SMIL: `<animate>`, `<animateTransform>`, `<set>`): she slowly pulls her head back out of the shower, a face slides out from behind a counter, a lid closes, drool runs down the glass, hands tremble, a bag swings. The clock starts **when the decor appears** (the engine resets it), so `begin` times are counted from the scene: put the action a few seconds in, where the reader reaches the sentence. Keep it slow and small, `fill='freeze'` for one-time movements, `repeatCount='indefinite'` for trembling or dripping.

### 9b. `vue`: what the gesture shows

A gesture that only lets the reading continue is useless: **every gesture has a `vue`**, a full-screen image of what the character sees (under the door, in the closet, under the bed, the photo they received). It closes by itself after `duree`, or with a tap.

| Key | Type | Default | Description |
|---|---|---|---|
| `decor` | string | — | Name of a decor in `fond.decors` (or `svg` directly). Portrait `0 0 400 860`, except for `photo` (landscape, e.g. `0 0 320 220`). |
| `entree` | string | `fondu` | How it is discovered: `fondu` (fade), `zoom` (the eyes adjust to the dark), `tourner` (you turn your head), `baisser` (you lean down, you look down), `ouvrir` (two doors open), `photo` (like a photo received on a phone, with `titre` at the top). |
| `son` | string | `aucun` | Sound played when the view appears (`respiration`, `coeur`, `mouches`, `silence`…). |
| `duree` | 1200 to 30000 ms | 3400 | Time before it closes by itself (long only for a whole "video", like a montage of clips). |
| `titre` | string | — | With `photo`: the sender shown at the top ("Unknown number"). |
| `texte` | string | — | (optional) A short line at the bottom of the view. |

Views can be more detailed than decors: mostly black, a light on what matters, grain (`feTurbulence`), a little blur (`feGaussianBlur`), and even a SMIL animation (`<animate>`: an eye that blinks, hands that shake, flies that move).

---

## 10. Block `style`

Optional, on any block: a string of space-separated words (or an array).

| Style | Effect |
|---|---|
| `centre` / `droite` | Alignment (center / right). |
| `gros` / `petit` | Size (×1.45 / ×0.82). |
| `agite` | Every word trembles nervously (panic, writing that slips). |
| `efface` | Very pale, as if erased. |
| `accent` | The whole block in the accent color. |
| `majuscules` | In spaced capitals. |
| `espace` | Large space before the block (to isolate a sentence). |
| `lent` | Very slow appearance (2.8 s). |

Example: `{ "type": "paragraphe", "texte": "It's still here.", "style": "centre gros lent" }`

---

## 11. Text markup

| Markup | Effect |
|---|---|
| `*text*` | *Italics*. |
| `##text##` | Whispered: small, pale, each word trembles slightly. |
| `\|\|text\|\|` | Redacted: black bar, revealed when tapped. The reader may never see it: don't hide **essential** information in it. |
| `%%text%%` | Glitch: the characters scramble from time to time. |
| `^^text^^` | Appears letter by letter, slowly (when the block comes on screen). |
| `__text__` | Accent color. |
| `~~text~~` | Strikethrough (wavy in `manuscrit` and `journal_intime`). |
| `[text]` | Annotation in brackets, in pale italics (e.g. `[inaudible]`, `[silence]`, `[laughter]`). |
| `\n` | Line break. `\n\n` = new paragraph (in `paragraphe`). |

- Tags don't overlap; `%%…%%` and `^^…^^` contain plain text (no other tag inside).
- Every tag must be **closed** (an even number of `##`, `||`, `%%`, `^^`, `__`, `~~`).
- There is no way to escape these sequences: in a story from the owner, they always produce their effect (see DAILY.md, step 4).
- Sparingly: a few well-placed effects are better than ten.

---

## 12. Events

`evenement` block: invisible, it fires **only once** when it passes the middle of the screen while scrolling. Place it **right after** the sentence that justifies it.

```json
{ "type": "evenement", "effet": "noir", "son": "respiration", "duree": 3000, "texte": "##Don't turn around.##" }
```

| Key | Type | Default | Description |
|---|---|---|---|
| `effet` | string | `aucun` | Visual effect: `glitch` (the screen scrambles, shifted colors), `noir` (fully black screen), `flash` (white flash), `tremblement` (the **scenery** takes a jolt, like an impact; the text never moves), `epier` (a pale face peeks out from an edge of the screen, stares at the reader without blinking, then hides; tapping it makes it flee), `notification` (texts arrive at the top of the screen, like on an iPhone, with sound; they stack up, the most recent on top; beyond 4, an "and N more notifications" counter climbs; a tap dismisses them), `appel` (full-screen incoming call with a ringtone: Decline or Accept; if you accept, you hear breathing, then "Call ended"), `aucun` (none). |
| `son` | string | `aucun` | Synthesized sound (list below). |
| `duree` | milliseconds | noir 1600, flash 700, glitch 800, tremblement 900, epier 2600, notification 6000 | Duration of the effect **and** of the sound (for sounds that have a duration). |
| `nombre` | 1 to 12 | depends on the sound | Repetitions for `coup` (3), `pas` (5), `coeur` (5), `vibration` (2), `goutte` (3). |
| `texte` | string | — | With `noir`: text shown on the black screen. With `notification`: the message (if there are no `messages`). With `appel`: a line shown during the call, if accepted ("[breathing]"). Markup allowed. |
| `titre` | string | — | With `notification`: the sender ("Lynn", "Unknown number"). With `appel`: who is calling (mandatory). |
| `messages` | array | — | With `notification`: **a burst** of 1 to 40 texts, arriving one by one. Each one: a string, or `{ "texte": "…", "titre": "…", "app": "telephone" }` (a missed call among the texts). When someone writes several times, put **all** of them. |
| `intervalle` | 150 to 5000 ms | 900 | With `notification`: time between two texts of the burst (400 to 600 = relentless). |
| `reste` | 0 to 999 | 0 | With `notification`: after the burst, the "and N more" counter climbs by that much more, very fast ("dozens of texts"). |
| `app` | string | `messages` | With `notification`: `messages` (green bubble) or `telephone` (missed call). |
| `attaque` | boolean | `false` | With `epier`: the face slowly comes closer during `duree`; if the reader doesn't touch it, it leaps at the screen (a thud, a vibration). |
| `visage` | string | `epieur` | With `epier`: the name of the face decor in `fond.decors` (e.g. `epieur-beant` for the gaping-mouth version when the text says her mouth hangs open). An `epier` that the reader has already scrolled well past is dropped, never shown late. |
| `cote` | string | `droite` | With `epier`: `droite` (right), `gauche` (left), `bas` (bottom) or `haut` (top, upside down, as if hanging from the ceiling). |

**Sounds**:

| `son` | Description |
|---|---|
| `chute` | Someone falling down a long metal shaft (a garbage chute): bangs and scraping, faster and farther, then a heavy dull impact far below (about 2.5 s). |
| `crac` | A small dry crack, muffled (something breaking inside a box). |
| `garage` | An old garage door: tugged and whacked (it rattles), it gives, springs creak, rollers clack over the track joints, a hinge squeals twice, it bangs at the top (about 4.3 s). |
| `coups_garage` | Three kicks into a sheet-metal garage door (boom, wobble, rattle), then a heavy box slipping and landing flat on concrete (about 1.6 s). |
| `gemissement` | A woman's raspy moan, close, from the throat (echoes in a small room). |
| `gemissement_loin` | The same moan, muffled through a door or a wall: you're not sure you heard it. |
| `gemissement_bref` | A tiny moan, mouth open. |
| `hoquet` | Excited little hiccups in her throat (`nombre`: how many). |
| `rire_aigu` | A shrill, breathy giggle, behind him, a little far. |
| `choc` | The moment you see her: a deep hit and a dissonant string shriek (`nombre` 1 to 3: how hard). Silences the music for a few seconds. |
| `choc_gemissement` | `choc`, then her moan. |
| `choc_hoquet` | `choc`, then her hiccups. |
| `montee` | A rising swell of tension (`duree`), cut dead at the end. |
| `coup_couteau` | A stab (a glass shard): the swish, the skin giving way, his breath catching. |
| `video_colis` | The soundtrack of the neighbor's footage in No. 2 (about 26 s, timed to the `vue-video` drawing): wind and a reversing truck, his muffled voice and laughs, flaps and tape, a truck, a warehouse, then three kicks, the drop, the crack, silence… and the garage door. Silences the music while it plays. |
| `coup` | Knocks on wood (door, wall). |
| `craquement` | A floor or door that creaks. |
| `chuchotement` | An unintelligible whispered voice moving from left to right. |
| `respiration` | Close breathing (in, out). ~2.7 s per cycle. |
| `porte` | A creak, then a door slamming. |
| `silence` | **Cuts the music sharply** for `duree`, then it comes back gently. Very effective. |
| `pas` | Footsteps coming closer. |
| `grattement` | Something scratching (claws on a wall). |
| `coeur` | Heartbeats speeding up. |
| `statique` | Radio crackle with a ghostly tone. |
| `cloche` | A single deep bell toll (tuned to the music's tonic). |
| `vibration` | A phone vibrating. |
| `goutte` | Water drops falling, with echo. |
| `texto` | The sound of an incoming text (three short notes). **Use it on notifications.** |
| `mouches` | Flies buzzing and circling around (for `duree`). |
| `aucun` | No sound. |

Rules: **3 to 40 events per story** (every story is chosen by the owner; see DAILY.md), at key moments, **2 to 5 gestures** (each with its view) and a **scene** at each change of place. No gratuitous jump scares: an event must extend what the text has just said. `flash` and `tremblement` become very soft if the user has turned off intense effects.

---

## 13. Robustness and DEV mode

- Missing or invalid field → default value + warning. Unknown block type with a `texte` → shown as a paragraph. Unreadable block → ignored.
- Slightly damaged JSON (trailing comma, comment) → repaired if possible, reported as an error.
- Tonight's file not found → "radio silence" fallback screen (never an older story). **Never a blank screen.**
- Font not found → fallback font (serif, sans-serif, monospace or cursive).
- DEV mode (Settings → tap the version number at the bottom **3 times** → code `BURGER`): read the next story (and the past ones) as a preview, raw JSON, list of format errors and warnings, preview of a pasted JSON, test of every layout, background, sound and music layer, suggestion box settings.

---

## 14. Full example

Short but complete and valid example (a real story is the owner's whole text, verbatim, with 3 to 40 events, 2 to 5 gestures and its scenes):

```json
{
  "version": 1,
  "numero": 2,
  "date": "2026-10-19",
  "titre": "The Night Watch",
  "sousTitre": "Buoy 44, Gulf of St. Lawrence",
  "sousGenre": "maritime horror",
  "lieu": "Île aux Corneilles lighthouse, Gulf of St. Lawrence",
  "epoque": "November 1958",
  "auQuebec": true,
  "format": "A lighthouse keeper's logbook",
  "resume": "Every night, a lighthouse keeper writes down the light of a boat that never comes any closer.",
  "retournement": "inversion: it isn't the boat that is coming closer, it's the coast that is drifting away",
  "origine": "choisie",
  "avertissement": "",
  "miseEnPage": "journal_intime",
  "theme": {
    "palette": { "fond": "#05080c", "surface": "#ece6d4", "texte": "#1b2433", "accent": "#9c2a1e", "secondaire": "#4a5d78" },
    "polices": { "titre": "Caveat", "texte": "Patrick Hand", "notes": "Caveat" },
    "cssPersonnalise": ""
  },
  "document": { "auteurDocument": "Wilfrid", "dateDocument": "November 1958" },
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
    { "type": "titre", "texte": "Monday, November 10 — midnight" },
    { "type": "paragraphe", "texte": "Thick fog. The foghorn every thirty seconds. A light out at sea, due east. It doesn't move." },
    { "type": "evenement", "effet": "aucun", "son": "cloche" },
    { "type": "titre", "texte": "Tuesday, November 11 — 3:12 AM" },
    { "type": "paragraphe", "texte": "The light is closer. ##It blinks in time with me.##" },
    { "type": "note", "texte": "Don't forget: wind up the clock weights." },
    { "type": "evenement", "effet": "noir", "son": "silence", "duree": 2500, "texte": "The foghorn has gone quiet." },
    { "type": "paragraphe", "texte": "I didn't light the lantern tonight. ^^And yet the lighthouse is shining.^^", "style": "espace" }
  ],
  "phraseFinale": "The boat never came any closer. __It's the coast that is drifting away.__"
}
```
