# Game Database

A browser-based game database and build planner. Browse monsters, weapons, armour, skills, and plan your character build — all client-side, no server required beyond a simple static file host.

![Game Database](https://img.shields.io/badge/version-4.3-green) ![License](https://img.shields.io/badge/license-MIT-blue)

---

## Features

- **Bestiary** — searchable monster list with stats, elements, drops, and HIT/FLEE breakdowns
- **Forge** — crafting recipes with ingredient cross-references
- **Skill Tree** — interactive visual skill tree with tooltips and archetype filtering
- **Element Table** — full 12×12 element damage reference
- **DPS Calculator** — stat-based damage calculator with mob targeting and hit chance
- **Build Planner** — full character planner with:
  - Stat allocation with accurate tiered point costs
  - Equipment slots with refinement and soul gem sockets
  - Skill tree integration — passive and buff skills automatically apply to stats and DPS
  - Critical hit DPS modelling
  - Per-skill DPS table with element, DEF, and hit chance applied
  - Build save/load and shareable URL export

---

## Getting Started

The app uses `fetch()` to load JSON data files, so it must be served over HTTP — it won't work if you open `index.html` directly as a `file://` URL.

**Python (easiest):**
```bash
git clone https://github.com/YOUR_USERNAME/game-db.git
cd game-db
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

### Known gaps (good first issues)
- Sacred Blade / Poisoned Blade change weapon element — not yet wired into the DPS element table
- Frenzy Slash stacking buff not modelled in DPS
- Conqueror / Lethal Tempo on-hit stacking buffs not modelled in DPS
- Formula passive bonuses currently use base stat values rather than fully-buffed values (two-pass calculation needed)

### Code contributions
The whole app is one HTML file. To make a change:
1. Fork the repo
2. Run locally with `python3 -m http.server 8000`
3. Edit `index.html` directly
4. Test in browser
5. Open a PR with a description of what you changed and why

If you're using Claude Code, a `CLAUDE.md` is included with full architectural notes.

---

## Data

Game data files (`*.json`) and image assets (`items/`, `monsters/`, `equipment/`, `skills/`) are extracted from the game and are the property of their respective owners. They are **not** covered by the MIT licence and are included here for informational/community purposes only.

The application code (`index.html`) is MIT licenced.

---

## Licence

MIT — see file header in `index.html`.
