# Rabi Oscillations Simulation

**[rabi-oscillations-simulation.vercel.app](https://rabi-oscillations-simulation.vercel.app)**

An interactive quantum physics simulation and student worksheet built for teaching Rabi oscillations at the high school level. The site lets students use a quantum two-level system simulation, observe how the qubit state changes, and do a structured worksheet that auto-saves their answers to a database.

---

## What I Built

### Interactive Simulation (`Index`)
The main page is a live Rabi oscillation simulator. Students can drag sliders to adjust the Rabi frequency (Ω) and detuning (Δ) and watch the qubit state update across two seperate visualizations:

- **Bloch sphere** — a 3D orthographic projection drawn showing exactly where the qubit state sits at any moment
- **Probability chart** — a live Chart.js plot of P(|0⟩) and P(|1⟩) over time, with an animated marker tracking the current state

π and π/2 pulse buttons let students apply quantum gates directly and see the result immediately. All physics runs client-side using the exact Rabi oscillation equations — no approximations.

### Physics Explainer (`/about`)
A standalone explainer page covering the background physics: resonance, two-level systems, the Rabi frequency, detuning, and how this connects to real quantum computing hardware. Written to be accessible to students encountering quantum mechanics for the first time.

### Student Worksheet (`/worksheet`)
A worksheet with 12 questions across four sections:

- **Part I** — Background reading (classical resonance analogy + quantum twist)
- **Part II** — Pre-simulation predictions students answer before touching the sim
- **Part III** — Guided simulation exploration: Activity A (effect of Ω), Activity B (effect of Δ), Activity C (π and π/2 pulses and quantum gates)
- **Part IV** — Analysis and synthesis questions connecting the simulation to real quantum computing

Every answer field auto-saves to a Supabase database as students type. A session ID in `localStorage` lets students close the tab and pick up exactly where they left off. Student name and grade are stored alongside every answer so responses are easy to look up by student in the database.

The Answer Key button at the bottom stays locked until every single field is filled in — students can't skip ahead.

The worksheet also has a clean **print/PDF mode**: the navigation and save UI disappear, a name/date/period/class/teacher header appears at the top, and all answer boxes print empty and ready to fill by hand.

### Teacher Answer Key (`/answer-key`)
A separate page with model answers for all 12 questions and completed data tables, accessible from the worksheet once a student has finished.

---

## Stack

- **Vanilla HTML / CSS / JavaScript** — no framework, no build step
- **Chart.js** — probability history chart
- **Supabase** — Postgres database, accessed directly from the frontend via REST API
- **Vercel** — static hosting

---

## Physics

The simulation models a quantum two-level system driven by a near-resonant oscillating field. The key quantities:

- **Ω** — Rabi frequency, sets how strongly the field couples to the qubit
- **Δ** — Detuning, the difference between the drive frequency and the qubit's natural transition frequency
- **Ω_R = √(Ω² + Δ²)** — Generalized Rabi frequency
- **P(|1⟩) = (Ω/Ω_R)² sin²(Ω_R t / 2)** — Probability of finding the qubit in the excited state
- **t_inv = π / Ω** — Time to fully invert the qubit at perfect resonance (Δ = 0)

At resonance (Δ = 0), the qubit oscillates completely between |0⟩ and |1⟩. As detuning increases, the oscillation speeds up but never fully reaches |1⟩ — the qubit gets "pulled off axis" on the Bloch sphere.
