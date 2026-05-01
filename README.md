# Rabi Oscillations Simulation

An interactive quantum physics simulation and student worksheet for teaching Rabi oscillations in a high school or introductory college setting. Built as a static HTML/CSS/JS site — no build step, no framework, deploys directly to Vercel.

---

## Pages

| File | URL | Description |
|---|---|---|
| `index.html` | `/` | Interactive simulation with Bloch sphere and probability chart |
| `about.html` | `/about` | Physics explainer — resonance, qubits, Rabi frequency, detuning |
| `worksheet.html` | `/worksheet` | Student worksheet with auto-save to Supabase |
| `answer-key.html` | `/answer-key` | Teacher answer key (locked until all worksheet answers are filled) |

---

## Simulation Features

- Real-time Rabi oscillation probability plot (P|0⟩ and P|1⟩)
- Interactive Bloch sphere visualization (orthographic 3D projection via Canvas 2D)
- Adjustable Rabi frequency (Ω), detuning (Δ), and number of cycles
- π and π/2 pulse buttons
- Probability history chart (Chart.js)
- Animated probability marker

---

## Worksheet Features

- 12 questions across 4 parts: background reading, pre-simulation predictions, simulation exploration (Activities A, B, C), and analysis
- Student name and grade fields saved to database
- All answers auto-save to Supabase on input (debounced 800ms)
- Session ID stored in `localStorage` — students can close the tab and resume
- Answer Key button locked until every field is filled in
- Print/PDF button with classroom-ready formatting (name, date, period, class, teacher fields)

---

## Database Setup (Supabase)

The worksheet uses the Supabase REST API directly from the frontend — no backend required.

### 1. Create the table

Run this in your Supabase project under **SQL Editor**:

```sql
CREATE TABLE worksheet_responses (
  id            UUID        DEFAULT gen_random_uuid() PRIMARY KEY,
  session_id    TEXT        NOT NULL,
  question_id   TEXT        NOT NULL,
  answer        TEXT,
  student_name  TEXT        DEFAULT '',
  student_grade TEXT        DEFAULT '',
  updated_at    TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE (session_id, question_id)
);

-- Allow anonymous read/write (no login required)
ALTER TABLE worksheet_responses ENABLE ROW LEVEL SECURITY;

CREATE POLICY "anon insert"     ON worksheet_responses FOR INSERT TO anon WITH CHECK (true);
CREATE POLICY "anon select own" ON worksheet_responses FOR SELECT TO anon USING (true);
CREATE POLICY "anon update own" ON worksheet_responses FOR UPDATE TO anon USING (true);
```

### 2. Credentials

The Supabase URL and anon key are already set in `worksheet.html` near the bottom of the file:

```js
const SUPABASE_URL  = 'https://hitybuefnjmmoakqbpcf.supabase.co';
const SUPABASE_ANON = 'eyJ...';
```

To use a different project, replace both values with your own from **Supabase → Settings → API**.  
Always use the **anon/public** key — never the `service_role` key in frontend code.

### 3. How data is stored

Every answer is one row: `(session_id, question_id, answer, student_name, student_grade)`.  
`session_id` is a UUID generated on first visit and persisted in `localStorage`.  
`student_name` and `student_grade` are stamped on every row, so filtering by name in the Supabase table shows all of a student's answers at once.

### Question ID reference

| question_id | Question |
|---|---|
| `student_name` | Student's full name |
| `student_grade` | Student's grade level |
| `p2_q1_resonance_line1` / `p2_q1_resonance_line2` | Q1 — Define resonance |
| `p2_q2_qubit_prediction` | Q2 — Predict qubit behavior at resonance |
| `p2_q3_superposition_meaning` | Q3 — What is 50/50 superposition? |
| `p3a_omega{X}_osc_speed` | Activity A — Oscillation speed at Ω = X |
| `p3a_omega{X}_reaches_100` | Activity A — Does \|1⟩ reach 100% at Ω = X? |
| `p3a_omega{X}_inversion_time` | Activity A — Time to full inversion at Ω = X |
| `p3a_q4_omega_pattern` | Q4 — Pattern: how Ω affects speed |
| `p3a_q5_prob_ceiling` | Q5 — Why P(\|1⟩) can't exceed 100% |
| `p3b_delta{X}_omega_r` | Activity B — Calculated Ω_R at Δ = X |
| `p3b_delta{X}_max_prob` | Activity B — Max P(\|1⟩) at Δ = X |
| `p3b_delta{X}_speed` | Activity B — Faster or slower vs Δ = 0? |
| `p3b_q6_detuning_effect` | Q6 — Effect of increasing Δ on max probability |
| `p3b_q7_detuning_speed` | Q7 — Effect of Δ on oscillation speed |
| `p3c_half_pi_p0_after` | Activity C — P(\|0⟩) after π/2 pulse |
| `p3c_half_pi_p1_after` | Activity C — P(\|1⟩) after π/2 pulse |
| `p3c_half_pi_gate_name` | Activity C — Name of π/2 gate |
| `p3c_pi_p0_after` | Activity C — P(\|0⟩) after π pulse |
| `p3c_pi_p1_after` | Activity C — P(\|1⟩) after π pulse |
| `p3c_pi_gate_name` | Activity C — Name of π gate |
| `p3c_q8_half_pi_measurement` | Q8 — Expected measurement after π/2 pulse |
| `p3c_q9a_double_pi_prediction` | Q9a — Predict two π pulses in a row |
| `p3c_q9b_double_pi_result` | Q9b — What the simulation showed |
| `p4_q10a_rabi_swing_similarity` | Q10a — Similarity between Rabi and swing |
| `p4_q10b_rabi_swing_difference` | Q10b — Fundamental difference |
| `p4_q11a_qubit_error_factor1` | Q11a — First thing that could go wrong |
| `p4_q11b_qubit_error_factor2` | Q11b — Second thing that could go wrong |
| `p4_q12_atomic_clock_reasoning` | Q12 — Why define time with atomic oscillations? |

---

## Deployment (Vercel)

This is a static site — no build process needed.

1. Push the `/v3/` folder to a GitHub repository
2. Import the repo in [vercel.com](https://vercel.com)
3. Set **Framework Preset** to `Other` and **Root Directory** to `v3`
4. Deploy — done

Or drag-and-drop the `/v3/` folder directly into the Vercel dashboard.

---

## Physics Reference

| Symbol | Meaning |
|---|---|
| Ω | Rabi frequency — coupling strength between qubit and drive field |
| Δ | Detuning — difference between drive frequency and qubit transition frequency |
| Ω_R | Generalized Rabi frequency: Ω_R = √(Ω² + Δ²) |
| t_inv | Time to full inversion at resonance: t_inv = π / Ω |
| P(\|1⟩) | Probability of measuring excited state: (Ω/Ω_R)² sin²(Ω_R t / 2) |

---

## Tech Stack

- Vanilla HTML / CSS / JavaScript — no framework, no bundler
- [Chart.js](https://www.chartjs.org/) — probability history chart
- [Google Fonts](https://fonts.google.com/) — Space Mono, DM Sans
- [Supabase](https://supabase.com/) — Postgres database via REST API (anon key, no backend)
- [Vercel](https://vercel.com/) — static hosting
