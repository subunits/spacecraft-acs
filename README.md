# Hybrid ACS v7.21 (flat)

A single-file, in-browser version of the Hybrid Spacecraft Attitude Control simulation. No build step, no dependencies: everything lives in `index.html`, and it runs on desktop, tablet, and phone.

## What it shows

- A spacecraft starting well away from its target attitude and slewing back under the hybrid controller, drawn against the target orientation
- Live readouts: time, control regime, pointing error, angular speed, energy ratio, damping gain, disturbance torque, and gyro bias
- Pointing error over time on a log scale, with the regime thresholds marked
- A quaternion memory bank of 1024 slots with a write pointer that wraps around when the buffer fills

## Controls

- **Pause / Reset** stop and restart the 25 s mission.
- **Entropy** switches randomness on or off and restarts the run. When on, each run begins from a random attitude and spin, the gyro bias drifts slowly, and a small random disturbance torque acts on the spacecraft throughout. When off, every run starts from the same 120° offset with no disturbances.
- **Speed** sets playback as a multiple of real time.
- **Seed** fixes the random sequence, so the same seed always gives the same run. **Random seed** picks a new one.

## Layout

The page adapts to screen size. The attitude view and error plot sit side by side when there is room and stack when there is not. The memory bank grid chooses its column count from the available width, using 32 columns on phones and 64 on wider screens.

## What is ported

A JavaScript port of `Hybrid.hs` from [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid):

- Quaternion math, exponential and logarithmic maps, double-cover handling
- Four-regime gain scheduling: acquisition, tracking, settling, fine-pointing
- Energy-ratio braking and 45 N·m torque saturation
- Star tracker and IMU noise
- Euler rotational dynamics with inertia 100 / 120 / 80
- A 25 s run at 100 Hz

## Differences from the Haskell

- The entropy features (random starts, gyro drift, disturbance torque) are additions. They are not in `Hybrid.hs`. Turning entropy off reproduces the original fixed scenario.
- The memory bank follows the behavior of the Verilog controller in [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware), checked in simulation: five 32-bit values per slot, 1024 slots with wrap-around, a registered read that lags one clock, and a write counter that counts every write. It does not run the Verilog or C code. The busy flag is left out because the controller's flag never asserts.
- Position and velocity are omitted. Only attitude is simulated.
- The 3D view is a wireframe box, not a spacecraft model.
- The integrator is explicit Euler, as in the Haskell, so torque-free momentum and energy drift slightly (about 0.1% over 25 s).
- With entropy off, the headline results match the compiled Haskell: a final error of about 1.13° and a peak torque of about 16.7 N·m from a 120° start. Sensor noise is random in both, so small figures vary from run to run.

## Related repositories

| Repository | Role |
|---|---|
| [Space-Craft-Hybrid](https://github.com/subunits/Space-Craft-Hybrid) | Current Haskell simulator |
| [SpaceCraft](https://github.com/subunits/SpaceCraft) | Earlier Haskell versions (v2.0 to v10.0) and two NASA ACS files |
| [quaternion-memory-hardware](https://github.com/subunits/quaternion-memory-hardware) | Verilog memory-bank controller and C driver |
| [Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware](https://github.com/subunits/Hybrid-Spacecraft-Attitude-Control-Quaternion-Simulation-to-Real-Time-Hardware) | UML diagrams covering the simulator and the hardware |

## Running

Open `index.html` in any modern browser.
