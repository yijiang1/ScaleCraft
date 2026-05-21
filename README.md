# Scalecraft

A multi-scale factory-builder puzzle game. Route atoms through conveyor belts and bonders to synthesize molecules — then zoom out and use those molecules to build battery nanoparticles, battery cells, and electric cars.

Inspired by SpaceChem, Opus Magnum, and Factorio.

## Play

Open [`Scalecraft.html`](Scalecraft.html) in any modern browser. No install needed.

## How It Works

- **Place tiles** while paused: belts (→ ← ↑ ↓), dispensers, bonders, and a collector
- **Run** the simulation and watch atoms travel, accumulate, and bond
- **Zoom in** (right-click a dispenser with a ↳ badge) to edit the sub-factory that produces it
- **Zoom out** via the breadcrumb to return to the bigger picture

## Scale Ladder

| Scale | What you build | From |
|-------|---------------|------|
| ⚛ Atomic (~1 nm) | H₂, H₂O, NH₃, H₂O₂ | H, O, N atoms |
| 🔬 Nano (~100 nm) | Electrolyte drop, Cathode dot | Molecules from atomic scale |
| 🔋 Meso (~1 mm) | Battery cell | Nano particles |
| 🚗 Macro (~1 m) | Electric car | Battery cells + motor + chassis |

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Space | Run / Pause |
| R | Reset simulation |
| D | Load demo layout |
| 1–4 | Belt tools |
| 5+ | Element dispensers |
| 0 | Eraser |

## Design Document

See [`docs/design.md`](docs/design.md) for the full game design document.

## License

GNU General Public License v3.0
