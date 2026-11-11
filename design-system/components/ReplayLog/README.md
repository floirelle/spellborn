# ReplayLog

The step-by-step record of a fight: one row per action, naming the moment, the actor, the spell and the spellbook line that caused it.

**Markup.** `<div class="sb-log" role="log">` holding `sb-log__entry` rows of four cells: `sb-log__t` (moment, `t14`), `sb-log__who`, `sb-log__what` (the call, then `<span class="rule">· line N</span>`) and `sb-log__out` with an outcome class: `out-harm`, `out-mend`, `out-guard` or `out-fizzle`.

| Modifier | Use |
| --- | --- |
| `sb-log__entry--current` | The moment the replay scrubber is on. `gilt-soft` row, and the moment sits in a gilt tab (`<span class="sb-tab">t14</span>`), the same mark as a fired rule. |
| `sb-log__entry--fizzled` | A fizzle. Strikethrough call, and the outcome says why: "fizzled: 1 burning, needs 3". |
| `sb-log__entry--foe` | An enemy's action. Muted italic name. |

**States.** Use one `sb-log__status` paragraph instead of entries:
- Empty: "No fight yet. Press **Run fight** and every action will appear here, linked to its line."
- Running: "Running the fight: moment **42**…" with `aria-busy="true"` on the log.
- Error (`sb-log__status--error`): name the line that stopped the fight and what to do.

- Do always link a row to its line number; a loss must point at a line the player can fix.
- Do write outcomes as signed numbers (−34, +22) with a true minus sign, or a short word (guarding).
- Don't truncate the fizzle reason; it is the most useful text on the screen.
