# 3h12

One horror story every night, at 3:12 AM (Toronto time).
Each story brings its own setting: layout, colors, fonts, animated background, illustration and synthesized music.

The app is in English, and so are the stories. Every story is chosen by the owner and published verbatim, exactly as its author wrote it (with the author's permission).

## How it works

- `index.html`: the engine (HTML + CSS + JavaScript, no dependencies). It never changes.
- `stories/YYYY-MM-DD.json`: one story per day. `stories/index.json`: the list of all the stories.
- `SCHEMA.md`: the complete reference of the JSON format (its keys and values are French identifiers read by the engine).
- `DAILY.md`: the instructions followed every night (3:12 AM) by the automation. It never writes a story of its own: it lays out, stages and dresses the stories the owner supplies, and stays **one night ahead**: today's story comes out at 3:12 AM, and tomorrow's is already prepared (hidden in the app until its release), with a preview sent in the Claude conversation. If the owner hasn't supplied a story for tomorrow, it says the night is empty.

## Publishing

The site is served by **GitHub Pages** from the `main` branch (root folder).
`.nojekyll` stops GitHub from transforming the Markdown files.

## In the app

- Tap the entry screen to enter (this also unlocks sound on iPhone).
- Top left: mute. Top right: the menu (volume, settings).
- **A story only lives 24 hours** (3:12 AM → 3:12 AM): no archives, no favorites, no saving.
- The scenery follows the story: at each place, the illustration crossfades, with its own tint, animated background and ambient sound (fridge, shower, car, clock, neon, flies…).
- Gestures ("Look under the door", "Turn around"…) show what the character sees, full screen. Text messages arrive as notifications, with sound, and stack up when someone keeps writing.
- At the end: a suggestion box for the next night (sent by email to the owner, who reads it and decides what to do with it).
- Season: one story per night from October 20 to 31, 2026 (countdown before, end screen after).
- DEV mode: Settings → tap the version number at the very bottom **3 times** → secret code.
