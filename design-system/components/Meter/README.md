# Meter

A horizontal bar for HP or focus, always with its label and exact numbers beside it.

**Markup.** `<div class="sb-meter" role="meter">` containing `sb-meter__label`, `sb-meter__track` > `sb-meter__fill` (width as a percentage) and `sb-meter__value`. The consumer provides the current and maximum values and the `aria-value*` attributes.

| Modifier | Use |
| --- | --- |
| (none) | HP. `mend` fill. |
| `sb-meter--focus` | Focus, the spellbook's code budget. `guard` fill. |
| `sb-meter--low` | HP below 30%. Fill and number turn `harm`. |

Before a fight has data (for example, a hero with no class chosen yet), show the track empty and the value as "–".

- Do show "current / max" in code type; players compare these numbers against conditions like `hp_pct < 60`.
- Do animate width changes only under `prefers-reduced-motion: no-preference`.
- Don't add segment ticks or gradients; the number is the exact reading, the bar is the glance.
