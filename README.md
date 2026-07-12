# P.A.W.S. Protocol — Umpire

A dead-simple, mobile-first, single-page web app that acts as an umpire for the board game
**P.A.W.S. Protocol**. It resolves an attack between an attacker card and a defender card
**without ever revealing either card's identity** — it only reports whether each card *survives*
or is *eliminated*.

Supports both **1v1** and **2v2** via a mode toggle (2v2: each team has two of every card;
Lionheart can stack to +2, and Furor Tigris may hit two of the same card).

Everything is client-side — no build step, no server, no state kept between resolutions.

## How it works

0. On the attacker screen, choose **1v1** or **2v2** (a session setting).
1. **Attacker** picks their card and confirms (all 9 options — Micro Mine may attack, but it
   always loses). Choosing Colonel Tiger offers **Furor Tigris** — attack two targets.
   A **🦁 Lionheart** control adds +1 (or +2 in 2v2) rank to the attacker's unit(s).
2. The pick is hidden; hand the device to the **Defender**.
3. **Defender** selects their card — or, under Furor Tigris, two targets (which may be the same
   card in 2v2) — and confirms. The defender has their own **🦁 Lionheart** control.
4. A hand-off screen prompts the defender to show the device to the attacker, then reveals the
   result: each card is `SURVIVED` or `ELIMINATED` (both can be eliminated). If either side used
   Lionheart, the result also shows which side(s) had it active and at what level.

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
- **Missile being attacked** → Missile always loses (but *survives* when attacked by a Mine).
- **Mine being attacked** (except vs Rat) → Mine wins but is eliminated after → both eliminated.
- **Mine attacking** → Mine always self-destructs and the defender survives; **Mine vs Mine** → both eliminated.
- **Lionheart** (per-side, +1 or up to +2 in 2v2) → adds that many ranks to the side's unit(s)
  in the rank comparison only. Special cases stay rank-independent, so attacker-Lion still beats
  defender-Lion, and a buffed Tiger (6+1) can tie an unbuffed Lion (7) → both eliminated.
- **2v2** → Lionheart stacks to +2 (two Lions) and Furor Tigris may target two of the same card.
  Each matchup resolves the same way; the win condition (both team Lions down) is tracked by the
  players, not the app.

> Two Unique Skills are handled — **Furor Tigris** and **Lionheart**. Other skills (Grizzly
> Guard, Tusk Rampage, etc.) are out of scope; players apply those manually.

## Run locally

Just open `index.html` in any browser. No dependencies.

## Install on a phone (optional)

The page is an installable web app (`manifest.webmanifest` + `icons/`). Served over HTTPS,
open the URL on a phone and use **Add to Home Screen** — it launches fullscreen with the paw
icon, like a native app. Tap **Rules** in the top-right for the in-app rank & resolution
cheat-sheet.
