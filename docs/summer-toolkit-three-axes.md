# Magnetic Field Line Chaos: Topology, Dynamics, and Transport
### A summer toolkit — Summer 2026

Reproduction and verification. No new physics is claimed; the value is in the
measurement discipline and in one negative result, stated plainly.

**One question:**

> When a magnetic field breaks, does the heat leak out?

Answering it needs three distinct diagnostic axes, and none of them can stand in
for another.

| Axis | The question it asks | Tools |
|---|---|---|
| **Topology** | Is there a wall (a flux surface)? | Poincaré, Greene residue, converse-KAM |
| **Dynamics** | Is the orbit regular or chaotic? | Weighted Birkhoff Average (WBA), Lyapunov/FTLE |
| **Transport** | Does heat actually leak? | diffusion exponent μ, effective volume V_PD |

## Repository map

The work lives in five repositories. One naming caution is worth stating up
front, because confusing two of them caused a chain of mistaken "corrections"
during the write-up.

| Label | Repository | Contents | Commit |
|---|---|---|---|
| **D1** | sv-2/`d1_standard_map` | standard map, diffusion **exponent** μ(K) | `4bc1fbd` |
| **B2** | sv-3 | Weighted Birkhoff Average | `ffe38da` |
| B4, D_FL, config scan | sv-2 | QUASR vacuum configurations | `0218c15` |
| C0–C3b | sv-4 | converse-KAM 3D, V_PD | `8a52ca1` |
| *(separate)* | sv-1 | standard map, diffusion **coefficient** D(K) | `ecd0c67` |

GitHub: [sv-1](https://github.com/kmh21012799-cpu/sv-1) ·
[sv-2](https://github.com/kmh21012799-cpu/sv-2) ·
[sv-3](https://github.com/kmh21012799-cpu/sv-3) ·
[sv-4](https://github.com/kmh21012799-cpu/sv-4)

> **sv-1 is not "D1".** D1 (sv-2/`d1_standard_map`) measures the diffusion
> *exponent* μ(K); sv-1 measures the diffusion *coefficient* D(K). Same system,
> different quantity.

## Overview

Part 1 builds the three axes on the standard map and on a QUASR vacuum field, and
checks them against each other. Part 2 carries them to a real 3D vacuum
configuration (104183), where three of the observables turned out not to measure
what they appeared to — and the three failures pointed to a single physical
conclusion. Part 3 moves to reduced models with a known answer key, and completes
a comparison that Paul, Hudson & Helander (2022) deferred to "future publication."

The headline of Part 2:

> Vacuum configurations (β = 0) contain almost no confined chaos. Confined chaos
> is a product of finite β.

So a vacuum field is not the stage on which to measure transport; that requires a
finite-β 3D equilibrium — HINT.

---

## Part 1 — Building the tools

### 1.1 QUASR pipeline (reproduction)

Reproduce Table A1 of Davies, Smiet, Punjabi, Boozer & Henneberg (2025,
*Nucl. Fusion* **65**, 076018) as an end-to-end check of the tool chain
(commit `2a7858a`, in sv-2).

The full chain — QUASR coils → Biot–Savart → 1 cm interpolated field → Poincaré
map → fixed point → Jacobian → residue → Lyapunov — was run on configuration
104183 (a tokamak-like divertor case). Its two period-1 divertor X-points
reproduce the paper to three significant figures.

| Quantity | Paper | This work |
|---|---|---|
| Topological index | −1 | −1 |
| Greene residue | −1.93 | −1.932 |
| Lyapunov exponent | 2.26 | 2.264 |

Three by-products:

1. **A typo in the paper.** Table A1's label 74608 returns HTTP 404 from QUASR
   and does not exist; the configuration in the body text (§4.2) is 74609.
2. **Finite-time singularity of the NRD flow.** For ε_x < 0 the u² term produces
   a force ~R³, so the continuous flow reaches a singularity in finite time;
   Punjabi–Boozer sidestep this with a fixed-step symplectic map (dφ = 2π/3600).
   This is not stated in the paper and is only found by hitting it — and the
   divergence corresponds physically to the divertor's diversion.
3. **The 1 cm grid method holds.** The finite-difference Jacobian of the
   interpolated field agrees with exact Biot–Savart on the residue to six digits.

The remaining six Table A1 rows (74609, 1258083) were not localized — their fixed
points sit outside the LCFS (74609) or in an A=24 miniature plasma (1258083), and
blind search did not converge. This is not a code error; the exact reproduction
of 104183 is the evidence.

### 1.2 D1 (sv-2/`d1_standard_map`) — diffusion exponent μ(K)

Source: sv-2/`d1_standard_map`, commit `4bc1fbd`. Stand up the transport axis by
reproducing the standard-map diffusion exponent μ(K), which reproduces the
textbook structure.

| K | μ | regime |
|---|---|---|
| 0.5, 0.7, 0.9 | ~0 | KAM wall, no global transport |
| 0.9716 (K_c) | ~0 | last torus breaks |
| 1.2 → 2.0 | → 1.0 | normal diffusion |
| 3.0, 6.9 | → 2.0 | ballistic |

**Trap 1 — N-convergence, ruled out.** The literature μ ≈ 1.5 might be a
finite-time artifact. Tracking K = 6.9 to N = 10⁷ (2D grid):

| N window | μ |
|---|---|
| 10³–10⁴ | 1.74 |
| 10⁴–10⁵ | 1.93 |
| 10⁵–10⁶ | 1.99 |
| 10⁶–10⁷ | 1.98 |

μ saturates at 2.0 and stays there; it does not drift to 1. The cause
(topology → transport): the m=1 accelerator fixed point at K=6.9, θ* = 1.9968, is
elliptic (Tr = −0.85), a stable island. About 0.37% of orbits are permanently
trapped and advance by Δp = 2π per step; their contribution
0.0037 × (2πN)² ≈ 1.5×10¹³ matches the measured MSD(10⁷) = 1.26×10¹³. The
super-diffusion is genuine ballistic transport from a stable island. The
literature "μ → 1 for N ≥ 10⁷" is the contrast case — a K *without* a stable
island (sticky/cantori Lévy) — and does not apply at K = 6.9.

**Trap 2 — symmetry line, confirmed real.** Initial conditions on the θ₀ = 0 line
versus a 2D grid: at K = 3.0 the line gives μ = 1.0 while the grid gives μ = 2.0
(a qualitatively different answer); at K = 6.9 the line is unusable (90% seed
spread). The line misses the accelerator island's basin. (Independently confirmed
by B2/sv-3, below.)

### 1.3 B2 (sv-3) — Weighted Birkhoff Average (reproduction)

The last dynamics-axis tool — the WBA of Duignan & Meiss (2023, *Physica D*
**449**, 133749), commit `ffe38da`. Three of four pass criteria met: weight
normalisation C(w=1) = 142.2503757771 (paper 142.2503758), ∫g = 1; regular-orbit
super-convergence dig ≈ 10–13; chaotic-sea dig ≈ 1–2.

**Two axes point at the same object — but this is not a cross-validation.** At
K = 6.9, D1 (transport) detects the accelerator anomaly through the diffusion
coefficient blowing up, and B2 (WBA) classifies the island interior as regular
(median dig = 10.43) with area fraction 0.405% on a 500×500 grid (the total
regular fraction, 0.406%, is essentially all this one island). The two agree, but
the shared quantities — position θ* and trace — are analytic properties of the
accelerator fixed point (sin θ* = 2π/K, Tr = 2 + K cos θ*), not independent
measurements, and D1 only detects the island, it does not quantify it. sv-3's own
record puts it precisely: WBA is an *"independent confirmation of the island D1
identified by transport scaling,"* and *"No novelty, no discovery claimed."* The
accurate statement is: the transport axis flagged an anomaly, and the dynamics
axis confirmed its cause.

The genuine independent cross-check is internal to sv-3: WBA and the Benettin
(finite-time Lyapunov) classifier agree on 99.99% of 14,400 initial conditions at
K = 6.9 — same repository, unrelated method. (Honestly, the speed advantage is
modest — 1.24× at equal iteration count; WBA's real edge is machine-precision
digits on regular orbits.)

**Symmetry-line trap (sv-3, K = 6.9).**

| IC sampling | chaos fraction | island hits |
|---|---|---|
| θ₀ = 0 line (2000 pts) | 1.0000 | 0 |
| θ₀ = 0.15 line | 0.9995 | 1 |
| p₀ = 0 line | 0.9060 | 188 |
| 2D grid | 0.9959 | island = 0.405% |

> The θ₀ = 0 line misses the accelerator island's basin entirely. Drawing initial
> conditions from a line contaminates the sample. D1 (sv-2/d1) met the same trap
> independently (K = 3.0: grid μ = 2, line μ = 1; §1.2).

**ε_cr = 0.665 not reproduced (an honest gap).** The paper gives the amplitude
weights (/21600) but not the amplitude-to-Hamiltonian normalisation (the
island-width convention). Both readings are possible — literal ε_cr ≈ 0.426,
overlap-calibrated ε_cr ≈ 1.2 — and the paper's 0.665 lies between them. The
mechanism does reproduce (WBA sees the confining KAM barrier break as dig
collapses from 8–9 to <2); only the number is convention-dependent. No
coefficient was tuned to hit 0.665.

### 1.4 sv-1 — diffusion coefficient D(K) *(a separate study)*

Commit `ecd0c67`. A different study from D1: D1 (sv-2/d1) measures the diffusion
*exponent* μ(K) — the exponent of ⟨(Δp)²⟩ ~ N^μ; sv-1 measures the diffusion
*coefficient* D(K) = ⟨Δp²⟩/(2T). Same system (standard map), different quantity.

D(K) was measured against Rechester–White quasilinear theory. Ensemble N = 20,000
particles, T = 2,000 steps, 5 seeds; K scan over [1.0, 10.0], 54 points. Away from
accelerator modes D/D_QL → 1 (median |ratio − 1| = 0.341, K ≥ 4), agreeing with
Rechester–White to a median 8.3%; Lyapunov λ rises from 0.05 (K=1) to 1.62 (K=10),
matching ln(K/2) to a few percent.

**The trap — the accelerator window.** For 2π ≈ 6.283 < K ≲ 7.45 transport is
ballistic, ⟨Δp²⟩ ∼ t². Fitting a diffusion coefficient there gives a large,
confident, meaningless number: D/D_QL = 618.8 at K = 6.40 (395.5 at K = 6.9). What
catches it is the fit R², which drops from 0.9998 in the diffusive regime to ~0.97
in the window.

> Fit a diffusion coefficient where there is no diffusion, and the number
> explodes. Check the growth exponent before you trust the coefficient.

**An observation that prefigures Part 3.** sv-1's record notes that the Lyapunov
exponent passes smoothly through the accelerator window — it measures local
instability and is blind to the global ballistic island that spikes D.

| In the accelerator window | |
|---|---|
| Transport (D) | blows up by 619× |
| Dynamics (λ) | passes smoothly, as if nothing happened |

The same thing recurs in Part 3, on Paul's four fields:

| On Paul's four fields | |
|---|---|
| Topology (converse-KAM) | cannot distinguish them |
| Dynamics (WBA) | cannot distinguish them |
| Transport (V_PD, ΔT) | distinguishes them |

The reason is the same: Lyapunov measures local instability, but transport is set
by global structure; and converse-KAM and WBA likewise ask "is this locally
chaotic," not "where does the heat go." The first repository and the last say the
same thing:

> A dynamics indicator does not see transport.

This is a connection drawn after the fact, not something sv-1 set out to find.
Stated honestly: in hindsight, the signal was already there in sv-1.

Not done: sv-1 did not quantify the island geometry ("does not characterise the
island geometry"), and did not fit the exponent μ(K) — that was D1 (sv-2/d1).

---

## Part 2 — Three failures, one conclusion

Three attempts to stand up the transport axis on a real 3D vacuum field (QUASR
104183, the pipeline-validated configuration). Each failed for a different,
identifiable reason.

### Failure 1 — tautology (connection length L_c)

A confined orbit completes the full 400 toroidal returns by definition, so its
connection length is fixed: L_c = 400 × circumference = 2595 ± 54 m for every
confined orbit. "Confined ⇒ long L_c" merely restates the confinement criterion;
it carries no independent information. More statistics would not help — a denser
grid would only confirm the artifact with more confidence.

### Failure 2 — bounded coordinate (Δψ with ψ = ι)

Radial diffusion was measured in a flux coordinate ψ built from ι. But ι runs only
from −0.21 (axis) to −0.15 (edge), a width of ≈ 0.06, so any spreading is bounded
and saturation is guaranteed. Measured ⟨(Δψ)²⟩ (sv-2, RECORD_D_FL_3D, commit
`3791557`): both classes are flat (μ → 0) — regular orbits (n=410) at 1.2×10⁻⁴ (the
ψ-interpolation noise floor), chaotic orbits (n=17) at 3×10⁻⁴ (2.5× the floor, in
variance). Nothing is detectable above the floor. (Earlier hand-supplied numbers —
μ ≈ 0.54/0.71, D_m ≈ 4.4×10⁻¹², corr −0.62 — were fabricated and are refuted; the
real corr(dig, log D_m) = −0.45.)

### Failure 3 — aliasing (dig from angle increments)

The chaos classifier used the WBA convergence (dig) of the poloidal-angle
*increment* per toroidal return. For ι ≳ 1 the field line advances more than one
poloidal turn per return, so the once-per-return increment is sampled below the
Nyquist rate and aliases; regular high-ι orbits then look chaotic. This produced a
plausible but false pattern — "high ι → high chaos," Pearson r(|ι|, chaos) = 0.674
— in a scan of random configurations, with 18 of 30 apparently exceeding 10%
confined chaos. Replacing the observable with a bounded position coordinate
(x = r cos θ, y = r sin θ) removed the aliasing: the correlation dropped to 0.279
(carried by a single configuration), and the count fell to 1 of 25. The same fix
also removed a milder derivative-noise inflation at low ι: for 104183, the
confined-chaos fraction fell from 2.29% to 0.29–0.76%.

### The root cause: there was nothing to measure

Re-classifying with the corrected (position-based) dig:

| | old dig (angle increment) | new dig (position) |
|---|---|---|
| regular | 410 | 450 |
| chaotic | 17 | 0 |
| escaped | 1103 | 1103 |

104183 has **zero** confined chaotic orbits. The 17 the old observable reported
were regular orbits with modestly elevated FTLE that the noisy angle-increment dig
pushed below the cut. We had been trying to measure something that was not there.

### Is there a stage anywhere? — a configuration scan

40 random QUASR vacuum configurations (criteria fixed before coding; nothing
cherry-picked):

| | |
|---|---|
| classified successfully | 25 |
| ≥ 10% confined chaos | 1 (1646237, 43%) |
| the other 24 | mostly ~0% |

The single exception, 1646237, has no conventional magnetic axis. A period-doubling
bifurcation has turned the axis point into a hyperbolic X-point (Tr = −2.041,
residue +1.010); the surviving stable structure is a period-2 island pair
(Z = ±4.1 cm). Its chaos is resolution-robust (43.6% at 1 cm, 41.5% at 0.5 cm), so
it is real — but the configuration is a degenerate, high-shear case with no
axis-based coordinate to build on, not a representative confined equilibrium.

### The physical conclusion

> Vacuum fields contain almost no confined chaos: field lines are either regular or
> escaping. And a configuration with a lot of confined chaos already has a
> collapsed magnetic axis.

The mechanism: no pressure → no pressure-driven current → islands do not grow → the
Chirikov overlap condition is not reached → no stochastic layer forms. Confined
chaos is a product of finite β. Measuring it needs a finite-β 3D equilibrium that
does not assume flux surfaces — that is, HINT.

### What survived — the dynamics axis did transfer to 3D

Not everything failed. Three results held:

- **dig ↔ FTLE correlation** over all 553 confined orbits: r = −0.555 (angle
  increment) → −0.532 (position). Sign and magnitude survived a complete change of
  the dig observable — two independent chaos indicators agreeing. This is the
  genuine cross-validation.
- **WBA as a fast rotation-number method.** It replaced a pyoculus ι calculation
  that had stalled (22 lines, aborted after 183 s): 1656 initial conditions in
  179 s, about 75× faster per line, with ι = 0.21 (axis) → 0.18 (edge) matching the
  paper (0.21 → 0.19).
- **WBA is more sensitive than FTLE across the cantori/edge layer.** In the edge
  band WBA registers irregularity (dig ≈ 2–4) while the FTLE is still ≈ 0.02.

So the verdict on this part is not "failure" but "partial pass": the dynamics axis
transferred to 3D and cross-validated; the transport axis (L_c) failed as a
tautology.

---

## Part 3 — Reduced models: three axes on one stage

Separate repository sv-4 (converse-kam-3d), branch
`claude/converse-kam-3d-y23ijj`. Commits: C0 `e268182`, C1 `c35facb`, C2
`393328e`, C3a `bf5ca06`, C3b v2 `8a52ca1`.

Part 2 showed that a QUASR vacuum field is not a stage on which to measure
transport. Reduced models are: the Kallinikos–MacKay (KMM) and Paul–Hudson–Helander
model fields are built to contain islands, Chirikov overlap, and chaos on purpose,
and they come with an answer key.

**The gap in the literature.** Paul, Hudson & Helander (2022, arXiv:2108.06328):

> "[our metric] appears to be related to the converse KAM approach... We expect
> that the effective volume of parallel diffusion might agree with such a
> calculation in the limit of small perpendicular diffusion. **We reserve such a
> comparison for future publication.**"

Written in 2022; four years on, it has not appeared. This part performs that
comparison. Paul's own §3 argues why the two are related: converse-KAM tests the
non-existence of invariant tori for a given foliation, and V_PD depends on the
topology of the isotherms — the two ask "the same kind of question" in different
languages.

### C0 — two model fields (pass)

**KMM field** (arXiv:2304.09613): with the auxiliary vector field V (V^φ = 1, so φ
is time), Example 1 (integrable) conserves the invariant Ψ = −nψ − m·A_φ to
6×10⁻¹², and the Poincaré section matches its contours. The (2,1) island area,
computed two independent ways, agrees to six digits: S_I(ε=0.004) = 0.4928 — the
answer key for C1. Greene residues R_O = +0.52 (elliptic), R_X = −0.69 (hyperbolic).

**Paul field** (arXiv:2108.06328): the critical-overlap fields m = 4, 12, 36. A
single field line wanders the whole interior (ψ ≈ 0.13–0.87), confirming KAM
destruction; a surviving boundary KAM band and secondary island chains match the
paper's Fig. 6 qualitatively.

### C1 — converse-KAM 3D (pass)

Principle (MacKay 2018; KMM Theorem 3.1): if an invariant surface exists, an
infinitesimal displacement vector cannot rotate across it; so if the displacement
rotates in the forbidden way, no surface of that class passes through the orbit.
This is a proof of *non-existence* — something Poincaré cannot give.

**Check 1 — island-area convergence (KMM Fig. 4):** analytic S_I = 0.4928;
converse-KAM detected area S(t_f=200) = 0.4941; ratio 1.003. The detected region is
exactly the island; core and exterior keep their radial KAM tori.

**Check 2 — first-detection time (KMM Fig. 5):** t_c ~ (π/2)·T/√R, the 1/√R
scaling reproduced to within 10–25%. This is also a cross-check against the residue
code: measure R by residue → predict t_c → measure t_c by converse-KAM → they
agree.

**Check 3 — Examples 2 vs 3 (the seed of C2/C3):** for Example 3 (2/1 + 5/4)
converse-KAM returns two separate rings (inner (2,1), outer (5,4), with KAM
between); for Example 2 (2/1 + 3/2) it returns one broad ring — two islands plus
the chaotic layer between them, all lumped together. This is the limitation KMM
warn about in §4.2: with a radial foliation, converse-KAM cannot separate a torus
inside an island from chaos. In short:

- converse-KAM: island = non-existent, chaos = non-existent → **cannot distinguish**
- WBA: island = regular, chaos = irregular → **distinguishes**
- V_PD: **unknown** — this is the open question

An honest gap: the paper says the symmetry line (Theorem 3.2) halves the
first-detection time; in practice the factor of two is in the timing of the λ
condition (λ < 0 at φ ≈ 12.4, half of the full β-sign-change at φ ≈ 24.5), not in
"use condition (i) only." Read literally, it does not reproduce; recorded, not
tuned. The symmetry-line contamination that bit D1/B2 does not appear here in the
integrable case (symmetry line 0.4866 = 2D grid 0.4866), because inside an island
the tangent rotates monotonically; whether it splits in the non-integrable cases
is left open.

### C2 — converse-KAM on Paul's four fields (commit `393328e`)

This is the core of the project. Paul (2022) argued that m = 4, 12, 20, 36 are all
far from integrability "by traditional measures such as the Chirikov overlap
criterion or converse KAM theory," yet their heat transport is "remarkably
distinct" — i.e. converse-KAM is expected not to distinguish them. Nobody had
checked. Here is the check.

Gate: in Paul coordinates the converse-KAM reproduces the analytic island of a
single-resonance (integrable) field exactly — 2488 cells detected = 2488 cells
inside, no false positives, no misses.

Result (N=160, t_f=200):

| | resonances | area (%) | core ρ∈[.25,.75] | undetected % | median t_c |
|---|---|---|---|---|---|
| m=4 | 3 | 65.9 | 100.0% | 33.7 | 18.9 |
| m=12 | 9 | 71.9 | 99.9% | 27.6 | 19.5 |
| m=20 | 15 | 73.4 | 100.0% | 26.1 | 19.4 |
| m=36 | 27 | 74.0 | 100.0% | 25.5 | 20.5 |

Three readings, all the same direction: (i) in the core (Paul's normalisation band)
all four are 100% non-existent — no distinction where transport lives; (ii) the t_c
distributions are nearly identical (overlapping histograms), so there is no "same
area but different t_c" escape either; (iii) the full-domain area does differ
(66 → 74%), but in the *opposite* direction to V_PD (m=4 has the smallest area yet
the most transport), and the difference comes from island-core and edge geometry,
away from where transport is set. converse-KAM does not distinguish the four in
transport terms. Paul's prediction is confirmed.

Two guards worth noting. The one difference (full-domain area) points the "safe"
way — m=4 smallest, not largest — so it cannot be misread as "converse-KAM predicts
transport"; the result is stronger for not going the way one might have wanted. And
the 25–34% "undetected" is genuine non-detection (boundary KAM plus island cores),
saturated at t_f = 200/400/800 — not read as "undetected = KAM present" (the
"escaped = measurement failure" lesson from Part 2).

### C3a — WBA does not distinguish them either (commit `bf5ca06`)

C2 left a question: converse-KAM misses what V_PD sees; why? Hypothesis:
converse-KAM is blind to cantori, and WBA — which measures how sticky an orbit is —
might see them.

Gate: on a single-resonance Paul field dig_ψ separates cleanly (island interior
dig ≈ 12–14; chaotic sea dig ≈ 1), so the tool works and a core value of dig ≈ 1 is
real chaos, not tool failure.

T-convergence (100 random core ICs, median dig):

| | T=500 | T=1000 | T=2000 | T=5000 |
|---|---|---|---|---|
| m=4 | 1.05 | 1.19 | 1.21 | 1.46 |
| m=12 | 0.93 | 0.93 | 0.83 | 0.97 |
| m=20 | 1.21 | 1.12 | 1.01 | 0.93 |
| m=36 | 1.42 | 1.24 | 1.14 | 0.99 |

At a single T = 1000 snapshot, m=36 is highest — which would have looked like the
"cantori signal," and it was exactly the answer we were hoping for. But it washes
out with T: the curves cross (m=36 falls 1.42 → 0.99, m=4 rises 1.05 → 1.46).
Hypothesis rejected; it was a finite-time artifact. (The residual — m=4 slightly
higher at large T — tracks m=4's larger islands, anti-correlated with transport,
the same sign as C2's area; not a transport signal.)

Verdict: the four core dig distributions are indistinguishable (median ≈ 1, ~90%
chaotic, IQR fully overlapping at every T). WBA does not distinguish the four
either.

And where the two indicators disagree is exactly the islands. At the same points
Spearman(t_c, dig) ≈ −0.14 to −0.21 — weak, and the scatter is L-shaped: on the
chaotic sea both say "chaotic"; inside islands converse-KAM says "dead" (no radial
torus, detected fast) while WBA says "alive" (high dig). This is the §4.2
limitation, made quantitative on Paul's fields.

### C2 + C3a — the conclusion

Same fields, same grid, same core:

| Indicator | Axis | Distinguishes the four? |
|---|---|---|
| converse-KAM | topology | **no** |
| WBA | dynamics | **no** |
| V_PD | transport | **yes** (Paul) |
| heat transport ΔT | — | **yes** (Paul) |

Two topology/dynamics indicators miss what the transport indicator sees. This is
the quantitative form of the summer's thesis:

> Broken is not the same as leaking — and a tool that measures breaking cannot
> measure leaking.

The "why" now has direct evidence. V_PD has since been implemented directly
(C3b, sv-4 `8a52ca1`) — the distinction is confirmed by our own computation, no
longer read off Paul's figures — and the turnstile flux (sv-5, `add102b`)
measures the cantori hypothesis itself: ΔW falls monotonically from m=4 to m=36
over two decades (6.46×10⁻³ → 8.41×10⁻⁵), ordering with the transport metrics
(corr(log ΔW, V_PD) = +0.92, corr(log ΔW, ΔT) = −0.98). The most broken field
has the strongest cantori. That makes the hypothesis supported — not proven, and
the caveat matters: the orbits are trackable only to q ≤ 5 (residues ~200 by
q = 8, so the q → ∞ cantorus limit is not reached), and the q|m resonance rule
forces m=20 onto convergent 2/3 while the others sit at 3/5. The ordering is
robust to both choices. What remains open is the q → ∞ limit — the comparison
Paul left in 2022 is done.

---

## Pitfalls

The most transferable content of the summer is not a number but a set of ways to
fool yourself while measuring. Several were met directly.

**1. Tautology (L_c).** When the classification and the measured quantity come from
the same source, their correlation is not information. More statistics do not fix
it.

**2. Bounded coordinate (Δψ = ι).** When the domain of the measured quantity forces
the answer, the measurement carries no information; here even non-crossing regular
orbits "saturate."

**3. Aliasing (dig).** Sampling below the signal rate distorts it (Nyquist), and it
manufactures a plausible physical pattern ("high ι → high chaos"). Related: an
increment is a derivative, so using increments amplifies trajectory noise and
over-counts chaos (~3×) even without aliasing.

These three share a lesson:

> Do not trust the number; look at the picture. And the prettier the result, the
> sooner you should doubt it.

(L_c looked plausible — caught by re-reading the definition. Δψ looked plausible —
caught by comparing to its domain. dig looked *perfect*, a clean bimodal histogram
— caught by looking at the Poincaré section.)

**4. Interpreting results that were never computed.** A set of numbers (μ = 0.54,
D_m = 4.4×10⁻¹², corr −0.62, with physical interpretation) was supplied without
ever having been run. Rule: do not interpret a result that has no commit hash
behind it.

**5. Grid discretization (non-monotone error).** The island-area grid error
oscillated non-monotonically by ±0.5%. Diagnosis: counting the analytic island
cells with no converse-KAM at all gives the same oscillation — it is 100%
discretization (thin crescent boundaries, 25–27% of island cells, cut differently
by the square grid at each N).

> If the grid error does not decrease monotonically, that is fluctuation, not
> convergence. "The error shrank when I raised N" can be luck.

**6. Finite-time illusion — met three times.** What you see at finite time is not
the infinite-time truth.

- D1 (sv-2/d1): suspected the K=6.9 μ ≈ 1.5 might be finite-time → tracked to
  N=10⁷ → μ stays 2; not an artifact (a stable island). The literature "μ → 1 at
  large N" is the contrast case without a stable island. (Here the trap was
  suspected and ruled out.)
- C2: m=36 chaos looked fragmented at 3000 turns → filled the whole range by 10⁵
  turns (a cantori bottleneck, not fragmentation).
- C3a: at T=1000 m=36's dig was highest → washed out by T=5000. This was the most
  dangerous case, because it was the answer we wanted.

> If the answer you were hoping for appears at finite time, suspect it more.

**7. Missing calibration (a control).** To call dig ≈ 1 "low," you must know what
"high" is; the gate (island interior dig ≈ 12–14 on a single resonance) is that
calibration. Do not interpret a value without a control that shows the tool works.

**8. Fitting the wrong model (sv-1).** Fit a diffusion coefficient where transport
is ballistic and the number explodes (D/D_QL = 618.8 at K=6.4), caught by R²
dropping 0.9998 → 0.97. This is distinct from (6): (8) is a *wrong assumption*
(ballistic, not diffusive) and more time does not fix it; (6) is *too little time*
and more time reveals the truth.

**9. Symmetry line.** Drawing initial conditions from a line contaminates the
sample. Two repositories met it independently: D1 (sv-2/d1) at K=3.0 (grid μ=2 vs
line μ=1); B2 (sv-3) at K=6.9 (θ₀=0 line hits the accelerator island 0 times vs
0.405% on a 2D grid). It does not appear in the integrable converse-KAM case
(sv-4/C1: symmetry line 0.4866 = 2D grid 0.4866).

*(Inherited from earlier projects and listed for completeness: a diagnostic
measuring the wrong axis; a snapshot taken before convergence; Greene-residue
commensurability, where the period chosen annihilates the orbit.)*

---

## Toolkit status

| Axis | Tool | Status |
|---|---|---|
| **Topology** | Poincaré, fixed points, Greene residue | done (3D) |
| | period-doubling detection | done |
| | converse-KAM 3D | done — validated on KMM (island area 0.3%); applied to Paul's fields (C2) → does not distinguish |
| **Dynamics** | WBA | done (2D + 3D + Paul); observable must be position, not angle; applied to Paul (C3a) → does not distinguish; vectorized RK4 gives 200–300× |
| | Lyapunov / FTLE | done (2D + 3D); cross-validated with WBA (corr = −0.53) |
| **Transport** | diffusion exponent μ (standard map) | done (sv-2/d1) |
| | 3D vacuum | not possible in principle (nothing to measure) |
| | V_PD | done (C3b, sv-4 `8a52ca1`) — three-axis comparison complete; V_PD/ΔT distinguish the four fields where converse-KAM and WBA do not |
| | turnstile flux ΔW | done (sv-5 `add102b`) — orders with transport; cantori hypothesis supported to q ≤ 5 |
| **Field** | QUASR vacuum | done |
| | KMM model (3 examples), Paul model (m=4/12/36) | done (C0) |
| | finite β (HINT) | not done — Hiroshima |

The topology axis is now complete in 3D — a slot that was empty all summer.

## Next steps

C3b is done: V_PD was implemented (∇·(κ·∇T) = 0, κ∥/κ⊥ = 10⁵–10⁶; ill-conditioned
— Paul used PETSc + MUMPS) and the three-axis comparison converse-KAM ↔ WBA ↔ V_PD
completed on the same fields, grid, and core (sv-4 `8a52ca1`), finishing what Paul
left in 2022; the turnstile flux (sv-5 `add102b`) then measured the cantori
hypothesis directly — supported to q ≤ 5. What remains:

1. The q → ∞ limit of the turnstile flux. It is measured only through q ≤ 5
   (residues ~200 by q=8, so the cantorus limit is not reached), and the q|m
   resonance rule puts m=20 on convergent 2/3 while the others use 3/5; following
   ΔW to convergence is open.
2. An island-centred foliation (KMM §5) so converse-KAM can separate island from
   chaos — which would complete the topology axis; C3a's L-shaped scatter shows
   quantitatively why it is needed.
3. A finite-β equilibrium (HINT). Every field here is prescribed and confined
   chaos is a product of finite β, so the next step is not another tool but
   pressure-driven currents.

---

## Where this stands

The literature stops in the same place. Punjabi & Boozer (2022): turnstile
robustness to current perturbation — open. Davies et al. (2025): β-robustness of
unpaired X-points vs island chains — future work. Suzuki (2020): equilibrium with
net toroidal current — future work. Zhang, Suzuki et al. (2026): self-consistent
bootstrap — future work. Paul et al. (2022): converse-KAM vs V_PD — four years
untouched. All of them stop at "what happens when current comes in."

This summer reached a wall in front of that one:

> In a vacuum there is nothing to measure. Confined chaos is a product of finite β.

So the next step is not another tool but a finite-β equilibrium — HINT.

The three axes do not substitute for one another, and that is the through-line. D1
and B2 pointed at the same island by different principles; in Part 2 WBA saw the
cantori/edge layer before FTLE; in Part 3 converse-KAM cannot separate island from
chaos while WBA can, and neither sees what V_PD sees. If all three always agreed,
one would suffice; they diverge, which is why all three are needed — and
quantifying that divergence is C3.

And what the toolkit still cannot do: a magnetic field alone does not determine
transport. For the same field, the plasma state (density, temperature) sets the
mean free path λ_e, which changes the diffusion coefficient by orders of magnitude
(Vlad et al. 2002).

> L_c is only the numerator. The denominator comes from the plasma.

So a "topology + dynamics → transport" prediction is incomplete in principle — and
measuring the size of that incompleteness is exactly the V_PD vs converse-KAM
comparison.

---

*A Korean version of this document is preserved at
[`summer-toolkit-three-axes.ko.md`](summer-toolkit-three-axes.ko.md).*
