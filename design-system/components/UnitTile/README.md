# UnitTile

One hero or enemy on the battlefield: sprite, name, HP meter, status badges, and the gilt tab while it acts. It is a button, because selecting a unit opens its spells and spellbook.

**Markup.** `<button class="sb-unit" type="button" aria-pressed>` containing `sb-unit__sprite`, an optional `sb-unit__impact`, and `sb-unit__info` with `sb-unit__top` (`sb-unit__name` plus an `sb-tab` while acting), an `sb-meter` and `sb-unit__badges`. The consumer provides:
- the sprite sheet, as `background-image` on `.sb-unit__sprite` (sheets are 16px cells drawn at whole-number scales; set `--cols` if the sheet is not 5 cells wide), and the cell index as `--x`;
- an `aria-label` that says the unit's name, HP, and "fallen" or "acting now" when true;
- the click behaviour (select the unit).

| Modifier | Use |
| --- | --- |
| `sb-unit--selected` | The unit whose spells and spellbook are open. `gilt` border. |
| `sb-unit--down` | Fallen. Dashed border, grey sprite, muted name, plus a "Fallen" `sb-badge--plain`. |
| `sb-unit--compact` | 40px sprite for phones and the replay strip. |

Impacts (`sb-unit__impact--harm`, `--mend`, `--guard`, `--fizzle`) show the last effect on the unit at the end of an action: `−34`, `+22`, `shield`, `fizzled`. They rise once under `prefers-reduced-motion: no-preference` and sit still otherwise.

- Do put the gilt tab on the acting unit with the line being checked: `line 2`.
- Do list a column of tiles per side, in party or enemy order; the list grows with party size.
- Don't add portraits, frames or glows; the sprite and the border carry the state.
