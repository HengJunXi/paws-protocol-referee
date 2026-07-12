# P.A.W.S. Protocol — Umpire

A mobile-first, single-page web app that acts as an umpire for the board game
**P.A.W.S. Protocol**. It resolves an attack between an attacker card and a defender card
**without ever revealing either card's identity** — it only reports whether each card *survives*
or is *eliminated*.

Supports both **1v1** and **2v2** via a mode toggle (2v2: each team has two of every card;
Lionheart can stack to +2, and Furor Tigris may hit two of the same card).

Everything is client-side — no build step, no server, and no history kept between resolutions.
Three preferences are remembered across visits via `localStorage`: the **1v1/2v2 mode**, a
**light/dark theme toggle** (defaults to your device's preference), and the **language**
(English and Simplified Chinese ship today, switchable via the 🌐 button; more languages can be
added as a pure data change).

Made a wrong pick? There's no in-app "back" once the attacker is locked in — refresh the page
(or pull down to refresh) to reset the resolution. Both hand-off screens show this as a hint.

## How it works

Pass-and-play on one shared device:

1. Choose the mode (**1v1** or **2v2**) and, as the **Attacker**, secretly pick your card, then
   hand the device to the Defender.
2. As the **Defender**, secretly pick your card, then hand the device back.
3. Reveal the result: each card is **SURVIVED** or **ELIMINATED** — the umpire never shows the
   cards themselves, only their fate.

The special skills (Furor Tigris, Lionheart) and the full resolution rules are covered below and
in the app's in-app **Rules** sheet.

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

## Deployment

Hosted on **GitHub Pages** — a static site with no build step, served from the repository root
on the `main` branch.

## Install on a phone (optional)

The page is an installable web app (`manifest.webmanifest` + `icons/`). Served over HTTPS,
open the URL on a phone and use **Add to Home Screen** — it launches fullscreen with the paw
icon, like a native app. Tap **Rules** in the top-right for the in-app rank & resolution
cheat-sheet, or **About** for credits and links to the original game.

## Credits & disclaimer

**P.A.W.S. Protocol** is a board game by **Atelier Rayfish**
([official page](https://www.rayfishgames.com/pawsprotocol) ·
[BoardGameGeek](https://boardgamegeek.com/boardgame/474041/paws-protocol)).

This project is an **unofficial, fan-made tool** and is **not associated with, endorsed by, or
affiliated with** P.A.W.S. Protocol or Atelier Rayfish. It exists only to assist play of the
original board game by acting as an umpire. Please support the creators and buy the game. The
box image (`images/paws-protocol-box.avif`) is © Atelier Rayfish and used only to reference the
original game.
