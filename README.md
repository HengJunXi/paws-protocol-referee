# P.A.W.S. Protocol — Referee

A dead-simple, mobile-first, single-page web app that acts as a referee for the 1v1 board game
**P.A.W.S. Protocol**. It resolves an attack between an attacker card and a defender card
**without ever revealing either card's identity** — it only reports whether each card *survives*
or is *eliminated*.

Everything is client-side — no build step, no server, no state kept between resolutions.

## How it works

1. **Attacker** picks their card and confirms (8 options — Micro Mine cannot attack).
   Choosing Colonel Tiger offers **Furor Tigris** — attack two different targets.
2. The pick is hidden; hand the device to the **Defender**.
3. **Defender** selects their card — or, under Furor Tigris, two different targets — and confirms
   (all 9 options available).
4. A hand-off screen prompts the defender to show the device to the attacker, then reveals the
   result: each card is `SURVIVED` or `ELIMINATED` (both can be eliminated).

No card names are ever displayed in the resolution — only outcomes.

## Cards & ranks

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

## Resolution rules implemented

- Higher-ranked card eliminates lower-ranked; equal rank → both eliminated.
- **Lion vs Lion** → attacker always wins.
- **Rat** vs Lion / Mine / Missile → Rat always wins (either direction).
- **Missile attacking** (except vs Rat) → target eliminated **and** Missile self-destructs → both eliminated.
- **Missile being attacked** → Missile always loses.
- **Mine being attacked** (except vs Rat) → Mine wins but is eliminated after → both eliminated.

> Unique Skills (Lionheart, Grizzly Guard, etc.) are **out of scope** — players apply those
> manually. The referee resolves raw card-vs-card combat only.

## Run locally

Just open `index.html` in any browser. No dependencies.

## Install on a phone (optional)

The page is an installable web app (`manifest.webmanifest` + `icons/`). Served over HTTPS,
open the URL on a phone and use **Add to Home Screen** — it launches fullscreen with the paw
icon, like a native app. Tap **Rules** in the top-right for the in-app rank & resolution
cheat-sheet.
