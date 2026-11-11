# HeroCard

One party member's live state during a fight or replay: name, status, HP and focus meters, and the rule they are acting on this moment.

**Markup.** `<article class="sb-hero">` containing `sb-hero__head` (an `sb-hero__name` heading plus `sb-hero__class` text or an `sb-badge`), two `sb-meter`s, and `sb-hero__now`. The consumer provides the hero's data and the "now" line in the form `line N: <b>spell</b>(target)`.

| Modifier | Use |
| --- | --- |
| `sb-hero--selected` | The hero whose spellbook is open. `gilt` border. |
| `sb-hero--down` | Fallen. Dashed `line-strong` border, muted name, status line reads "Fallen at t11". |

Status effects use `sb-badge` coloured by outcome (`--harm` for burning and other damage over time, `--guard` for shields) and always name the effect and the moments left.

Hero names and classes in the preview are placeholders; the class roster is not decided yet. An empty party slot is an `sb-hero` with a dashed border and the text "Empty slot: choose a hero".

- Do lay cards out in a grid with `space-4` gaps, one card per hero, in party order.
- Don't show portraits until character art exists.
