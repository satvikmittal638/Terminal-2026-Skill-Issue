# Terminal C1Games Bot Strategy

Welcome to the central repository for our algorithmic bot for the Citadel Terminal competition.

Our core philosophy revolves around two principles: **Agility and Computational Efficiency.** We sacrifice complex pathfinding abstractions (like A*) in favor of hard-coded situational reactions and O(N) threat calculations.

---

## 🛡️ Defensive Strategy

Our defense avoids resource-inefficient units. **We never purchase Walls, and we never upgrade Turrets.** Our defense relies exclusively on a sturdy baseline of upgraded Supports and an expanding, reactive network of Turrets.

### 1. Support Core

- **Initial Placement:** We place exactly four Supports directly in the center backline at `(12, 11), (13, 11), (14, 11), (15, 11)`.
- **Upgrades:** We immediately seek to upgrade three of these supports, saving the final upgrade for available structure points (SP) in later rounds. The game engine resolves multiple upgrade requests efficiently based on available SP.

### 2. Turret Phasing

Our turrets are deployed in a phased approach.

- **Base Layer:** First, we establish a central block at `(12, 12), (11, 11), (15, 12), (16, 11)` to protect the core Supports.
- **Reactive Expansion:** To counter active threats, our bot profiles JSON turn strings to determine where the enemy struck last. We immediately prioritize pouring SP into the side taking heavy fire (Left or Right) through three strict build phases:
    1. **Outer Guard:** Securing the far edges (e.g., `(0, 13), (2, 13)` for Left).
    2. **Secondary Line:** Reinforcing the inner channels directly below the outer guards.
    3. **Center Fill:** Plugging the gaps in the centerline `(Y=13)` to seal the defense.
- If we have remaining SP after defending the active threat side, we continue building the identical mirrored phase on the opposite side.

---

## ⚔️ Offensive Strategy

Our offense is lean, alternating, and relies solely on **Scouts**. We explicitly ignore Demolishers and Interceptors.

### 1. The "Chunk of 5"

When we possess 5 or more Mobile Points (MP), we launch a strike consisting of exactly **5 Scouts** at once. Grouping attackers maximizes the chance of breaking through layered defenses before support shields deplete.

### 2. Alternating Assault Vectors

To bypass dynamic enemy scripts designed to place defenses on the side being attacked, we **never strike the same side twice in a row**. Our assault alternates systematically between the Left and Right quadrants.

### 3. Spawn Evaluation (Y <= 10)

When preparing to launch our Scouts:

1. We filter possible launch coordinates ensuring `Y <= 10` (from `[12,1]` down the physical diagonal edges).
2. We skip any coordinates currently blocked by our own stationary defenses.
3. We run a rapid calculation checking the target path to find the spawn location facing the **least guaranteed Turret damage**.
4. **Compute Shortcut:** If our loop identifies a path with zero incoming structural damage, we adopt it immediately and bypass further calculations.

---

## ⚡ Performance Optimization

By tracking incoming damage and breaches directly within the `on_action_frame` JSON payload, our algorithm dynamically deduces the enemy's attack side entirely through numerical array slicing, circumventing heavy simulation loops entirely.

The combination of preemptive coordinate mapping, linear threat assessments, and engine-native tracking guarantees our logic submits instantaneous moves on every single turn.

---

## 🏆 Performance & Team

### Team: Skill_Issue

We successfully created the **highest ELO bot** in the competition, ultimately securing **3rd Place** overall!

| Place | Team | Algorithm Name | Language | Elo Rating |
| :---: | :--- | :--- | :---: | :---: |
| 🥇 1 | HUNTR/X | TAKEDOWN_V4_DONTDELETE | PYTHON | 2053 |
| 🥈 2 | BBH | PYTHON-ALGO 2 | PYTHON | 1846 |
| 🥉 **3** | **SKILL_ISSUE** | **FINAL3GEM** | **PYTHON** | **2357** 🏆 |
| 4 | SEGMENTATION_FAULT | CHAOS | PYTHON | 2275 |

### Team Members

1. **Satvik Mittal**
2. **Om Gore** (Captain-Flowinity)
3. **Chatanya Maheshwari**

---

## 📁 Repository Structure

- **`final3gem/algo_strategy.py`**: The core executable Python bot holding all of our strategic algorithm routing and reactive defense logic.
- **`strategy.txt` & `final3.txt`**: Our raw strategy scratchpads detailing the mathematical breakdown of our defensive layers and the "Chunks of 5" scout methodology.
- **`rules.md`**: The official Citadel Terminal documentation outlining base-level unit stats, match mechanics, and engine details.
- **`STARTERKIT_README.md`**: The preserved default setup guide from C1Games explaining how to test algorithms locally across different operating systems.

