# Spellborn game design decisions

Decisions agreed so far. Numbers marked *sample* are placeholders until balancing.

## Fights

- **Moments are AGI-ordered rounds.** Each moment, every living unit acts once, highest AGI first across both sides. Ties go to party order, then enemy order.
- **No randomness.** No random dodge, crit or damage rolls: the same spellbooks against the same encounter always give the same fight. Effects that are usually chances (critical hits, blocking) work as meters instead.
- **Party size** defaults to 3 heroes; screens and the simulation handle more.

### Formation
- **The party uses a 2×2 grid:** a front row and a back row, 2 heroes each (4 at most, matching the 4 party slots).
- **Enemies also stand in a front and back row, but have no size limit.** An encounter can field any number of enemies per row.
- **Reach:** melee can only reach the back row once the front row is empty; ranged attacks and spells reach either row.
- **An empty front row promotes the back row**, so melee always has a target.
- **Moving:** `move_front()` and `move_back()` cost the moment. A hero moving into a full party row fizzles, and the replay says why.
- **Row trade-offs:** the front row deals and takes more damage; the back row is safer, but melee from it is weak. **Taunt only works from the front row.**
- **Row-wide effects:** some spells hit a whole row (*examples:* a cleave on the front row, a nova on the back), so stacking a row is risky.
- **Row-aware targets and checks** in spellbooks: `front_enemy`, `back_enemy`, `self.row`, `enemy.front_count` (*names to confirm with the language design*).

## Stats

| Stat | Effect |
| --- | --- |
| STR | Physical damage (attacks and physical skills). |
| VIT | Max HP. |
| AGI | Turn order within a moment. |
| INT | Spell power (heals, fire, shields). |
| WIS | Max mana and mana regained each moment. |

- **Stat points:** each class has fixed starting stats; every character level gives stat points that **the player assigns** (*sample:* 3 points per level).
- **Mana regen** each moment comes from WIS plus mana regen sub-bonuses on gear.

Spellbooks can read stats and resources: `self.mana`, `self.hp_pct`, `nearest_enemy.agi > self.agi`.

## Classes (first version)

| Class | Leans on | Role |
| --- | --- | --- |
| Healer | INT, WIS | Heals, group mend, revives with last_rite. |
| Tank | VIT, STR | Taunts and shields; soaks hits. |
| Ranger | AGI, STR | Acts early, hits the back row, interrupts charging enemies. |
| Mage | INT, WIS | Sets enemies burning, then fire_nova. |

## Spells and resources

- **Kit size:** every class has a free, weak **basic attack plus 4 spells** in the first version, unlocked at character levels **1, 3, 7 and 12**.
- **Mana and cooldowns.** Each spell costs mana and has a cooldown in moments.
- `ready("spell")` is true when the spell is off cooldown **and** the caster has enough mana. (Proposed; confirm when the spell kits are designed.)
- Casting without enough mana, off cooldown, or without a built-in requirement **fizzles**: the hero loses the moment and the replay says why ("mend needs 6 mana, you have 4").

## Focus (code budget)

- **Cost:** 1 per rule, plus 1 per part of its condition (each comparison or check joined by `and` / `or`).
- **Budget:** set by the hero's **artifact**, which also carries one logic perk. Not tied to a stat, so no stat is mandatory for every class. Choosing an artifact is choosing how much logic a hero can run.

## Gear

- **Slots:** helmet, chest, gloves, boots, primary weapon, off-hand, 2 rings, artifact. All slots are open from level 1.
- **Universal sets** come from **chapter reward pools**: each chapter has several stages and its own pool, and clearing stages awards pieces from it. Any class can wear them. Sets are built around playstyles, not stats (*examples:* Bulwark for protection, Ember for burning, Vigil for mana and acting early, Tactician for logic perks).
- **Set pieces:** helmet, chest, gloves, boots, primary, off-hand. Set bonuses at 2, 4 and 6 pieces, mixing numbers and logic (*example:* 2 pieces +stats, 4 pieces a new condition, 6 pieces a signature effect). **Set bonuses carry the playstyle;** a piece's main stat comes from its slot.
- **Weapons** come from stages too, but by type: each class uses its own weapon types (*examples:* Tank sword and shield, Ranger bow and quiver, Mage staff, Healer lantern).
- **Class reward gear:** character levels give class-specific pieces (*example:* level 1 helmet, level 3 boots…). They are weaker than stage gear, have **fixed stats (no rolls)**, and form a small class set with a modest bonus tied to the class's kit, so keeping one or two can still pay off.
- **No gear upgrades in the first version;** better gear replaces old gear. Upgrading may come later.
- **Rings and artifacts** fit any class, are not part of any set, and come from medals, challenge modes and the daily boss.

## Rarity and rolled stats

- **Rarities:** common, uncommon, rare, epic, legendary, mythic. **Mythic is planned but not in the first version:** the first draft has no mythic items. The design below records the intended plan so it can be added later.
- **Main stat:** every piece has a main stat **fixed by its slot**, with a value range rolled when the piece is obtained (*sample:* 1 to 5 STR). Which stat each slot gets is still open; rings and artifacts should offer more variety than armour. There is no bad-luck protection.
- **Sub-bonus pool** (all deterministic, all readable from spellbooks):

| Group | Sub-bonus | Effect |
| --- | --- | --- |
| Core | STR, VIT, AGI, INT, WIS | As in Stats. |
| Resource | Max mana, Mana regen | Bigger pool, faster refill. |
| Meters | **Precision** | Each hit fills a crit meter; at 100% the next hit crits and the meter resets (25% = every 4th hit). Readable as `self.crit_ready`. |
| Meters | **Block** | Each hit taken fills a block meter; at 100% the next hit taken is halved. Readable as `self.block_ready`. Replaces dodge. |
| Defence | **Armour** | Reduces physical damage. |
| Defence | **Resistance** | Reduces spell damage. |
| Offence | **Penetration** | Ignores part of the target's armour or resistance. |
| Sustain | **Leech** | Heals the attacker for part of the damage dealt. |
| Tempo | **Haste** | Shortens cooldowns. |

- **Two damage types:** physical and spell, so Armour, Resistance and Penetration make the choice of attacker and target matter.
- **Build-defining states live only in set bonuses**, never as rolled sub-bonuses, so they stay deliberate and never stack across pieces (*examples:* Momentum, a bonus each moment the same rule fires again; Adaptation, a bonus when a different rule fires than last moment; Affliction, stronger statuses; Last stand, more damage below 30% HP).
- **Sub-bonuses** are added by rarity and also rolled:

| Rarity | Sub-bonuses | Roll ranges | Extra |
| --- | --- | --- | --- |
| Common | 0 | Base | |
| Uncommon | 1 | Base | |
| Rare | 2 | Base | |
| Epic | 3 | Base | |
| Legendary | 4 (cap) | Higher | |
| Mythic (planned, not in first version) | 4 | Highest | One unique logic perk (*examples:* +1 memory slot, the first rule each fight costs no focus, unlocks a new condition) |

- **Fights stay deterministic:** rolls happen only when gear is obtained, never during a fight, so the same spellbooks and the same gear always give the same fight.

### How a drop is rolled
1. **Rarity first, rarest tier down.** Check legendary against its chance; if it fails, check epic, then rare, then uncommon. If every check fails, the drop is **common**, so every drop gives at least a common item.
2. **Chances can exceed 100%.** Bonuses (from stage difficulty, medals or gear, to be decided) add to a tier's chance; at 100% or more that tier is guaranteed.
3. **Then the item.** Pick one item from the chapter's reward pool among the items available at the rolled rarity, then roll its main stat and sub-bonuses.

## Progression

### Start
- The player picks **one hero from any of the 4 classes**. Every class gets a basic attack and a way to survive alone, and Chapter 1 encounters are solvable by any class.

### Two levels
| | Character level (each hero) | Account level (the player) |
| --- | --- | --- |
| Earned from | Any win with that hero, repeats included, scaled by level gap | Milestones only: first clears and challenge medals (fewest lines, under focus, fastest win) |
| Gives | Stat points the player assigns, that class's spells one at a time, and class reward gear | New classes, party slots, sigil library, saved spellbooks, challenge modes, sandbox |
| Limit | Capped by account level | Rises only by beating new content |

- **Level-gap scaling:** full XP when an encounter is at or above the hero's level, less the further below it is, none at the cap.
- **Max character level:** 100.
- **Level curve:** each level needs 15% more XP than the one before up to level 20, then 5% more per level (*sample:* 100 XP to reach level 2). Level 100 needs about 600 times the XP of level 2 (15% all the way would be about 890,000 times), so numbers stay readable.
- **Stage XP** grows at the same 15% per stage level, so a first clear is always worth about half a level (*sample*).
- **First clears count per account.** The first time any party clears a stage, every hero in it earns full stage XP. After that, every clear of that stage pays **10%** of it, for any hero.
- **Formula for one win:** `stage XP × (1 on the account's first clear, else 0.10) × level-gap factor`, stopped at the hero's level cap.
- **The cap keeps logic first:** a stuck player can grind a hero up to the cap (the level the current content is designed for) but never past it.
- **Recruits start at level 1** and learn their kit spell by spell. Older encounters give them full XP, so catch-up is quick.

### Roster and party
- Account level makes a class available; the class then joins through a **recruitment fight**: it arrives with a spellbook that has a bug, and fixing the misfiring line recruits it.
- **Party slots** grow with account level, up to 4. Every fight uses all unlocked slots by default; an encounter can set a lower cap for constraint puzzles.

### Account level unlocks
- Classes and party slots.
- **Sigil library:** more conditions and targets for drag-and-drop spellbooks.
- **Saved spellbooks:** several saved versions per hero, to swap strategies per encounter.
- **Challenge modes:** the daily boss and remixed old encounters (for example no healer, half the focus budget).
- **Sandbox:** write enemy spellbooks and test a party against them.

### What stays outside levels
- **Language features** are unlocked by chapter encounters, each introduced by the fight that needs it, never by account level.
- **Ranked modes** (daily boss, challenges) are **not in the first version.** When added, they fix hero levels and use a fixed standard loadout, so leaderboards compare logic, not progress or lucky rolls.
- **Stat growth** from levels stays small; most power comes from logic, gear perks and the artifact's focus.

### Planned for later: stat reset
Not in the first version. Draft plan:
- **Free resets below a level threshold** (*sample:* up to level 10), so new players can experiment.
- **After that, a reset costs a currency** earned from medals or challenges, never bought with real money, so resets reward playing well.
- **Saved builds:** a stat build is saved alongside a saved spellbook, so swapping strategy per encounter swaps both.
- **Ranked modes ignore it:** they use fixed stats and gear anyway.

## Encounter design rules

- **Every enemy has its own spellbook**, readable before the fight. Reading the enemy is part of the puzzle.
- **Enemies are stronger than the party** in stats. The party wins by out-thinking them, not out-levelling them, so players have to read enemy spellbooks closely.
- **Enemy spellbooks grow with the game:** early enemies run one or two rules; later ones use memory, timing and coordination, the same language features the player is learning.
- **One-rule spellbooks should win the first fights.** Each new encounter then breaks a naive spellbook at one specific point, and the replay shows the line that failed.
- **Each encounter is built around one idea** and records which naive spellbook it beats and why, for example:
  - charged attacks punish a Tank that taunts on cooldown;
  - mana drain punishes a Healer that tops up allies at 95%;
  - a back-row healer punishes always hitting the nearest enemy;
  - reflect windows punish attacking every moment;
  - cursed allies punish "heal the lowest" blindly;
  - silence zones punish relying only on spells;
  - two simultaneous threats punish a single taunt plan;
  - a moment limit punishes pure defence.
- **Spell kits support this:** every class has a free, weak basic attack; strong spells cost mana, have cooldowns or need built-in conditions, and fizzle when used blindly.
- **Gear perks reward conditional play**, not raw stats.
- **Grinding can't bypass a fight:** hero levels are capped by account level, at the level current content is designed for.
- **Fewest-lines leaderboards** still reward simple spellbooks whenever they genuinely win.

## Still open

- Each class's spell kit: names, mana cost, cooldown, stat scaling, built-in requirements.
- Base stats per class, stat points per level, and the formulas (damage from STR, HP from VIT, and so on).
- Mana numbers: pool size and regen per point of WIS.
- Main stat for each slot; the stat variety on rings and artifacts.
- Gear numbers: stat ranges per rarity, artifact focus values, the first universal sets and their bonuses.
- Drop chances per rarity, and what can raise them (stage difficulty, medals, gear).

## First version scope

- Levels 1 to 20, with 4 spells per class.
- Class reward gear and the first universal sets for that range.
- Rarities from common to legendary. Mythic items come later.
- No gear upgrades, no ranked modes, no stat reset.
- Max level stays 100 for later content.
