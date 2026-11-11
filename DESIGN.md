# Spellborn design direction

The full design system (tokens, brand book, component markup and previews) lives in `design-system/`.

Read `design-system/README.md` first, then `design-system/tokens.json` and `design-system/components.css`. That folder is the source of truth for values; this file records the direction and the reason behind each decision.

## Design read

Reading this as: game UI plus player-facing docs for people who enjoy programming puzzles and party tactics, in a "grimoire you program" visual language, dial **ENERGY 2 / RHYTHM 2 / MOTION 1** for interface screens. Battle playback animation is specified separately with the battle screen.

## Identity

- **Personality:** a patient tutor at your shoulder. Precise, readable, a little arcane, never flashy.
- **Motif:** the gilt tab, a small gold block holding a line number or moment, meaning "this one, now". It is the same in the editor and the replay.
- **Focal point per screen:** the thing the gilt marks (the Run button before a fight, the fired line during a replay).

## Decisions and why

| Decision | Reason |
| --- | --- |
| Dark `night` theme first, light `vellum` theme shipped with a toggle | Players read code for long stretches beside a lit battlefield; light stays available for docs and preference. |
| Ground, surface, sunk-surface neutrals with a slight teal bias | Wells for code and logs need to read as set into the page without shadows. |
| One accent, `gilt`, only for "now" | The game is about knowing which rule acted; one accent keeps that answer instant. |
| Three outcome colours: `harm`, `mend`, `guard` | The replay must tell damage, healing and protection apart at a glance; they are data, not decoration, and never colour chrome. |
| `fizzle` grey-violet plus strikethrough and the word | Fizzling is the game's signature failure and needs its own unmistakable form. |
| Grenze for titles and names | Roman letters on blackletter bones read as a grimoire while staying legible. |
| Atkinson Hyperlegible for text | Built so similar letters never blur, which serves "failures are readable". |
| Atkinson Hyperlegible Mono for code | Same letterforms as the text face, so sigils, code and prose read as one language. |
| 4px spacing with 24px code rows | Editor and replay share one vertical rhythm. |
| Nearly square radii (2px, 6px); round only on meters | Tablets and pages, not app bubbles; a round fill reads as liquid. |
| Borders, not shadows, on panels | Shadows are reserved for things that float: tooltips, menus, a dragged sigil. |
| Words instead of icons; no logo yet | No icon set or mark has been chosen; the wordmark is Grenze text as a placeholder. |
| 44px minimum buttons; sigils 44px on touch | Tap targets must work on phones. |
| Empty, running and error states on every data list | A loss or an error must always point at a line the player can fix. |

## Placeholders

Hero roles, HP values and enemy actions in the previews are sample data. The class roster, spell list and balance values are not decided, so screens must not present them as canon.
