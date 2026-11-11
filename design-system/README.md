# Spellborn design system

Spellborn is a party RPG autobattler where players write the rules their heroes follow, then watch those rules play out. Every screen serves one loop: read the encounter, write spellbooks, run the fight, study the replay, fix a line. The look is a grimoire you program: dark scriptorium panels, one gilt accent that always means "now", and three outcome colours that tell harm, healing and protection apart in the replay. Build every screen (spellbook editor, battle and replay, encounter briefing, title) from these tokens and components so the pages read as one game.

Direction dials for interface screens: ENERGY 2 / RHYTHM 2 / MOTION 1. Battle playback animation is the exception and is specified with the battle screen.

## Using it

| File | What it holds |
| --- | --- |
| `tokens.json` | Every token with its value per theme and a usage note. The reference for names, values and where each one is allowed. |
| `tokens.css` | The same tokens as CSS custom properties, plus one `sb-` class per type style (`sb-title`, `sb-body`, `sb-code`…). |
| `components.css` | The `sb-` component classes and the Google Fonts import. |
| `components/<Name>/README.md` | Markup, states and rules for one component. |
| `components/<Name>/preview.html` | A static specimen; open it in a browser. |

Load `tokens.css`, then `components.css`. Night is the default theme; set `data-theme="vellum"` on `<html>` for the light theme. When a value changes, change it in `tokens.json` and `tokens.css` together.

## Content fundamentals

Write like a patient tutor at the player's shoulder: plain, specific, second person ("your healer", "you wrote"). Sentence case everywhere except the `label` style on stat names. No exclamation marks, no emoji, no em dashes.

Use the game's own words, always the same way:

| Word | Means | Never say |
| --- | --- | --- |
| spellbook | One hero's full set of rules | script, program, loadout |
| rule / line | One priority entry in a spellbook | gambit, statement |
| sigil | A readable condition, spell or target block | node, card |
| fires | A rule's condition was true and it acted | triggers, executes |
| fizzles | A spell cast without its requirement met; the moment is lost | fails, misses |
| moment | One deterministic step of a fight, written `t14` | turn, tick, frame |
| focus | A hero's code budget | mana, MP |
| Run fight | Start the fight | Play, Start, Go |

Name the line when something goes wrong. Good: "Line 4 fizzled: fire_nova needs 3 burning enemies, there was 1." Bad: "Your spell failed."

Code in copy is set in `code` or `code-sm` and written exactly as the player would type it: `weakest_ally.hp_pct < 60`.

Sample content in the component previews (hero roles, HP values, enemy actions) is placeholder data. The class roster, spell list and balance values are not decided yet, so don't treat any of it as canon.

## Visual foundations

**Themes.** `night` is the first theme and the home of every in-game screen: players read code for long stretches and watch a lit battlefield, so a dark ground keeps the eye on the code and the action. `vellum` is the light theme for docs, the website and players who prefer light; ship a working toggle between them. Both themes share every token name, so a screen switches with `data-theme` alone.

**Colour roles.**
- Lay screens out as `ground` behind, `surface` panels on it, and `surface-sunk` wells inside panels for code, logs and meter tracks. Separate panels with a 1px `line` border, not a shadow. Controls a player clicks or drags (buttons, sigils, inputs) take the stronger `line-strong` border.
- Set text in `ink`; secondary text, line numbers and moment numbers in `ink-muted`.
- `gilt` is the single accent, and it always means "now" or "this one": the Run button, the gilt tab on the fired rule and the current replay moment, the selected hero and the focus ring. Nothing else. If two gilt things compete on one screen, one of them is wrong.
- Three outcome colours carry combat data only: `harm` (damage, burning, low HP, spellbook errors), `mend` (healing, the HP fill) and `guard` (shields, taunts, the focus fill). Colour spell names by what the spell does. Never use an outcome colour for chrome, headings or decoration.
- `fizzle` marks a fizzled spell and nothing else. Pair it with a strikethrough and the word "fizzled".
- Harm and healing differ in hue (orange against blue-green) and in sign: always print −34 or +22 with a true minus sign. No state is ever told by colour alone.

**The gilt tab.** The system's one motif: a small `gilt` block with `on-gilt` digits, `radius-sm`, holding a line number or a moment (`sb-tab`). It marks the rule that fired and the moment the replay is on, so the editor and the replay point at "now" the same way. Don't use it for anything else.

**Type.**
- `title` (Grenze 40px) once per screen, for the encounter or screen name. `heading` for panel titles and hero names. `impact` for the numbers that float over the battlefield. Grenze never sets paragraphs or UI labels.
- `body` for prose, `small` for metadata and rule state words, `label` (uppercase, 0.06em tracking) only for stat names beside meters.
- `code` for spellbook lines (24px rows), `code-sm` for logs and inline sigils. Mono only ever sets real code.
- Load the three families from Google Fonts: Grenze 400/600/700, Atkinson Hyperlegible 400/700 and italic 400, Atkinson Hyperlegible Mono 400/600. `components.css` imports them.

**Spacing and layout.** 4px base. `space-4` inside panels, `space-8` between screen regions, `space-6` per code row so the editor and replay keep the same rhythm. Use `font-variant-numeric: tabular-nums` wherever numbers stack (meters, logs, line numbers). Every button is at least 44px tall; sigils grow to 44px on touch screens.

**Radii.** Nearly square: `radius-sm` on sigils, badges, gilt tabs and inputs, `radius-md` on buttons, panels and cards, `radius-full` only on meters.

**Elevation.** Panels are flat with borders. `shadow-float` only for things that float above the board: tooltips, menus, a sigil being dragged.

**States.**
- Fired: `gilt-soft` row with the line number in a gilt tab. The current replay row matches.
- Skipped: text drops to `ink-muted` and the state reads "false".
- Fizzled: `fizzle` colour, strikethrough, the word "fizzled" and the reason.
- Error: wavy `harm` underline under the line, and a message that gives the corrected line.
- Selected: `gilt` border. Fallen or disabled: dashed border.
- Hover strengthens a control's border to `ink`; active sinks it 1px.
- Keyboard focus: the `focus-ring` shadow (2px ground-colour gap, 2px solid gilt) on every interactive element. It meets 3:1 on every surface in both themes.
- Every list of data has an empty state that says what will appear and how to make it appear, and an error state that names the line to fix.

**Motion.** Fights are deterministic; interface motion should feel like a clock, not a slot machine. Meter fills ease out over 0.3s and buttons sink 1px when pressed. No bounces, fades on load, screen shake or particles in the interface. Under `prefers-reduced-motion: reduce`, cut straight to the end state.

## Iconography

There is no logo or icon set yet. Set the name "Spellborn" in Grenze 700 wherever a wordmark is needed, and treat it as a placeholder until a mark is designed. Label everything with words: a sigil's kind ("when", "cast", "on") is a lowercase keyword, not a glyph, and buttons carry no icons or arrows. Choose an icon set only when a screen needs one that words can't serve, and add it here as its own asset group.

## Components

- **Button**: Run, secondary and ghost actions.
- **Sigil**: condition, spell and target blocks for drag-and-drop spellbooks.
- **SpellbookRule**: a numbered priority line with its fired, skipped, fizzled or error state.
- **Meter**: HP and focus bars with exact numbers.
- **HeroCard**: one hero's live state in a fight, including status badges.
- **ReplayLog**: the moment-by-moment record that links every action to its line, with empty, running and error states.
- **UnitTile**: one hero or enemy on the battlefield, with sprite, HP, status badges and the gilt tab while it acts.

Components are plain HTML with the `sb-` classes in `components.css`; each component's guidelines give the markup and say what the consumer supplies. There is no JavaScript.
