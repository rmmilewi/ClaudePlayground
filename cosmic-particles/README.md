# Cosmic Particles

An interactive particle simulator that creates mesmerizing galaxy-like formations in your browser.

## How to Use

Open `index.html` in any modern browser. No build step, no dependencies — it just works.

## Controls

| Input | Action |
|-------|--------|
| **Left click** | Place a gravity attractor — particles spiral toward it |
| **Right click** | Place a repulsor — particles blast away from it |
| **Click + drag** | Fling a stream of particles in the drag direction |
| **Scroll wheel** | Increase/decrease gravity strength (0.1x – 5.0x) |
| **Spacebar** | Toggle particle trails on/off |
| **R** | Reset the simulation |

## How It Works

- ~800 particles spawn initially and drift gently across the screen
- New particles continuously stream in from the edges
- Clicking creates gravity wells (attractors or repulsors) that decay over time
- Particles change color based on velocity: cool blue when slow, shifting to purple and pink as they accelerate
- The trail effect is achieved by clearing the canvas with a semi-transparent black overlay each frame, letting previous positions fade gradually
- Additive blending (`globalCompositeOperation: 'lighter'`) creates the glowing effect where particles overlap
- A twinkling starfield provides atmosphere in the background

## Technical Details

- Pure HTML5 Canvas 2D — no libraries, no WebGL
- Targets 60fps with up to 3,000 particles
- Particle-well interactions use softened inverse-square gravity
- Object recycling minimizes garbage collection pauses
