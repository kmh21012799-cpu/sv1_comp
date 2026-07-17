# Magnetic Field Line Chaos — Summer 2026

**Kim Minhyuk (김민혁), Sejong University**

Reproduction and verification across six repositories. No new physics is claimed;
the value is in the measurement discipline and in one negative result, stated
plainly.

## One question

> **When a magnetic field breaks, does the heat leak out?**
>
> **Asked twice. Both times the answer was: the field alone cannot tell you.**

## Three axes

None of them can stand in for another.

| Axis | The question it asks | Tools |
|---|---|---|
| **Topology** | Is there a wall (a flux surface)? | Poincaré, Greene residue, converse-KAM |
| **Dynamics** | Is the orbit regular or chaotic? | Weighted Birkhoff Average (WBA), Lyapunov/FTLE |
| **Transport** | Does heat actually leak? | diffusion exponent μ, effective volume V_PD |

## The narrative

**→ [docs/summer-toolkit-three-axes.md](docs/summer-toolkit-three-axes.md)**

Three parts and nine pitfalls. (A Korean version is at
[docs/summer-toolkit-three-axes.ko.md](docs/summer-toolkit-three-axes.ko.md).)

## Repositories

### [sv-1](https://github.com/kmh21012799-cpu/sv-1) — Standard map: diffusion coefficient D(K)

Measures the diffusion *coefficient* D(K) against Rechester–White quasilinear
theory. In the accelerator window (2π < K ≲ 7.45) transport is ballistic, and a
diffusion fit gives a large, meaningless number — D/D_QL = 618.8 at K = 6.40 —
caught by the fit R² dropping from 0.9998 to ~0.97. The Lyapunov exponent passes
smoothly through the window that spikes D: a dynamics indicator does not see the
transport anomaly. (A separate study from D1; sv-1 measures the coefficient, D1
the exponent.)

### [sv-2](https://github.com/kmh21012799-cpu/sv-2) — QUASR vacuum configurations (Parts 1–2)

**Part 1** reproduces Table A1 of Davies et al. (2025) on configuration 104183 to
three significant figures (Greene residue −1.932 vs −1.93; Lyapunov 2.264 vs 2.26),
and finds a paper typo (label 74608 does not exist; the body text means 74609).
**Part 2** carries the three axes to 104183 and reports three failures (tautology,
bounded coordinate, aliasing). Corrected, 104183 has **0 of 553** confined orbits
chaotic; a scan of 40 random vacuum configurations finds only **1 of 25**
exceeding 10% confined chaos — and that one has no magnetic axis. The conclusion:
vacuum fields contain almost no confined chaos; it is a product of finite β. This
repository also contains **D1** (`d1_standard_map/`), the diffusion *exponent* μ(K)
study.

### [sv-3](https://github.com/kmh21012799-cpu/sv-3) — Weighted Birkhoff Average (WBA)

Implements the WBA of Duignan & Meiss (2023) as a regular/chaotic classifier
(normalisation constant C(w=1) = 142.2503757771, matching the paper). At K = 6.9 it
flags the accelerator island as regular (median dig = 10.43, area 0.405%), and
agrees with the Benettin Lyapunov classifier on **99.99%** of 14,400 initial
conditions — the genuine independent cross-check.

### [sv-4](https://github.com/kmh21012799-cpu/sv-4) — converse-KAM 3D + V_PD (Part 3)

Implements converse-KAM in 3D (validated on the KMM island: detected area 0.4941 vs
analytic 0.4928, ratio 1.003) and applies it to Paul's four critical-overlap fields
(m = 4, 12, 20, 36). In the transport-relevant core all four are 100% non-existent
— converse-KAM cannot distinguish them; WBA cannot either. Yet V_PD and heat
transport do. This performs the converse-KAM vs V_PD comparison that Paul, Hudson &
Helander (2022) reserved "for future publication" and left untouched for four years.

### [sv-5](https://github.com/kmh21012799-cpu/sv-5) — Turnstile flux (ΔW)

Measures the turnstile flux of MacKay–Meiss–Percival (1984) as the action
difference ΔW = W_minimax − W_minimizing of the periodic orbits at noble
convergents. Validated on the standard map — flux ≈ 0 below K_c ≈ 0.9716 and
positive above, Greene's residue criterion reproduced, flux-scaling exponent
β ≈ 2.85 against MacKay's ≈ 3.0 — then applied to Paul's four fields: ΔW falls
monotonically over two decades from m = 4 to m = 36 (6.46×10⁻³ → 8.41×10⁻⁵),
ordering with the transport metrics (corr(log ΔW, V_PD) = +0.92,
corr(log ΔW, ΔT) = −0.98). The most broken field has the strongest cantori. This
makes the cantori hypothesis **supported, not proven**: the orbits are trackable
only to q ≤ 5 (residues ~200 by q = 8, so the q → ∞ cantorus limit is not
reached), and the q|m resonance rule puts m = 20 on convergent 2/3 while the
others use 3/5.

## The result

Both times the question was asked — in the standard-map accelerator window (sv-1)
and on Paul's four fields (sv-4) — the topology and dynamics indicators missed what
the transport indicator saw.

> **Broken is not the same as leaking — and a tool that measures breaking cannot
> measure leaking.**

The reason is the same in both places: those indicators ask "is this locally
chaotic," not "where does the heat go." Transport is set by global structure, and
by the plasma state that a vacuum field does not contain.

As for *why* converse-KAM misses it, there is now direct evidence: the turnstile
flux (sv-5) shows that the fields converse-KAM cannot tell apart are cleanly
separated by cantorus strength, and that strength orders with the transport. The
cantori hypothesis is supported (to q ≤ 5) — the remaining open problem is the
q → ∞ limit, not whether the comparison can be made.

## What remains

- **The q → ∞ limit.** The turnstile flux is measured only through q ≤ 5 (at
  critical overlap the q = 8 convergent orbits have residues ~200); following ΔW
  to the cantorus limit — and resolving the q|m resonance constraint that puts
  m = 20 on a different convergent (2/3) than the others (3/5) — remains open.
- **An island-centred foliation** (KMM §5) would let converse-KAM separate island
  from chaos, completing the topology axis.
- **Finite β.** Confined chaos is a product of finite β, so measuring transport
  needs a finite-β 3D equilibrium that does not assume flux surfaces — that is,
  HINT.
