# Spellborn

*A party RPG autobattler where your spells don't cast themselves. You write the conditions that make them fire.*

---

## What is it?

Spellborn is a party RPG where you never press an attack button. Before each fight you write a spellbook for every hero: a short set of rules that decides what they do, when they do it, and to whom. Then you press **Run** and watch your party carry out your logic, one moment at a time.

If they win, your logic held up. If they lose, you watch the replay, find the rule that misfired, and fix it.

The game takes the "programming as gameplay" idea from games like *The Farmer Was Replaced* and the tactical party scripting from systems like *Final Fantasy XII*'s gambits, and combines them into an autobattler where **your logic is your build**.

## The twist: spells need conditions

In Spellborn, spells aren't buttons with cooldowns. Each spell has to be **bound to conditions**, and it only fires when those conditions are true.

- A heal does nothing until you tell it *who* is hurt enough to deserve it.
- A taunt is useless if it's cast at the wrong moment.
- Some powerful spells come with **built-in requirements**. For example, a fire nova that only ignites when enough enemies are already burning, or a last rite that only works the moment an ally falls.

Conditions are written as **sigils**, readable rules such as "an ally is badly wounded" or "the enemy is charging a big attack". Sigils are bound to spells and arranged in priority order. When a spell is cast without its conditions met, it **fizzles** and the hero loses the moment.

Setting up those conditions, often across several heroes at once, is the heart of the game.

```
# A healer's spellbook
if weakest_ally.hp_pct < 60: heal(weakest_ally)
if ready("mend") and allies_hurt() > 1: mend()
attack(nearest_enemy)
```

## How you play

1. **Read the encounter.** Enemies follow spellbooks too, and you can read theirs before the fight.
2. **Write your party's spellbooks.** Choose which spells each hero uses and which sigils trigger them.
3. **Run the fight.** Combat plays out on its own, deterministically.
4. **Study the replay.** A step-by-step log shows which rule fired for each hero at every moment.
5. **Refine and win.** Victories unlock new spells, new sigils and new language features.

## Your spellcasting grows with you

The scripting language unlocks gradually, and each new feature is introduced by an encounter that's awkward or impossible without it:

- **Priority rules**: the first true condition wins, so order is strategy.
- **Memory**: heroes remember things between moments, for example counting an enemy's attacks to predict the big one.
- **Lists and loops**: finding the right target in a crowd, such as the enemy healer hiding at the back.
- **Party messages**: heroes signal each other so they act on the same moment ("go now!").
- **Custom spells**: your own reusable functions.
- **Reading intent**: seeing what an enemy is casting and interrupting it.

Heroes also have a limited **focus** (a code budget) and **memory**. Clever spellbooks cost more, and better gear lets you write smarter ones. Writing elegant logic is part of the challenge.

Players who don't code can start with drag-and-drop sigils that turn into real code as they progress.

## Design pillars

- **Logic is the build.** Stats matter, but a well-written spellbook beats better gear with sloppy logic.
- **Same input, same fight.** Every battle is fully deterministic, so replays, shared solutions and leaderboards can be trusted.
- **Unlocks answer pain you've already felt.** New features arrive when you've already wished for them.
- **Failures are readable.** A loss always points at a line you can fix.

## Features (planned)

- A party of heroes from distinct classes, each with its own spell kit
- Hand-crafted encounters, each built around one idea
- Readable enemy spellbooks, so understanding your opponent is a skill in itself
- Replay viewer with a step-by-step log and line highlighting
- **Daily boss**: the same fight for everyone, with leaderboards for fewest lines, fastest win and lowest focus used
- Shareable solutions and replays
- Plays in the browser, with no download or account needed to start

## Tech overview

- **Engine**: a pure, deterministic TypeScript simulation that runs in a Web Worker
- **Language**: a small custom scripting language with a hand-written parser and interpreter, so execution is sandboxed and budgeted by design
- **Verification**: the same engine runs on the server to check leaderboard submissions, so scores can't be faked
- **Hosting**: static frontend plus a lightweight serverless backend, with no AI calls at runtime

## Status

Early design. The party size, number of classes, spell lists and balance values are not decided yet.

## License

TBD.