# Hybrid ACS v7.21 (flat)

A single-file, in-browser version of the Hybrid Spacecraft Attitude Control simulation. No build step, no libraries: open `index.html` or serve it with GitHub Pages.

**Live page:** `https://<your-username>.github.io/<this-repo>/` (replace with your own)

## What it shows

- A spacecraft starting 120° away from its target, slewing back under the hybrid controller
- Live readouts: time, control regime, pointing error, angular speed, energy ratio, damping gain
- Pointing error over time on a log scale, with the regime thresholds marked
- A model of the quaternion memory bank: 1024 slots, a write pointer, and wrap-around

## What is ported

This is a JavaScript port of `Hybrid.hs` from [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid):

- Quaternion math, exponential and logarithmic maps, double-cover handling
- Four-regime gain scheduling (acquisition, tracking, settling, fine-pointing)
- Energy-ratio braking and 45 N·m torque saturation
- Star tracker and IMU noise
- Euler rotational dynamics with inertia 100 / 120 / 80
- 25 s run at 100 Hz

## What is simplified

- The memory bank is a small JavaScript model of the buffer in [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware). It does not run the Verilog or C code, and it has no busy flag.
- Position and velocity are left out. Only attitude is simulated.
- The 3D view is a plain wireframe box, not a real spacecraft model.
- Noise is not seeded, so every run differs slightly.
- I have not compared the output against the compiled Haskell. Treat the numbers as a faithful port of the logic, not a verified match.

## Related repos

| Repo | Role |
|---|---|
| [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid) | Current Haskell simulator |
| [SpaceCraft](https://github.com/subunits/SpaceCraft) | Earlier Haskell versions (v2.0 to v10.0, plus two NASA files) |
| [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware) | Verilog memory-bank controller and C driver |
| [Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware](https://github.com/subunits/Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware) | UML diagrams for the above |

## Run locally

Open `index.html` in any modern browser. On a computer you can also run `python3 -m http.server` in this folder.
