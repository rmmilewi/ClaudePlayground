# Development Notes — Scientific Software Testing Interactive Tool

## Starting Point
- Read ISSTA 2020 paper "Testing High Performance Numerical Simulation Programs" by He et al.
- Paper covers testing 5 nuclear simulation programs (ANT-MOC, MISA-MD, MISA-SCD, MISA-RT, ATHENA) using 5 approaches
- Found 33 bugs total; PT and MT most effective

## Design Decisions

### Choosing the Exemplar
- Chose 1D heat diffusion (explicit Euler) over molecular dynamics or neutron transport
- Reasons: easy to visualize, has analytical solutions, conservation laws, demonstrates all 5 approaches naturally
- Heat equation: dT/dt = alpha * d2T/dx2
- Discretized: T[i]^{n+1} = T[i]^n + r*(T[i+1]^n - 2*T[i]^n + T[i-1]^n), r = alpha*dt/dx^2

### Bug Design
- Each chapter has a "primary" bug that the testing approach catches most naturally
- CT bug: wrong alpha (1.2x) — causes output to diverge from analytical solution
- DT bug: Dirichlet BC instead of Neumann on left edge — causes energy leak visible in differential comparison
- PT bug: off-by-one in update loop (skips last interior point) — violates energy conservation
- MT bug: asymmetric right boundary (slight bias) — breaks symmetry metamorphic relation
- RE bug: forward difference instead of central difference — degrades convergence from O(dx^2) to O(dx)

### Key Technical Challenges

1. **JS `**` operator ambiguity**: `-expr ** 2` causes a SyntaxError in strict parsing because unary minus before exponentiation is ambiguous. Fixed by using `Math.pow()` throughout.

2. **Global variable collision**: Named the div `id="app"` and the JS variable `app`, but browsers create `window.app` for elements with IDs, overwriting the class instance. Fixed by using `window.heatApp`.

3. **Boundary conditions for Classical Testing**: The sine IC analytical solution assumes Dirichlet BCs (T=0 at ends), but the simulator initially used Neumann BCs everywhere. Added a `bcMode` parameter to the simulator.

4. **Alpha bug stability**: Initially tried alpha*1.5 (50% off) but this pushed `r` from 0.4 to 0.6, exceeding the explicit Euler stability limit of 0.5. Settled on alpha*1.2 (r=0.48, still stable).

5. **Richardson's Extrapolation**: Tricky to get right. Initial approach compared solutions at different resolutions directly via interpolation — got garbage ratios. Switched to comparing L2 error norms against analytical solution at each resolution. Used fixed dt (from finest grid) for all runs to isolate spatial error. Used median/average ratio for robustness.

### Architecture
- Single HTML file (~2000 lines) with embedded CSS and JS
- Classes: HeatSimulator, Visualizer, App
- Static methods: Analytical, TestRunner
- Canvas-based rendering for heat bars and line graphs
- DOM-based property dashboard with sparkline mini-charts
- Dark theme with chapter-specific accent colors

## What Worked Well
- The heat bar + line graph combination gives great visual intuition
- Property dashboard with real-time updates during simulation is very engaging
- The consistent chapter flow (intro → clean run → toggle bug → buggy run → results) is easy to follow
- Color-coded pass/fail badges with animation give satisfying feedback

## What Could Be Improved
- Could add more interactivity (e.g., let user define their own properties or MRs)
- Could show the bug's effect on the actual code with highlighted diff
- Could add a "challenge mode" where user must identify which test catches a hidden bug
- Richardson's extrapolation visualization could show a log-log convergence plot
