# Game Database Changelog

## v4.5 (2026-04-15)

### Armory — Renamed from Forge
- Tab renamed from "Forge" to "Armory"; tab order updated to Bestiary → Armory → Build Planner → Elements → NPCs

### Armory — Equipment organised by slot
- Weapons, Head, Head Lower, Back, Chest, Feet, Accessory, Souls sub-tabs replace the old flat list
- Weapon list grouped into sections by weapon type (One-Handed Sword, Two-Handed Axe, etc.)
- Armor tabs filter to the relevant slot automatically — no redundant label on each card

### Armory — Item source info
- Source filter bar (Crafted / Shop / Drop) — multiselect, all on by default
- Source badges (gold Craft / blue Shop / orange Drop) on each list card
- Detail panel now shows **Drops From** (mob name + drop %) and **Sold By** (NPC name, zone, price) sections
- Items that are both craftable and sold or dropped show all applicable badges

### Armory — Duplicate name fix
- Items sharing a display name are disambiguated with a `[N]` soul-slot suffix (e.g. Cloth Hood vs Cloth Hood [1])

### Armory — Recipe info merged into item detail
- Craft recipe (ingredients, anvil success rates) now appears inline in the item detail panel rather than as a separate pane

### Build Planner — Equipment picker improvements
- Picker grouped by weapon/armor type, sorted ATK/DEF low-to-high
- Picker anchors to the slot button (top-left to top-right); falls back gracefully if near screen edge
- Accessory picker no longer incorrectly center-aligns; long lists no longer clip at the bottom

### DPS Calculator — Removed
- DPS Calculator tab removed; all calculation capability lives in Build Planner

## v4.4 (2026-04-14)

### Build Planner — Weapon Element Imbue Skills
- **Sacred Blade** and **Poisoned Blade** now correctly override weapon element in DPS calculations when points are allocated
- Any `Applies buff` skill whose tooltip contains an element imbue line (e.g. `"Main weapon gains Holy element"`, `"Imbues you with Poison element"`) is automatically detected — future element-imbue skills will work without code changes

### Project
- Published at **https://thevibeengineer.github.io/torbia-db** — no install required
- Added MIT `LICENSE` file and `.gitignore`

## v4.3 (2026-04-14)

### Build Planner — Stat & Level Formula Fixes
- **Fixed stat point cost** — raising a stat now uses the correct tiered cost (1–10: 2 pts each, 11–20: 3 pts each, 21–30: 4 pts each, etc.) instead of flat 1 pt per level
- **Fixed points-per-level formula** — total stat point budget now correctly starts at 25 and gains `floor(level / 5) + 3` per level (+3/level at levels 1–4, +4 at 5–9, +5 at 10–14, etc.)
- **Fixed base level cap** — warning threshold corrected from 50 to 99
- **Fixed skill level cap** — warning threshold and display cap corrected from 50 to 69

### Build Planner — Buff Skills Now Apply to DPS
- Active buff skills (Blessing, Agility, Tempering Party, Berserker Roar, Forge Aura, Improve Concentration, Quicken skills, Overheat, etc.) now automatically apply their bonuses when points are allocated to them
- Weapon-type conditions respected (e.g. One-handed Quicken only applies ASPD if a one-handed sword is equipped)
- Forge Aura correctly sets max ATK variance mode

### Build Planner — Base Stat Propagation
- Added effective base stat variables (`effSTR`, `effAGI`, `effVIT`, `effINT`, `effDEX`, `effLUCK`) combining allocated stats with all flat bonuses from equipment, souls, passive skills, and active buffs
- All derived stat formulas (ATK, MATK, ASPD, HIT, FLEE, HP, SP, Cast Reduction, Max Weight) now use effective stats — e.g. Blessing's +STR correctly increases ATK, Agility's +AGI correctly increases FLEE and ASPD

### Build Planner — Critical Hits in DPS
- Crit chance now factors into skill DPS output using expected-value math: `DPS × (1 + critChance × (critMultiplier − 1))`
- Base crit multiplier is 150%; `% Critical Damage` bonuses stack on top
- Blood Instinct, Brutality, and any other crit-boosting passives/buffs now visibly move DPS numbers

### Build Planner — Formula Passive Bonuses
- Added support for `+StatName/Divisor TargetStat` bonus format
- **Blade's Edge**: now correctly applies `floor(STR / 20)` bonus ATK
- **Mana Reservoir**: now correctly applies `floor(INT / 15)` bonus SP Recovery

### Build Planner — Bonus Stat Display
- Stat slider rows now show a green `+N =Total` indicator when equipment, souls, or skills are providing flat bonuses to a base stat

## v4.2 (2026-04-11)

### Hit Chance System
- **Bestiary**: HIT/FLEE now show effective values (Lv + DEX/AGI + base) with base in parentheses
- **Bestiary**: Tooltip on HIT/FLEE rows shows full breakdown (hover anywhere on the row)
- **Build Planner**: Fixed HIT formula from `DEX + LUCK/5` to correct `BaseLv + DEX`
- **Build Planner**: Fixed FLEE formula from `AGI + LUCK/5` to correct `BaseLv + AGI`
- **Build Planner**: Added Hit% to derived stats panel
- **Build Planner**: Mob stats display shows FLEE and hit chance
- **Build Planner**: Physical DPS factors in hit chance, Hit% column in DPS table
- **Build Planner**: Kill time ("Die") accounts for mob miss rate

### Formulas
- `HIT = BaseLevel + DEX + baseHIT`
- `FLEE = BaseLevel + AGI + baseFLEE`
- `hitChance = 80 + attacker.HIT - target.FLEE`

## v4.1

- Build Planner: build share codec
- Build Planner: soul socket system
- Build Planner: passive skill bonuses affect derived stats

## v4.0

- Initial release with Bestiary, Forge, Skills, Elements, DPS Calculator, Build Planner
- 23 monsters, 53 forge recipes, 103 skills, 12x12 element table
- Pinnable/draggable tooltips, "Dropped by" cross-references
