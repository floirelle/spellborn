# Sigil

A sigil is one draggable piece of a rule in drag-and-drop mode: a condition, a spell or a target, written in the same words the code uses.

**Markup.** `<button class="sb-sigil" type="button">` (a button, so it is focusable and can be picked up with the keyboard) containing an optional `<span class="sb-sigil__kw">` keyword (when, cast, on) and the sigil's text. The consumer provides the text exactly as it appears in code, plus the drag and keyboard-move behaviour.

| Kind | Modifier | Look |
| --- | --- | --- |
| Condition | `sb-sigil--when` | Dashed `line-strong` border: a gate, not an action. |
| Spell | `sb-sigil--harm`, `--mend`, `--guard`, or none | Text in the outcome colour of what the spell does; neutral spells such as `attack` stay `ink`. |
| Target | (none) | Solid `line-strong` border, `ink` text. |

Sigils are 32px tall with a mouse and 44px on touch screens (`pointer: coarse`). Hover strengthens the border, dragging lifts the sigil with `shadow-float` and the grabbing cursor.

- Do keep sigil text in code form (`weakest_ally.hp_pct < 60`). Players graduate from sigils to code, so the words must not change.
- Do write keywords in lowercase `keyword` colour, the same as in code, so a row of sigils reads as a sentence.
- Don't colour a condition; only spells carry an outcome colour.
