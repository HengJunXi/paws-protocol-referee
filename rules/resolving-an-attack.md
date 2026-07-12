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

- The game ends immediately when the **General Lion** is eliminated.
- The player who eliminates the opposing **General Lion** wins!

---

## How the referee app applies these rules

The referee (`index.html`) resolves an attack of **attacker → defender** and reports only
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

**Lionheart (+1 rank):** each side has an optional toggle that adds +1 to its own card's rank.
This only changes the **rank comparison** rows above — every special case stays rank-independent.
So attacker Lion still beats defender Lion, and a buffed Tiger (6+1) ties an unbuffed Lion (7),
eliminating each other.
