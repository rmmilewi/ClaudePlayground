# Testing Scientific Software — An Interactive Guide

An interactive web-based teaching tool that walks users through five approaches to testing scientific simulation software, built as a single self-contained HTML file.

## Background

Based on the ISSTA 2020 experience report ["Testing High Performance Numerical Simulation Programs"](https://doi.org/10.1145/3395363.3397382) by He, Wang, Shi, and Liu, which documented testing five nuclear simulation programs and finding 33 bugs using five different testing approaches.

## The Exemplar: 1D Heat Diffusion

The tool uses a **1D heat diffusion simulator** as the "program under test." The heat equation `dT/dt = alpha * d2T/dx2` is discretized with explicit Euler and solved on a 1D grid. This simple physics problem is ideal because:

- It's easy to visualize (temperature distribution along a bar)
- It has known analytical solutions for certain initial conditions
- It has physical conservation laws (energy, boundedness)
- It naturally demonstrates all five testing approaches
- Realistic bugs can be injected that mimic real-world scientific software defects

## The Five Testing Approaches

### Chapter 1: Classical Testing
Compare the simulation output against a **known analytical solution** (oracle). A sine initial condition has an exact solution: `T(x,t) = 100*sin(pi*x)*exp(-pi^2*alpha*t)`.

**Bug:** Wrong diffusion coefficient (alpha is 20% too high), causing the numerical solution to diverge from the analytical.

### Chapter 2: Differential Testing
Compare the output of the **program under test** against a **reference implementation**. No oracle needed — just agreement between two independent implementations.

**Bug:** Dirichlet boundary condition (T=0, fixed) instead of Neumann (insulated, zero-flux) on the left edge, causing energy to leak out.

### Chapter 3: Property-Based Testing
Check that the simulation satisfies **necessary physical properties** at every timestep:
- **Energy Conservation:** Total thermal energy must be constant for an insulated system
- **Temperature Bounds:** No temperature below 0 or above the initial maximum
- **Monotonic Convergence:** Variance of temperature should decrease monotonically toward equilibrium

**Bug:** Off-by-one in the update loop (skips the last interior grid point), violating energy conservation.

### Chapter 4: Metamorphic Testing
Check **relationships between inputs and outputs** without needing an oracle:
- **Linearity MR:** Scaling the initial temperature by k should scale the output by k
- **Symmetry MR:** Mirroring the initial condition should mirror the output

**Bug:** Asymmetric right boundary handling (slight numerical bias), breaking the symmetry metamorphic relation.

### Chapter 5: Richardson's Extrapolation
Verify the **convergence order** of the numerical method by running at three resolutions. For a second-order method, the error ratio between successive refinements should be approximately 4.

**Bug:** Forward difference `(T[i+1] - T[i])` instead of central difference `(T[i+1] - 2*T[i] + T[i-1])`, degrading convergence from second-order to first-order.

## How to Use

1. Open `index.html` in any modern browser
2. Click "Begin the Walkthrough" or select a chapter from the sidebar
3. Each chapter follows the same pattern:
   - Read the introduction explaining the testing approach
   - Run the simulation with the bug **off** — observe the test passing
   - Toggle the bug **on** and run again — observe the test failing
   - Read the lesson card explaining strengths and limitations
4. Complete all five chapters and view the Summary comparison table

## Technical Details

- **Single file:** Everything is in `index.html` (~2200 lines of HTML/CSS/JS)
- **No dependencies:** Pure vanilla JavaScript, HTML5 Canvas for visualization
- **Simulation:** Explicit Euler method with configurable grid size, diffusion coefficient, timestep, and boundary conditions
- **Visualization:** Canvas-based heat bars (blue-white-red gradient), line graphs, sparkline property monitors, and convergence plots

## Key Findings from the Paper

| Approach | Bugs Found | Needs Oracle? | Most Effective For |
|----------|:----------:|:-------------:|-------------------|
| Classical | 3 | Yes | Simple validation against known results |
| Differential | 9+ | No (needs ref.) | Implementation differences |
| Property-Based | 18+ | No | Invariant violations |
| Metamorphic | 8+ | No | Relationship violations |
| Richardson's | + | No | Discretization errors |

The paper concluded that **property-based testing** and **metamorphic testing** were the most effective approaches, while classical testing was severely limited by the oracle problem.
