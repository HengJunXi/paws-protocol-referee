# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

A **single-page, mobile-first web umpire** for the board game **P.A.W.S. Protocol**.
It resolves an attack between an attacker card and a defender card **without revealing either
card's identity** — it only reports whether each card *survives* or is *eliminated*.

Supports both **1v1** (2 players, one set of 9 cards each) and **2v2** (4 players, each team
has 2 copies of every card = 18 cards) via a mode toggle. The umpire still resolves one
matchup at a time by card *type* and does not track the win condition (that stays with the
players); 2v2 only changes two skills — Lionheart can stack to +2, and Furor Tigris may hit
two of the same card.

Everything is client-side and self-contained. There is no build step, no server, no
dependencies, and no state persisted between resolutions (each attack is fully independent).

## Layout

- `index.html` — the app: HTML + inline CSS + inline JS. This is the deliverable.
- `manifest.webmanifest` — PWA manifest so the page can be installed / "Add to Home Screen".
- `icons/` — app icons (`icon-512.png`, `icon-192.png`, `icon-180.png`): gold paw print on the dark bg.
- `images/paws-protocol-box.avif` — box art for the About page (credit to Atelier Rayfish).
- `rules/resolving-an-attack.md` — transcription of the "Resolving an Attack" rules card.
- `rules/unique-skills.md` — transcription of the "Unique Skills" rules card.
- `README.md` — project overview and usage.

## The game, in brief

9 card types. 7 ranked characters + 2 unranked weaponry cards (in 2v2 each team holds two of
every type, 18 cards total; the umpire still works per type):

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
2. **Micro Mine attacks but always loses.** When attacking, the Mine self-destructs and the
   defender survives — except **Mine vs Mine**, where both are eliminated. `ATTACKER_IDS` and
   `DEFENDER_IDS` are now identical (all 9 cards). Note the Missile exception: a Missile
   attacked by a Mine *survives* (the Mine is eliminated).
3. **Raw resolution, with two supported skills.** `resolve(a, d, aBuff, dBuff)` implements raw
   card-vs-card combat. Two Unique Skills are wired into the UI:
   - **Furor Tigris** — Colonel Tiger attacks two different targets; each is resolved
     independently via `resolve()`, and Tiger is eliminated if it loses either matchup.
   - **Lionheart** — a per-side rank bonus (`attackerBuff` / `defenderBuff`, integers 0..`maxStacks()`)
     set by a segmented control on the attacker and defender screens. Max is +1 in 1v1 and +2 in
     2v2 (two Lions stacking). The bonus applies **only** in the rank-comparison branch of
     `resolve(a, d, aBuff, dBuff)`; every special case (Rat, Missile, Mine, Lion vs Lion) stays
     rank-independent, so attacker-Lion still beats defender-Lion regardless of buffs, and a
     buffed Tiger (6+1) can tie an unbuffed Lion (7) → both eliminated. Changing the level
     re-renders that side's list so ranks show the buffed number plus one accent `.buff-star`
     per +1; the result screen surfaces each side's level via the Lionheart chips (see Flow).
     Rank display and the modals share one helper, `rankHtml(c, buff)`, so nothing drifts.
   - **2v2 targeting** — in 2v2 Furor Tigris (`allowDupeTargets()`) the two targets may be the
     same card; `toggleDefender` allows a card to be picked up to twice. 1v1 keeps distinct
     targets. In Furor, a selected card's badge is a **✕ remove-one** control (`removeOneDefender`,
     `stopPropagation` so it doesn't also add) — needed because tapping a selected card adds a
     duplicate in 2v2, so there'd otherwise be no way to cancel a single target. Each matchup
     still resolves independently via `resolve()`.

   All other skills (Grizzly Guard, Tusk Rampage, etc.) remain out of scope — players apply
   them manually. Keep `resolve()` free of other skill effects.
4. **No persistence / no history.** Keep it stateless across resolutions and page loads.
5. **No third-party dependencies.** All CSS/JS is inline in `index.html`; the only local asset
   references are same-repo files (`manifest.webmanifest`, `icons/`, `images/`). No CDNs, web
   fonts, analytics, or runtime network calls. The only outbound links are the two external
   `<a href>`s on the About page (Atelier Rayfish, BGG), which open in a new tab. It runs on the
   GitHub Pages free tier as-is. (No service worker, so it is not offline-cached.)

## Flow

The attacker screen has a **1v1 / 2v2** mode toggle (`#mode-seg`, `setMode`); `mode` persists
across resolutions. The defender screen shows the locked mode as a read-only chip
(`#defender-mode-chip`) and, when the attacker used Lionheart, a banner with the attacker's
`+N` level (`#atk-buff-banner`) — this is fine to reveal because Lionheart is announced in-game
(it does not expose the attacker's card). Both the attacker and defender screens carry a
**🦁 Lionheart** segmented control (Off / +1 / +2, capped by `maxStacks()`) that sets that
side's buff before picking.

Attacker picks (9 options) → **confirmation modal** (shows the picked character, plus a
Lionheart note if active; picking Colonel Tiger offers "Single attack" or "Furor Tigris —
2 targets") → attacker locked, hand
device to Defender (no going back to change the attacker once passed) → Defender **selects**
their card(s): tap to select/deselect, single mode needs 1, Furor Tigris needs 2 different
targets (`defenderPicks` holds them in order). There is no confirm button — the **confirmation
modal opens automatically** once the required count is selected (single: attacker-style big
card; Furor: a list of both targets). Cancel pops the triggering pick and returns to selection.
Confirm → "show the device to the Attacker" gate with a **Show Result** button → result.

Two pill buttons in a right-aligned row above the header (`.topbar` / `.pill-btn`, in normal
flow so they don't overlap the title on small screens) open full-screen overlays at any time,
both using the shared `.rules-overlay` / `.rules-sheet` styles and the `no-scroll` lock:
- **Rules** (`#rules-overlay`) — the cheat-sheet; its ranks list is generated from `CARDS` via
  `buildRulesRanks()` so it can't drift from the resolution logic.
- **About** (`#about-overlay`) — credits and links to the original game (Atelier Rayfish, BGG),
  the box image, and a disclaimer that this is an unofficial fan-made umpire tool.

Result screens:
- Single: two outcome cards (Attacker's / Defender's), both-eliminated possible.
- Furor Tigris: Colonel Tiger's aggregate outcome (full-width) plus Target 1 / Target 2.
- Lionheart chips: each outcome card shows a `🦁 Lionheart +1` / `No Lionheart` chip, but
  only when at least one side used it (`showBuff = attackerBuff || defenderBuff`).

Screen IDs: `screen-attacker`, `screen-pass`, `screen-defender`, `screen-show-result`,
`screen-result`. State reset each resolution: `attackerId`, `furor`, `defenderPicks[]`,
`attackerBuff`, `defenderBuff`. `mode` (`'1v1'`/`'2v2'`) is a session setting and is **not**
reset per resolution.

## Conventions

- Vanilla HTML/CSS/JS only — no frameworks, no bundler.
- Mobile-first; keep the UI simple. Match the existing dark theme and CSS-variable palette.
- One typeface: the system font stack on `body`; `button { font-family: inherit }` keeps
  controls on it. "Weaponry" is title-case everywhere. Rank shows as `N · ★…` on the
  pick/confirm screens (via `rankHtml`) and `★… · N` on the rules sheet; a Lionheart buff adds
  +1 to the number and a highlighted accent `.buff-star`.
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
- Mine → Lion (or any non-Mine): Mine eliminated, defender survives.
- Mine → Mine: both eliminated.
- Mine → Missile: Mine eliminated, Missile survives (Missile's Mine exception).
- Buffed Tiger (attacker Lionheart) → unbuffed Lion: both eliminated (6+1 ties 7).
- Attacker Lion → defender Lion with defender Lionheart on: attacker still survives (special).
- 2v2: attacker Lionheart +2 makes Tiger (6+2=8) beat unbuffed Lion (7).
- 2v2 Furor Tigris → Tiger + Tiger (same card twice, `×2` badge): resolves both targets.
- Furor Tigris → Cat + Mine: Tiger eliminated (loses to Mine); Target 1 (Cat) eliminated,
  Target 2 (Mine) eliminated. Second target list must exclude the first pick.

## Deployment

Static hosting from the repo root (e.g. GitHub Pages) — it's just static files, no build step.
