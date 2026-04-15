# Game Database

A browser-based game database and build planner. Browse monsters, weapons, armour, skills, and plan your character build — all client-side, no server required beyond a simple static file host.

![Game Database](https://img.shields.io/badge/version-4.5.1-green) ![License](https://img.shields.io/badge/license-MIT-blue)

---

## Features

- **Bestiary** — searchable monster list with stats, elements, drops, and HIT/FLEE breakdowns
- **Armory** — browse all weapons, armour, and soul gems by slot; see craft recipes, drop sources (mob + rate), and shop prices (NPC + cost) in one place; filter by source type (Crafted / Shop / Drop)
- **Skill Tree** — interactive visual skill tree with tooltips and archetype filtering
- **Element Table** — full 12×12 element damage reference
- **Build Planner** — full character planner with:
  - Stat allocation with accurate tiered point costs
  - Equipment slots with refinement and soul gem sockets; picker grouped and sorted by type
  - Skill tree integration — passive and buff skills automatically apply to stats and DPS
  - Critical hit DPS modelling
  - Per-skill DPS table with element, DEF, and hit chance applied
  - Build save/load and shareable URL export

---

## Getting Started

**Just want to use it?** Visit: **https://thevibeengineer.github.io/torbia-db**

No installation required — runs entirely in your browser.

---

### Run locally

The app uses `fetch()` to load JSON data files, so it must be served over HTTP — it won't work if you open `index.html` directly as a `file://` URL.

**Python:**
```bash
git clone https://github.com/TheVibeEngineer/torbia-db.git
cd torbia-db
python3 -m http.server 8000
```
Then open **http://localhost:8000** in your browser.

**Node:**
```bash
npx serve .
```

---

## Project Structure

```
index.html        — entire app (HTML + CSS + JS, single file, no build step)
CHANGELOG.md      — version history
skills.json       — skill tree (103 skills, connections, tooltips, bonuses)
equipment.json    — weapons, armour, soul gems
bestiary.json     — monster data
npcs.json         — NPC data
usables.json      — usable items
items/            — item icons
monsters/         — monster icons
equipment/        — equipment icons
skills/           — skill icons
```

The entire application lives in `index.html` — no framework, no bundler, no dependencies. Each tab is lazy-initialised on first view.

---

## Contributing

Contributions are welcome! Here are the most useful ways to help:

### Data corrections
If you spot incorrect stats, missing items, wrong skill values, or anything that doesn't match the game — open an issue or PR against the relevant JSON file (`skills.json`, `equipment.json`, `bestiary.json`, etc.).

### Formula / mechanics corrections
Game mechanics like damage formulas, stat scaling, and level curves were reverse-engineered from in-game observation. If you have better data points or can confirm/correct a formula, open an issue with your evidence and we'll update the code.

### Known gaps
- **Auto-attack DPS not modelled** — ASPD is calculated but never used to compute damage output; the DPS table only covers active skills. Will also require `attackDelayMultiplier` added to weapon data.
  - Frenzy Slash: on-use buff, max 5 stacks, +N ATK per stack (N = skill level) for 10s — would add to auto-attack baseline once that exists
  - Conqueror: on-hit buff, max 3 stacks, +2/4/6 flat damage per stack for 10s — same
  - Lethal Tempo: on-hit buff, max 5 stacks, +1 ASPD per stack, duration scales with skill level (3s–15s) — same
- Formula passive bonuses (e.g. `+Strength/20 Attack`) — unclear whether the game calculates these off base stats only or base + flat bonuses; needs in-game verification before changing
- Attunement passives (`+N% Fire/Ice/Earth/Electric skill damage`) are currently applied as a global damage multiplier rather than conditionally to matching-element skills only — needs element-conditional DPS logic to fix properly
- Some skills (e.g. Blizzard) grant a weapon element imbue mid-tooltip (`"Imbues you with Ice element"`) but their first tooltip line is not `"Applies buff"`, so the element override is not currently detected — needs a broader parsing pass or case-by-case handling

### Code contributions
The whole app is one HTML file. To make a change:
1. Fork the repo
2. Run locally with `python3 -m http.server 8000`
3. Edit `index.html` directly
4. Test in browser
5. Open a PR with a description of what you changed and why

The architecture is documented in `CHANGELOG.md` and inline comments in `index.html`.

---

## Credits

- **Zark** — Torebia Dev
- **DMON** — Torebia Dev
- **Juaninside** — Torebia Dev
- **RTX** — original database tool through v4.2
- **Rogane** — Help with formulas, database entries, and values

---

## Data

Game data files (`*.json`) and image assets (`items/`, `monsters/`, `equipment/`, `skills/`) are extracted from the game and are the property of their respective owners. They are **not** covered by the MIT licence and are included here for informational/community purposes only.

The application code (`index.html`) is MIT licenced.

---

## Licence

MIT — see file header in `index.html`.
