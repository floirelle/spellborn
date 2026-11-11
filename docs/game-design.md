# Spellborn game design decisions

Decisions agreed so far. Numbers marked *sample* are placeholders until balancing.

## Fights

- **Moments are AGI-ordered rounds.** Each moment, every living unit acts once, highest AGI first across both sides. Ties go to party order, then enemy order.
- **No randomness.** No random dodge, crit or damage rolls: the same spellbooks against the same encounter always give the same fight. Effects that are usually chances (critical hits, blocking) work as meters instead.
- **Party size** defaults to 3 heroes; screens and the simulation handle more.

### Formation
- **The party uses a 2×2 grid:** a front row and a back row, 2 heroes each (4 at most, matching the 4 party slots).
- **Enemies also stand in a front and back row, but have no size limit.** An encounter can field any number of enemies per row.
- **Reach:** single-target attacks and spells only reach the **front row**. The back row can be hit by:
  - **row-wide effects** (*examples:* `fire_nova`, `volley`), which hit their whole row;
  - **the Ranger's weaker skills**, which can target any unit at reduced damage (its strong skills follow the normal rule);
  - anything, once the enemy front row is empty.
- **Taunt binds everyone,** the Ranger included.

### Targeting
- **Enemies target through their own spellbooks**, using the same words as heroes, from their side: for an enemy, `weakest_enemy` means the player's weakest hero. There is no hidden aggro or threat system; every target choice comes from a readable rule.
- **Enemy spellbooks show a plain-language note** under each target word (*example:* "weakest_enemy: your weakest hero").
- **Targets are picked when the unit acts**, not at the start of the moment, so a unit that acts later in the AGI order chooses from what is left.
- **Reach rules apply to both sides.** Enemy single-target hits only reach the party's front row unless the enemy has row-wide or any-target skills.
- **Taunt overrides** every enemy single-target attack, including charged attacks, pulling it onto the taunting hero. Row-wide attacks still hit their whole row.
- **Ties** (*example:* two heroes at the same HP %) go by formation order: front-left, front-right, back-left, back-right.
- **Charged attacks lock a formation spot, not a hero.** When an enemy starts charging, the targeted spot is shown (readable as `enemy.charge_target`). When the hit lands it strikes whoever stands in that spot. If the spot is empty, the charge keeps going down the same column and hits the unit in the back row behind it. If the whole column is empty, it fizzles. Swapping heroes around can't make the attack disappear; taunt is the intended counter.
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
| Healer | INT, WIS | Heals, resistance buffs, cleansing, and some damage with holy_flame. |
| Tank | VIT, STR | Taunts and shields; soaks hits. |
| Ranger | AGI, STR | Acts early, hits the back row, interrupts charging enemies. |
| Mage | INT, WIS | Sets enemies burning, then fire_nova. |

## Spells and resources

- **Kit size:** every class has a free, weak **basic attack plus 4 spells** in the first version, unlocked at character levels **1, 3, 7 and 12**.
- **Mana and cooldowns.** Each spell costs mana and has a cooldown in moments.
- `ready("spell")` is true when the spell is off cooldown **and** the caster has enough mana. (Proposed; confirm when the spell kits are designed.)
- **Fizzle:** an action that fails because its conditions weren't met when it was used. It does nothing, the caster loses the moment, and the replay names the line and the reason ("line 4 fizzled: fire_nova needs 2 burning enemies, there was 1"). The same applies to heroes and enemies. Causes:
  - the spell is on cooldown;
  - not enough mana ("bless needs 5 mana, you have 3");
  - a built-in requirement isn't met;
  - the target is invalid or out of reach;
  - moving into a full row.

## Class kits (first version, levels 1 to 20)

All numbers are *sample* values. Basic attacks are free (no mana, no cooldown) and weak. Single-target attacks follow the reach rule (front row only) unless noted.

**Class traits:** each class has one passive, giving it a way to survive alone at level 1: Healer *Lantern light* (heals on a target below 30% HP restore 50% more), Tank *Hold the line* (Block fills twice as fast in the front row), Ranger *Quick draw* (acts first in the first moment of a fight), Mage *Kindling* (burning enemies deal 15% less damage).

### Healer (INT, WIS)
| Lv | Spell | Mana | Cooldown | Type | Effect | Requirement |
| --- | --- | --- | --- | --- | --- | --- |
| – | `smite(enemy)` | 0 | 0 | Magic | Weak damage | – |
| 1 | `heal(ally)` | 4 | 0 | – | Restore 12 HP + 2 per INT; can target self | – |
| 3 | `bless(ally)` | 5 | 3 | – | +4 + 1 per 2 INT physical and magic resistance for 3 moments | Target isn't already blessed |
| 7 | `holy_flame(enemy)` | 5 | 2 | Magic | Damage scaling with INT, and heals the weakest ally for half the damage dealt | – |
| 12 | `purify(ally)` | 5 | 2 | – | Remove burning, curse and other statuses | Target has a status |

Later (after level 20): `mend()` (group heal) and raising a fallen ally.

### Tank (VIT, STR)
| Lv | Spell | Mana | Cooldown | Type | Effect | Requirement |
| --- | --- | --- | --- | --- | --- | --- |
| – | `bash(enemy)` | 0 | 0 | Physical | Weak melee hit | – |
| 1 | `guard()` | 3 | 2 | – | +50% Block meter (max 100%); at 100% the next hit taken is halved | – |
| 3 | `taunt()` | 4 | 3 | – | Enemies must target you for 2 moments | You are in the front row |
| 7 | `barrier(ally)` | 5 | 3 | – | Absorb 10 + 1 per VIT damage of any type on one ally | – |
| 12 | `cleave()` | 6 | 3 | Physical | Damage scaling with STR to the whole enemy front row | You are in the front row |

### Ranger (AGI, STR)
| Lv | Spell | Mana | Cooldown | Type | Effect | Requirement |
| --- | --- | --- | --- | --- | --- | --- |
| – | `shoot(enemy)` | 0 | 0 | Physical | Weak hit; **reaches any unit** | – |
| 1 | `pin(enemy)` | 3 | 3 | Physical | Weak hit; cancels the target's charge; **reaches any unit** | – |
| 3 | `aimed_shot(enemy)` | 5 | 2 | Physical | Heavy hit scaling with STR and AGI, with bonus Penetration; front row only | – |
| 7 | `volley()` | 6 | 3 | Physical | Damage to the whole enemy back row | Enemy back row is not empty |
| 12 | `mark(enemy)` | 4 | 4 | – | Target takes 25% more damage from everyone for 3 moments; **reaches any unit** | Target is not already marked |

### Mage (INT, WIS)
| Lv | Spell | Mana | Cooldown | Type | Effect | Requirement |
| --- | --- | --- | --- | --- | --- | --- |
| – | `spark(enemy)` | 0 | 0 | Magic | Weak damage | – |
| 1 | `firebolt(enemy)` | 4 | 0 | Magic | Damage scaling with INT, plus burning for 3 moments | – |
| 3 | `arcane_surge()` | 6 | 5 | – | +20% to the Mage's own damage for 3 moments | Not already surging |
| 7 | `frost_ward()` | 5 | 4 | – | Absorb 8 + 1 per INT damage on yourself | – |
| 12 | `immolate(enemy)` | 6 | 4 | Magic | Deal the target's remaining burn damage at once, doubled; ends the burn | Target is burning |

- **Balance note (tune later):** at +20% for 3 moments, `arcane_surge` adds about 0.6 of a cast but costs a whole moment, so on its own it usually loses damage. Kept as agreed; revisit when numbers are tested (options considered: also boosting burn ticks, or making it instant).
- **The Mage stays a front-row specialist;** the back row is the Ranger's job, or anyone's once the front row falls.
- Later (after level 20): an area spell such as `fire_nova`, redesigned so it neither ignores reach nor scales with uncapped enemy counts.

### Set bonuses can reshape spells
Specialised versions of spells come from set bonuses rather than extra spells (*example:* Bulwark 4 pieces: `bless` gives double physical resistance and no magic resistance).

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
- **Main stat:** every piece has a main stat **fixed by its slot**, with a value range rolled when the piece is obtained (*sample:* 1 to 5 STR). Which stat each slot gets is still open; rings and artifacts should offer more variety than the other slots. There is no bad-luck protection.
- **Sub-bonus pool** (all deterministic, all readable from spellbooks):

| Group | Sub-bonus | Effect |
| --- | --- | --- |
| Core | STR, VIT, AGI, INT, WIS | As in Stats. |
| Resource | Max mana, Mana regen | Bigger pool, faster refill. |
| Meters | **Precision** | Each hit fills a crit meter; at 100% the next hit crits and the meter resets (25% = every 4th hit). Readable as `self.crit_ready`. |
| Meters | **Block** | Each hit taken fills a block meter; at 100% the next hit taken is halved. Readable as `self.block_ready`. Replaces dodge. |
| Defence | **Physical resistance** | Reduces physical damage. Readable as `phys_res`. |
| Defence | **Magic resistance** | Reduces magic damage. Readable as `magic_res`. |
| Offence | **Penetration** | Ignores part of the target's physical or magic resistance. |
| Sustain | **Leech** | Heals the attacker for part of the damage dealt. |
| Tempo | **Haste** | Shortens cooldowns. |

- **Two damage types: physical and magic.** Physical resistance, magic resistance and Penetration make the choice of attacker and target matter (*example:* `if nearest_enemy.phys_res > 10: firebolt(nearest_enemy)`).
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

- Exact spell numbers and balance.
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
