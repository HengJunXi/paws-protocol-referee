# Protocol Directives — Resolving an Attack

The **Defender** will resolve and determine the outcome of the battle according to the
following rules:

- **Higher-ranked** cards eliminate **lower-ranked** cards.
- **Equal-ranked** cards eliminate each other.

## Exceptions

### Lion vs Lion
Attacker always wins, regardless of rank.

### Rat vs Lion / Mine / Missile
Rat always wins.

### Mine
- Always **loses** when attacking.
- Always **wins** when being attacked (except vs Rat).
- Always **eliminated** after resolving an attack.

### Missile
- Always **wins** when attacking (except vs Rat).
- Always **loses** when being attacked.
- Always **eliminated** after resolving an attack (except when being attacked by a Mine).

## Winning the Game

- **1v1:** the game ends immediately when a **General Lion** is eliminated; whoever eliminates
  the opposing General Lion wins.
- **2v2:** each team has two General Lions. A team loses only when **both** of its Lions are
  eliminated — losing just one keeps the game going.

---

## How the umpire app applies these rules

The umpire (`index.html`) resolves an attack of **attacker → defender** and reports only
whether each card *survives* or is *eliminated* (never the identity). Micro Mine may attack,
but it always loses (it self-destructs when attacking).

| Situation | Attacker | Defender |
|-----------|----------|----------|
| Higher rank vs lower rank | survives | eliminated |
| Lower rank vs higher rank | eliminated | survives |
| Equal rank (same animal) | eliminated | eliminated |
| Lion vs Lion | survives | eliminated |
| Rat → Lion / Mine / Missile | survives | eliminated |
| Lion / Mine / Missile → Rat | eliminated | survives |
| Missile → any animal (non-Rat) | eliminated | eliminated |
| Missile → Missile | eliminated | eliminated |
| Missile → Mine | eliminated | eliminated |
| Any animal (non-Rat) → Mine | eliminated | eliminated |
| Any animal → Missile | survives | eliminated |
| Mine → Mine | eliminated | eliminated |
| Mine → anything except Mine (incl. Missile) | eliminated | survives |

**Lionheart:** each side has an optional level that adds to its own card's rank — up to **+1**
in 1v1 and **+2** in 2v2 (two Lions stacking). This only changes the **rank comparison** rows
above — every special case stays rank-independent. So attacker Lion still beats defender Lion,
and a buffed Tiger (6+1) ties an unbuffed Lion (7), eliminating each other.

**2v2 targeting:** Furor Tigris may strike two of the **same** card (each team holds two copies);
in 1v1 the two targets must differ. Every matchup is still resolved independently.
