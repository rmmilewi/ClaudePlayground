# Cosmic Particles - Development Notes

## 2026-03-28: Project Start

### Goal
Build an interactive cosmic particle simulator that runs in a single HTML file with no dependencies.

### Design Decisions

- **Single file approach**: Everything in one `index.html` — HTML, CSS, and JS. No build step, no dependencies. Just open and play.
- **Canvas API**: Using HTML5 Canvas 2D for rendering. WebGL would be faster but Canvas2D is simpler and sufficient for ~3000 particles with trails.
- **Particle system architecture**:
  - Each particle has position, velocity, hue, size, and life
  - Particles are recycled when they die (object pooling avoids GC pressure)
  - Gravity wells stored in a simple array; each frame we iterate particles × wells
- **Visual effects**:
  - Trail effect achieved by clearing canvas with `rgba(0,0,0,0.05)` instead of full clear — previous frames fade gradually
  - Glow effect using `globalCompositeOperation = 'lighter'` (additive blending)
  - Particle color mapped from velocity via HSL — slow particles are cool blue, fast ones shift to warm pink/white
- **Interaction model**:
  - Left click = attractor (gravity well that pulls)
  - Right click = repulsor (pushes particles away)
  - Drag = fling new particles in drag direction
  - Scroll = adjust gravity multiplier
  - Keyboard: Space toggles trails, R resets

### Performance Considerations
- Targeting 60fps with 2000-3000 particles
- Avoiding object allocation in the hot loop
- Using squared distance to skip sqrt where possible
- Capping max particles and recycling dead ones
