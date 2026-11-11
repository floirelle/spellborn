# Button

Buttons start, step and reset fights; the gilt Run button appears once per screen and always means "execute the spellbooks".

**Markup.** `<button class="sb-btn" type="button">` plus one modifier. The consumer provides the label, the click handler and, for Run, the keyboard hint in a `<kbd>`. Every button is at least 44px tall so it works as a tap target.

| Modifier | Use |
| --- | --- |
| `sb-btn--run` | The single primary action: Run fight, Retry. 48px tall, `gilt` fill, `on-gilt` label. |
| (none) | Secondary actions: Step one moment, Read enemy spellbook. `line-strong` border. |
| `sb-btn--ghost` | Low-stakes, undoable actions: Reset spellbook, Clear log. Underlined so it still reads as a control. |

States: hover strengthens the border to `ink`, active sinks 1px onto `surface-sunk`, disabled turns the border dashed and the label `ink-muted`, focus shows `focus-ring`. No state relies on colour alone.

- Do write labels as verbs naming the result: "Run fight", "Step one moment", "Reset spellbook".
- Do disable Run when a spellbook is over its focus budget, and say by how much in the label.
- Don't put two Run buttons on one screen, and don't use `gilt` for any other button.
- Don't add arrows or icons to labels; the verb carries the meaning.
