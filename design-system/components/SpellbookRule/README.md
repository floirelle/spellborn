# SpellbookRule

One numbered priority line of a spellbook; in a replay it shows whether that line fired, was skipped or fizzled, and in the editor whether it has an error.

**Markup.** Wrap rules in `<div class="sb-book" role="list">`. Each rule is `<div class="sb-rule" role="listitem">` holding `sb-rule__n` (priority number), `sb-rule__code` (the line) and `sb-rule__state` (a word, or empty). Inside the code, the consumer wraps keywords in `.kw` and spell names in `.sp-harm`, `.sp-mend` or `.sp-guard` by what the spell does.

| State | Modifier | Meaning |
| --- | --- | --- |
| Not reached | (none) | A higher rule fired first. State cell empty. |
| Skipped | `sb-rule--skipped` | Evaluated, condition false. Code drops to `ink-muted`; state reads "false". |
| Fired | `sb-rule--fired` | This rule acted this moment. `gilt-soft` row, and the line number sits in a gilt tab (`<span class="sb-tab">2</span>`). State reads "fired". At most one per hero per moment. |
| Fizzled | `sb-rule--fizzled` | Cast without its built-in requirement met. `fizzle` colour, strikethrough, state reads "fizzled". |
| Error | `sb-rule--error` | The line cannot run. Wavy `harm` underline, state reads "error", and an `sb-rule__msg` under it says what to type to fix it. |

The empty state of a spellbook is a single row of `ink-muted` text: "No rules yet. Drag a sigil here or type a rule." Spellbooks are local, so there is no loading state.

- Do always print the state word. Colour alone never carries the state.
- Do write error messages as the fix, with the corrected line: "Line 5 needs a colon after the condition: if enemy.charging: taunt()".
- Do keep lines on one row; the book scrolls sideways rather than wrapping code.
- Don't reorder lines to show state; order is the player's strategy.
