# Terminal Game Rules and Documentation

Welcome to the Citadel Terminal competition documentation. This guide details gameplay mechanics, units, structures, pathing logic, and requirements.

## Table of Contents
1. [Special Competition Rule Changes](#special-competition-rule-changes)
2. [Gameplay Overview](#gameplay-overview)
3. [Units: Attackers (Mobile)](#units-attackers-mobile)
4. [Units: Defenders (Structures)](#units-defenders-structures)
5. [Map & Game Start](#map--game-start)
6. [Turn Structure](#turn-structure)
7. [Advanced Mechanics](#advanced-mechanics)
8. [Game Requirements & Dev Guide](#game-requirements--dev-guide)
9. [Troubleshooting](#troubleshooting)
10. [Glossary](#glossary)

---

## Special Competition Rule Changes
You are in a special competition that overrides the base game rules. Unranked "Playground" and "My Algos" pages only feature these rules.
Key differences from the base game:

| Unit | Before | After |
| --- | --- | --- |
| **Wall** | Health: 60, Cost: 1, Upg Cost: 0 | Health: 50, Cost: 2, Upg Cost: 1 |
| **Support** | Shield Range: 3.5, Cost: 4, Health: 30, Upg Shield/Unit: 4, Upg Range: 7 | Shield Range: 6.0, Cost: 4, Health: 20, Upg Shield/Unit: 8, Upg Range: 12 |
| **Turret** | Cost: 2, Start Health: 75, Damage: 5, Upg Dmg: 16, Upg Range: 3.5, Upg Cost: 4, Upg Health: 75 | Cost: 3, Start Health: 70, Damage: 6, Upg Dmg: 15, Upg Range: 3.5, Upg Cost: 8, Upg Health: 70 |
| **Scout** | Range: 3.5, Health: 15 | Range: 3.5, Health: 15 |
| **Demolisher** | Breach Dmg: 1.0, Speed: 0.5, Cost: 3 | Breach Dmg: 1.0, Speed: 1.0, Cost: 4 |
| **Interceptor** | Health: 40, Cost: 1, Range: 4.5, Self Destruct Dmg: 40, Speed: 0.25 | Health: 60, Cost: 3, Range: 5.0, Self Destruct Dmg: 45, Speed: 0.25 |

---

## Gameplay Overview
Terminal is a two-player, simultaneous-turns tower defense game on a diamond-shaped arena. You occupy the bottom half; the opponent occupies the top half. Objective: Reduce opponent health to zero by advancing Mobile units to their edge, while building Structures to protect your own edges.

![Terminal Example Round](https://ai-games.s3-us-west-2.amazonaws.com/assets/terminal-example-gameplay.gif)

Both players use two resources:
- **Mobile points**: Used to spawn mobile attacking units.
- **Structure points**: Used to build defensive structures.

| Feature | Mobile Unit | Structure |
| --- | --- | --- |
| **Deployment** | From either of your two arena edges | On any square in your half of the arena |
| **Movement** | Moves to opposite arena edge | Stationary (does not move) |
| **Targeting** | Targets all enemy units | Only attacks enemy Mobile units |
| **Stacking** | Multiple can stack in one location | Only one per location |
| **Blocking** | Does not block movement | Blocks movement (creates paths) |

---

## Units: Attackers (Mobile)
Mobile units are deployed from your edges and aim for the opposite edge in enemy territory. If they reach the edge, they decrease the opponent's Health by 1 point and award you 1 Structure point, then vanish.

| Name | Image | Cost (Mobile) | Health | Range | Damage | Speed | Role |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Scout** | ![Scout](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAYAAAEhyb7BAAAAAXNSR0IArs4c6QAAAR1JREFUOBGdk+EVwjAIhI2/3ED3cIa6hW6hY6or6AJuYP2Ohko0aX3yXgq5OwKl6WKB9X2/k1fQsWQXB4bAdjySON+UPjLLSEGs476MYU+eWj8d9uGKMRegKBIJiQ8jEAMIKyZFNh29GjVsDs58+Bv7siTAKotu4wm1ANEw4BopDIGVbfGG51Jypy8hoHqxQUmR7S0EuDja8J36WLLuDUE5VESbIHx89eQAIg1P9p60k+4hVbq8bpDJBTWvJPAzS7dym1J61nSTmNpixZdWu+1Wa6eRoGF8zh/IsE0txzAEem99fh8S4axJq5zhRhLsWFfWv6bc8pICaC76YWqvBWwmTprf5pXFlhkex+Z8pggOiH/rfko7y3GYZtjNCV8IUxz8JN2SMAAAAABJRU5ErkJggg==) | 1 | 15 | 3.5 | 2 | 1 | Fast, light damage, scores easily |
| **Demolisher** | ![Demolisher](data:image/png;base64,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) | 4 | 4 | 4.5 | 8 | 1 | High dmg & range, easy to kill |
| **Interceptor**| ![Interceptor](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACYAAAAmCAYAAAHfOtk4AAAAAXNSR0IArs4c6QAABMRJREFUWAm1l2uoFkUYx99Xj+CFzKDjBUO0IE6YIEVFyVHrW2YEImYgXvCTHRMiEL9oIQmW9UUSEQrpAmH2Ua1AoguEFzRRLEHipImmiOUXNfW4/f7z7szO7s7umfOecx74v/PM//k/z8zOO7uz20iSpKvRlpHZNyKQmXEo7oEkIGo04Kfq51owWiJR7gyRjxI4DfaB8eAZJ6JzDVy3BP5OIFNSqxqOE1ihbYmdlm8uqNls3m8DgbbXcWSFLxgFofESjsCZSrtLnQp7rK5QPkdKbFmrSVYpij827R+k3STikXxaviexYWin5UNZj9g8KwpeJYKxEmSbIEv2vSWuQ8ZG12nHocAdLehVJdPOA7K3zW+S7E6LnldQZiYYGonYMcMbWevnO5radfMLoV0KrrZSs/X8FmJZQTgNTpvWN90tm0CzoBWfzVoZviDWJ20+MHva5UCsAvscEemQE55EZaCiMHrdluU9AKkd3FeRV0mTU56ZSCy3sJUVvAA5u8F6R9F5DRx0hOfAjwG6nJUenXOJZbNTB9sI+oyXJL/Rzs9leB1i68GdVPsH7XvgmHb2bE/XtkudbhX7C8hugXWx1dA+AD4H1mbpOdADDoGHwLM2Qvs1mAznjP5CcA5ojS6D4zx3zZ9Gc8oI06BLkgO3BtxQzLPD+E/6Qvqd4KbjUvFIRxQc4nWxr4jr6lpG5wOw1fYH0pKXbQsl0h9VIiMrBvOCZD8FyekBe0oyyKNgQSlQQ6C/CTpLEsinwJ+lQA2BPrde/nnzNHmHa3LjQwyiKU+MzzB/XHhpilOOKUqOlqY3p4WYBS7myMgOebl10z77GSyKzM/JyLsNJjiSTr66i5h1eYnwXjDJo50L/xb41BA4OtJdMdwOoIedNd3crwN701/GX+yq4Uhoi23B3w8OiUxtG22Hn2B9+Jngh1SnZrt+bDH558HLNiG2JUdXtRnoRF+tKc4GsgtgF3gFjIstOFgdY2lCeii/C34Fsg2mLo4mJDsOfgH2kBInOwM+BC+AUe1MhrzHwQbwE7gLfDtL50BKnHP1IcYBnU2a0IMugEN/JOgGW8Ep4JuW/Sh4B+jGmgF6gAbRne7bJTqfgEXgPn8M+XDagLKFuRjEG4ZOki9zgZoO+tHgRfAR6AX/Ap0uK0D04yfV0yQHgsMR+F1RrDsoGAaSsXTr6oJk04NDEHjehPnLgoJhIBlvWzrm+7XlEe1NhWtqhUMQZJzp6VhaseDzyQ2DYBLQptZTcIwLDINDfXsnLo8qT4LuMtmOqIQ2RNTWC6NML5hxhvhhk9LalJXvd3HVwirq6+6VZe+HYWnGIh7YEmep0R5j+FtmdL+JJAx8ifutGhYwVvyWQWw+FGifCJcbOpYxmuBvIJtZWZmgzjLZx5WiIQ4w1mIzYpL8GCxNUN9lOlz/A6WzLJg0RCTjfQ9kr5ZKQn5hQknyZilYINDpwag3Pr3IFQ9rKPMcPEKrPaTD3Xw3Fsq4LvEuILsCsu8HOs+Jxc5aNX4nWA72gH+Ab7WHNUId7gvADtALfNMLtFZI775d3njmjRNus+UadE6AomklvgFrwQwnHqRDrQlgCfgMaIWKdg9iiu6KuYw1B+znE/3kIMcdVLomRAF9lHb8DzIgELrw3VWyAAAAAElFTkSuQmCC) | 3 | 60 | 5 | 20 | 4 | High health/dmg vs units. Cannot hit towers |

*Range*: Maximum Euclidean distance targetable.  
*Speed*: Frames required to move one space.

---

## Units: Defenders (Structures)
Structures persist across multiple turns. They block physical paths. A Structure can be upgraded to gain stats. Missing health persists through upgrade. Upgrade costs equal base cost unless stated otherwise.

| Name | Image | Cost (Struct) | Health | Role | Upgrade Stats |
| --- | --- | --- | --- | --- | --- |
| **Wall** | ![Wall](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABwAAAAcCAYAAAEFCu8CAAAAAXNSR0IArs4c6QAAAohJREFUSA2llj1uGzEQhSUjpXKBpDIMpNcBgs0FDKRwGuccPoAAH8ZwYeQEalK4MeBWOoDdxJXdGTA23+NyNsNZrlaOBhhxft7MkFwOqdksU9u2a5P7EWN7NOqR10O3SfHWIyw/DfIB4cF7ZyivMkBf4PMktW0jx1ZGC7VRgCT3gnkYCxvKCn6Blw7ToTBewws41d+V8gbQpszt8uFstchRSk5AFx6hKPQfySYl0JMHG+g2gKJ6XAThtYC3wuEUMBeWxapo00TXDjcqdlDWgqCdFi1G0c7RQbvAZVZenb8qgjvpA4VAOTYD402MwtYHIA93NSfZuCRePIsJCx2kpr6Gn2Gd11UBiAoAP13UAd3GGK2xcTAdV7XJR1gto2NsVG6gWRmLs+cr4HvLuK4yim3GYDd9oOQc+K8fZYigmg7Mpr0qs9TQzkag1izavjdQGyZ63tmtrpiJp1m46yuS5cS8YyOYdLMwLhV4BicaC5AdQNmP2fiUYzUMKmPTFWU0uAV8sIHi2FRnBaqfdojYVAPGjDmRnayQq6oKu7v9YjECGvgPfChpy+pboqI41ca1fdWHGHygykR1K/mPhppIOQcfsPYNRjs6Fos6Bfoj1tVMv91WI9plbD4d4skVxSJRVw7YGsJyp8bQ9enpPAb/r05SXZSe1vp29k6ZY6/3ap9JkHBhSfP4osvmMQR/Cvohasz1qIJXIeNl0A9RY66uFsuNzT35fEzNgpyxRcpbB4D9w8nbnR6zd7cGwWoJewgt1/Ap1ozxNnA8ygqSTe+aTp2eKR0Gsf2zlW8s7tvUbtitE1dMzr1JseXtkqvOJ6sDIFh/pL/DX+HPsJ0+nfAH+Df8az6f3zPupL+TafBOo0GExAAAAABJRU5ErkJggg==) | 2 | 50 | Basic blocker/path builder | Cost: 1, Health: 120 |
| **Support** | ![Support](data:image/png;base64,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) | 4 | 20 | Grants 3 shielding to friendly units within 3.5 | Range: 7. Shielding: 4. Grants (0.3*Y) extra shielding. |
| **Turret** | ![Turret](data:image/png;base64,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) | 3 | 70 | Deals 6 damage to enemy Mobile unit within 2.5 distance each frame | Cost: 8, Damage: 15, Range: 3.5 |

---

## Map & Game Start
Units are placed and navigate along a diamond-shaped grid. Coordinates range from `0,0` to `27,27`. Start resources per player:
- 40 Structure Points
- 5 Mobile Points
- 30 Health

![Map](https://ai-games.s3-us-west-2.amazonaws.com/assets/map.png)

![Terminal Resources and Health](https://ai-games.s3-us-west-2.amazonaws.com/assets/terminal-resources-ui.png)

---

## Turn Structure
Each turn has three phases: `Restore`, `Deploy`, and `Action`.

### 1. Restore Phase (Turn Income)
At the start of each turn:
- **Decay:** Lose 25% of stored Mobile points from the previous turn.
- **Income Base:** Receive 5 Structure points and 5 Mobile points.
- **Income Bonus:** +1 Mobile point for every 10 turns elapsed.
- **Damage Bonus:** +1 Structure point for each point of damage dealt to enemy core health in the prior turn.

### 2. Deploy Phase (Player Inputs)
- Players send commands within 5 seconds to deploy Mobile units or Structures using accumulated points. Failing the time limit damages health (-1 HP per sec limit exceeded).
- Players can refund/remove old structures: *Refund = 75% * InitialCost * (RemainingHealth / OriginalHealth)*

### 3. Action Phase (Simulation)
- **Rules Execution Order:**
  1. Supports grant shields to friendly units within range.
  2. Mobile units take steps. Self-destruct if trapped (*min. 5 tiles moved*).
  3. Units Attack (following targeting logic).
  4. Dead units (<= 0 health) are removed.
- **Win Condition:** First to reduce enemy Health to 0 wins. Otherwise, most health at round 100 wins. If tied, lowest global compute time wins.

---

## Advanced Mechanics

### Patching / Movement Logic
Mobile units choose the shortest path avoiding structures:
1. Target lowest path cost.
2. Alternate direction explicitly (e.g. horizontal, then vertical).
3. First move prefers vertical.
4. Breaks path duration ties by moving directly toward destination corner.
*Path Obstruction:* If opposite edge is blocked, the unit paths towards the deepest open tile in enemy territory, moves to it, and self-destructs (if distance traveled > 5), dealing equivalent structure damage.

### Targeting Logic
Priority ordered list for unit targeting:
1. Mobile units > Structures.
2. Nearest target distance.
3. Lowest health.
4. Furthest progressed inside unit's own territory.
5. Closest to an edge.
*Attack Ordering:* Units fire sequentially in the order they were spawned on the board during Deploy.

### Shielding Logic
- No cap to maximum stacked shielding on a unit.
- Supports can shield an unlimited number of units, but only shield a specific unit *once*.

---

## Game Requirements & Dev Guide
- **Python:** 3.6 or latest. Windows requires PowerShell v5.
- **Java:** 10 or latest.
- **Browser:** Google Chrome. Enable hardware acceleration if there are visual bugs.
- Always implement the provided `debug_print` function rather than standard `print` (to prevent Engine JSON read errors).

*A quick start repo with starter code for Python, Java, and Rust is provided over at the Official StarterKit.*

---

## Troubleshooting
- **com.google.gson.JsonSyntaxException:** Use `debug_print` to print, normal print interferes w/ Engine IO.
- **compiled by a more recent version of the Java Runtime:** Install Java 10+ and update PATHs appropriately.
- **No such file or directory:** Run matching engine from correct working directory base.

---  
## Glossary
- **Mobile points:** Attacker budget currency.
- **Structure points:** Defender budget currency. 
- **Health:** Core life. First to 0 loses.
- **Attack:** Triggers damage per frame if target in range bounds.
