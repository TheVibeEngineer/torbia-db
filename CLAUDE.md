# CLAUDE.md — Game Database Project Brief

## What this is
A single-file browser app (`index.html`) that serves as a game database and build planner. It loads game data from local JSON files via `fetch()` and renders everything client-side. **It must be served over HTTP — it will not work opened directly as a file:// URL.**

### How to run locally
```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

---

## Project structure

```
index.html          — entire app (HTML + CSS + JS, ~4200 lines)
CHANGELOG.md        — version history
skills.json         — skill tree data (103 skills, connections, tooltips, bonuses)
equipment.json      — weapons, armor, soul gems (exportDate, weapons[], armor[], souls[])
bestiary.json       — monster data
npcs.json           — NPC data
usables.json        — usable items
items/              — item icons
monsters/           — monster icons
equipment/          — equipment icons
skills/             — skill icons
```

---

## Architecture

The entire app lives in `index.html`. There is no build step, no bundler, no framework. Tabs are rendered by `switchTab(tabName)` which lazy-initialises each section:

| Tab | Init function | Key globals |
|---|---|---|
| Bestiary | `initBestiary()` | `data` |
| Forge | `initForge()` | `forgeInitialized` |
| Skills | `initSkills()` | `skillData`, `skillMap` |
| Elements | `renderElementTable()` | `ELEM_TABLE` |
| DPS Calculator | `initDPS()` | `dpsInitialized` |
| Build Planner | `initBuildPlanner()` | `bpBuild`, `bpEquipData` |

---

## Build Planner — the main area of active development

### State
```javascript
bpBuild = {
  stats: { STR, AGI, VIT, INT, DEX, LUCK },  // base allocated stats (1–99)
  equipment: { SlotName: { item, refine, souls[] } },
  skillLevels: { [guid]: level },             // 0 = not learned
  targetMob: guid | null,
  notes: ''
}
```

### Key functions
- `bpRecalc()` — master recalculation, called after any change. Aggregates all bonuses, computes derived stats, runs DPS table. **This is the most important function (~400 lines).**
- `bpInitStatSliders()` — renders the stat slider rows (called once on init, re-rendered on build reset)
- `bpRenderSkillNodes()` — renders the skill tree canvas
- `bpRenderSlots()` — renders equipment slots

### Stat point cost system (v4.3)
Each point of a stat costs more as the stat gets higher:
```javascript
function bpStatPointCost(v) {
  // cost per increment = floor(i / 10) + 2
  // so 1→10 costs 2 each, 10→20 costs 3 each, 20→30 costs 4 each
}
```

### Points per level (v4.3)
```javascript
function bpPointsAtLevel(level) {
  // starts at 25, gains floor(level / 5) + 3 per level
  // +3/level at 1–4, +4/level at 5–9, +5/level at 10–14, etc.
}
// Confirmed data points: Lv1=25, Lv2=28, Lv3=31, Lv5→+4, Lv52=433
```

### Level caps
- Base level: **99**
- Skill level: **69**

### Bonus aggregation pipeline (in bpRecalc order)
1. Equipment flat/pct bonuses → `flatBonuses`, `pctBonuses`
2. Passive skill bonuses (from `skill.levels[n].bonuses`) → same maps
   - Supports: `+N Stat`, `+N% Stat`, `+N% damage with X attacks`, `+StatName/Divisor TargetStat` (e.g. Blade's Edge: `+Strength/20 Attack`)
3. Active buff skill bonuses (parsed from `skill.tooltips[n]` where first line starts with `"Applies buff"`) → same maps
   - Weapon-type conditions honoured (e.g. One-handed Quicken only applies if weapon matches)
   - Forge Aura sets `useMaxVariance = true`
4. Effective base stats computed: `effSTR = s.STR + (flatBonuses.STR || 0)` etc.
5. All derived stat formulas use `effSTR/AGI/VIT/INT/DEX/LUCK`

### DPS calculation
- Per active leveled skill: `rawTotal = base (totalATK or totalMATK) × multiplier`
- Against a mob: applies hard DEF reduction, soft DEF, element table, hit chance
- Crit: `effTotal × (1 + critChance × (critMultiplier − 1))` where `critMultiplier = 1.5 + CriticalDamage% / 100`

### Stat bonus display (v4.3)
Each stat slider row has `<span id="bpBonus-STR">` etc. Updated in `bpRecalc` to show `+N =Total` in green when bonuses exist.

---

## Data formats

### skills.json
```json
{
  "skills": [{
    "guid": "...",
    "name": "Blessing",
    "isPassive": false,
    "maxLevel": 5,
    "archetype": "Priest",
    "requiredSpentPoints": 0,
    "prerequisites": ["guid", ...],
    "x": 100, "y": 200,           // skill tree position
    "levels": [{ "manaCost": 10, "bonuses": ["+1 Strength"] }],
    "tooltips": ["Applies buff for 60s: Blessing\r\n+1 Strength\n+1 Intelligence\n+1 Dexterity."]
  }],
  "connections": [{ "from": "guid", "to": "guid" }]
}
```

### equipment.json
```json
{
  "weapons": [{
    "guid": "...", "name": "...", "damage": 50, "magicDamage": 0,
    "weaponType": "One-Handed Sword", "weaponLevel": 2,
    "equippableSlots": ["WeaponMain", "WeaponSecondary"],
    "bonuses": [{ "type": "FlatBonus", "stat": "ATK", "amount": 10 }],
    "soulSlots": 2, "weight": 80
  }],
  "armor": [{ "guid": "...", "armorType": "Helmet", "defense": 20, "bonuses": [...] }],
  "souls": [{ "guid": "...", "name": "...", "bonuses": [...] }]
}
```

### Bonus types in equipment
- `{ type: "FlatBonus", stat: "ATK", amount: 10 }` → flat +10 ATK
- `{ type: "PercentBonus", stat: "ATK", percent: 5 }` → +5% ATK
- `{ type: "WeaponPhysicalDamageBonus", weaponType: "...", percent: 10 }` → +10% phys damage with specific weapon
- `{ type: "OnHitFlatBonus", amount: 5 }` → +5 flat on-hit damage
- `{ type: "MaxAtkVarianceBonus" }` → always deal max ATK

### bpMapStatName(name)
Maps display stat names to internal keys used in `flatBonuses`/`pctBonuses`. Lives at the bottom of the script. **Add new mappings here if new stat types appear in data.**

---

## Known limitations / future work
- Passive skill formula bonuses use `s.STR` (base stat) rather than fully buffed value — two-pass calculation would be more accurate but is a low priority
- `Roll the dice` skill is non-deterministic and not applied to DPS (intentional)
- Sacred Blade / Poisoned Blade change weapon element — not yet wired into the element damage table for DPS
- Frenzy Slash stacking buff not modelled in DPS
- Conqueror / Lethal Tempo on-hit stacking buffs not modelled in DPS

---

## Version history summary
- **v4.3** — stat cost formula, level formula, buff skill DPS, crit DPS, formula passives, bonus stat display
- **v4.2** — HIT/FLEE system, build planner hit chance
- **v4.1** — build share codec, soul sockets, passive skill bonuses
- **v4.0** — initial release
