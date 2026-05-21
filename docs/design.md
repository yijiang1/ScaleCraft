# Scalecraft — Game Design Document

## Concept

A multi-scale factory-builder puzzle game where players route materials through a grid using conveyor belts and bonding stations to synthesize products — from atoms all the way up to electric cars. Inspired by SpaceChem, Opus Magnum, and Factorio. Lives in the PtychoHub Kids section as a chemistry and engineering intuition builder — contributor-only.

The defining mechanic: every dispenser at a higher scale hides a sub-factory from the scale below. Players can zoom in to edit it, and zoom back out to see the bigger picture. Factories within factories, all the way down to individual atoms.

The loop: place tiles while paused → run the simulation → watch atoms travel, accumulate, and bond → adjust until the factory produces the target molecule reliably.

---

## Core Mechanics

### Grid
- 9 × 6 cells, 70px each
- Tiles are placed while paused; the grid locks during simulation
- Click to place active tool, right-click to erase a single tile

### Tile Types

| Tile | Behavior |
|------|----------|
| Belt (→ ← ↑ ↓) | Moves a particle one cell in the arrow direction each tick |
| Dispenser (H / O / N) | Emits a single atom of that element into the adjacent right cell every `EMIT_EVERY` ticks |
| Bonder | Holds all incomplete particles that arrive; when 2+ are present, merges them and checks if the result matches the target. Complete molecules are ejected one cell to the right; incomplete molecules stay and wait for more atoms |
| Collector (★) | Scores complete target molecules that arrive; wrong molecules pass through unaffected |

### Particles
- Created by dispensers, one atom each
- `complete: false` until the molecule matches TARGET exactly
- Incomplete multi-atom intermediates stay at bonders, accumulating more atoms
- Belt movement and bonder ejection happen each tick in order: emit → move → bond → collect

### Bonding (tryBond)
Greedy single-bond algorithm: iterates all atom pairs and adds a bond wherever both have remaining valence capacity. Runs to fixpoint. Order-dependent — earlier atoms in the array bond first.

### Simulation Timing
- Tick interval: 650 ms
- Dispenser emission: every 5 ticks
- Animation: particles CSS-transition to new positions over 487 ms (75% of tick interval), so movement completes before the next tick

---

## Elements

| Symbol | Color | Valence | Notes |
|--------|-------|---------|-------|
| H | Blue (#38BDF8) | 1 | Hydrogen |
| O | Red (#F87171) | 2 | Oxygen |
| N | Green (#4ADE80) | 3 | Nitrogen — tool exists, no puzzle uses it yet |

---

## Current Puzzle: H₂O × 5

**Target:** `{ H: 2, O: 1 }` — produce 5 water molecules  
**Grid size:** 9 × 6

### Demo Solution

```
Row 1: [H src] → → → [BONDER] → → → [★]
Row 3: [O src] → → →    ↑
Row 4:                   ↑
Row 5: [H src] → → →    ↑
```

Timing works because path lengths differ: H₁ arrives at the bonder at tick 3, O at tick 5 (forming incomplete H–O), H₂ at tick 7 (completing H₂O). The bonder holds each intermediate until the next atom arrives.

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Space | Run / Pause |
| R | Reset simulation (keeps tiles) |
| D | Load demo layout |
| 1–7 | Select belt / dispenser tools |
| 8 | Select Bonder |
| 9 | Select Collector |
| 0 | Select Eraser |

---

## Known Limitations

- **Bonder ejects rightward only.** Completed molecules always exit to `col + 1`. Placing a bonder near the right edge or without a belt to its right silently destroys the product.
- **Greedy bonding is order-dependent.** For simple diatomics this is fine; for larger molecules the result depends on arrival order, which may not be intuitive.
- **No particle collision.** Multiple particles can share a belt cell with no interaction — only bonders trigger merging.
- **Single puzzle.** The target molecule and grid size are hardcoded constants.

---

## Roadmap

### Near-term
- **Multiple levels** — puzzle progression with different target molecules (H₂O₂, NH₃, N₂, CO₂). Each level can introduce a new element or constraint.
- **Level select screen** — unlock next level on completion, persistent via localStorage.
- **Bonder direction control** — let the player set which side a bonder ejects from (could be a second tool variant or a right-click cycle).
- **Wrong molecule feedback** — flash red on collector when an incorrect molecule arrives, so the player knows their factory is producing junk.

### Medium-term
- **Splitter tile** — one input, two outputs; splits a multi-atom molecule back into atoms (needed for recycling waste or rerouting).
- **Rotator tile** — rotates the bond graph of a molecule passing through (relevant for stereochemistry puzzles).
- **Speed control** — slider for tick interval (100 ms–1500 ms); lets players slow down to debug or speed up once confident.
- **Solution replay** — after winning, offer a step-by-step playback mode for sharing or teaching.

### Long-term
- **Molecule viewer** — render bond graph inside the particle pill (lines between atom symbols) so players can see structure, not just formula.
- **Sandbox mode** — no target, free construction with an element palette; useful for learning what bonders and belts do.
- **Scoring system** — count tiles used and ticks to completion; surface a "efficiency" score after winning to encourage optimisation.
- **Editor / level sharing** — let contributors design and share custom puzzles via a URL-encoded layout string.

---

## Design Principles

1. **Puzzle, not simulation toy.** Every mechanic should serve a specific puzzle challenge. Add elements and tiles only when a new puzzle requires them.
2. **Timing is the puzzle.** The core insight is that belt path length = arrival delay. Good puzzles should make players reason about timing, not just connectivity.
3. **Fail visibly.** Wrong molecules should be observable (wrong formula label, wrong color, pass-through at collector) so players can diagnose problems without reading source code.
4. **Teach chemistry incidentally.** Valence, stoichiometry, and molecular formula are emergent from the mechanics — the player learns them by solving puzzles, not from explanations.
