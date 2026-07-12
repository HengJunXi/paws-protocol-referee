# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

A **single-page, mobile-first web referee** for the 1v1 board game **P.A.W.S. Protocol**.
It resolves an attack between an attacker card and a defender card **without revealing either
card's identity** — it only reports whether each card *survives* or is *eliminated*.

Everything is client-side and self-contained. There is no build step, no server, no
dependencies, and no state persisted between resolutions (each attack is fully independent).

## Layout

- `index.html` — the app: HTML + inline CSS + inline JS. This is the deliverable.
- `manifest.webmanifest` — PWA manifest so the page can be installed / "Add to Home Screen".
- `icons/` — app icons (`icon-512.png`, `icon-192.png`, `icon-180.png`): gold paw print on the dark bg.
- `rules/resolving-an-attack.md` — transcription of the "Resolving an Attack" rules card.
- `rules/unique-skills.md` — transcription of the "Unique Skills" rules card.
- `README.md` — project overview and usage.

## The game, in brief

9 cards. 7 ranked characters + 2 unranked weaponry cards:

| Card | Emoji | Rank |
|------|-------|------|
| General Lion | 🦁 | 7 |
| Colonel Tiger | 🐯 | 6 |
| Major Bear | 🐻 | 5 |
| Captain Boar | 🐗 | 4 |
| Lieutenant Wolf | 🐺 | 3 |
| Sergeant Cat | 🐱 | 2 |
| Commando Rat | 🐀 | 1 |
| Mini Missile | 🚀 | weaponry |
| Micro Mine | 💣 | weaponry |

Resolution rules and the full outcome table live in `rules/resolving-an-attack.md`.
The authoritative implementation is the `resolve(a, d)` function in `index.html`.

## Core invariants — do not break these

1. **Never reveal card identities in the resolution.** The result screen shows only
   `SURVIVED` / `ELIMINATED` per side. No names, emojis, or ranks of the chosen cards may
   appear on the pass/show-result or result screens. This privacy guarantee is the whole point.
   The one deliberate exception: **Furor Tigris** names Colonel Tiger as the attacker, because
   that skill is announced in the game — the two targets stay anonymous.
2. **Micro Mine cannot be an attacker** (it always loses when attacking). It is absent from
   `ATTACKER_IDS` but present in `DEFENDER_IDS`.
3. **Raw resolution only, with one supported skill.** `resolve()` implements raw card-vs-card
   combat and must stay that way. The **only** Unique Skill wired into the UI is Furor Tigris
   (Colonel Tiger attacks two different targets; each is resolved independently via `resolve()`,
   and Tiger is eliminated if it loses either matchup). All other skills (Lionheart, Grizzly
   Guard, etc.) remain out of scope — players apply them manually. Do not fold other skill
   effects into `resolve()`.
4. **No persistence / no history.** Keep it stateless across resolutions and page loads.
5. **No third-party dependencies.** All CSS/JS is inline in `index.html`; the only external
   references are same-repo assets (`manifest.webmanifest`, the `icons/` files). No CDNs,
   web fonts, analytics, or network calls — it runs on the GitHub Pages free tier as-is.
   (There is no service worker, so it is not offline-cached; it just loads over the network once.)

## Flow

Attacker picks (8 options) → **confirmation modal** (shows the picked character; picking
Colonel Tiger offers "Single attack" or "Furor Tigris — 2 targets") → attacker locked, hand
device to Defender (no going back to change the attacker once passed) → Defender **selects**
their card(s): tap to select/deselect, single mode needs 1, Furor Tigris needs 2 different
targets (`defenderPicks` holds them in order). There is no confirm button — the **confirmation
modal opens automatically** once the required count is selected (single: attacker-style big
card; Furor: a list of both targets). Cancel pops the triggering pick and returns to selection.
Confirm → "show the device to the Attacker" gate with a **Show Result** button → result.

A **Rules** button (top-right, `.rules-btn`) opens a full-screen cheat-sheet overlay
(`#rules-overlay`) at any time; the ranks list is generated from `CARDS` via `buildRulesRanks()`
so it can't drift from the resolution logic.

Result screens:
- Single: two outcome cards (Attacker's / Defender's), both-eliminated possible.
- Furor Tigris: Colonel Tiger's aggregate outcome (full-width) plus Target 1 / Target 2.

Screen IDs: `screen-attacker`, `screen-pass`, `screen-defender`, `screen-show-result`,
`screen-result`. State: `attackerId`, `furor`, `defenderPicks[]` (reset each resolution).

## Conventions

- Vanilla HTML/CSS/JS only — no frameworks, no bundler.
- Mobile-first; keep the UI simple. Match the existing dark theme and CSS-variable palette.
- One typeface: the system font stack on `body`; `button { font-family: inherit }` keeps
  controls on it. "Weaponry" is title-case everywhere. Rank shows as `N · ★…` on the
  pick/confirm screens and `★… · N` on the rules sheet.
- If you change resolution logic, update the outcome table in `rules/resolving-an-attack.md`
  to match `resolve()`.

## Testing

Open `index.html` in a browser. Sanity checks:
- Rat → Lion: Rat survives, Lion eliminated.
- Missile → any animal: both eliminated.
- Any animal → Mine: both eliminated.
- Any animal → Missile: attacker survives, Missile eliminated.
- Cat → Cat: both eliminated (equal rank).
- Tiger → Cat: attacker survives, defender eliminated.
- Furor Tigris → Cat + Mine: Tiger eliminated (loses to Mine); Target 1 (Cat) eliminated,
  Target 2 (Mine) eliminated. Second target list must exclude the first pick.

## Deployment

Static hosting from the repo root (e.g. GitHub Pages) — it's just static files, no build step.
