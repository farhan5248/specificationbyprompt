---
title: Are We There Yet? — Reading the Pair-Range Charts
---

* TOC
{:toc}

---

# Context

A working-session summary of how to read the Darmok pair-range charts and decide whether
the harness is "done" — i.e. stable enough that further tweaking would be tampering, and the
charts can be repurposed from *improvement tracking* into a *bad-input detector*.

This is the reasoning behind the per-run pair-range tabs in the
[metrics spreadsheet](https://docs.google.com/spreadsheets/d/1KcIrn1Cils0cyFS_E2aekstF82zo2_SIhjKqwmQruCA/edit),
companion to the [pbc-NNNN case studies](pbc-research) which diagnose individual runs.

# What The Data Showed

Across the tracked pairs (chronological), both the mean and the standard deviation of the
per-scenario pair-ranges dropped ~10×:

| Pair | range_mean (ms) | range_stdev (ms) |
|---|---|---|
| 44/45 | 114,548 | 287,354 |
| 65/66 | 37,832 | 117,932 |
| 67/68 | 29,747 | 87,844 |
| 69/70 | 19,158 | 40,872 |
| 72 vs 69–70 | 11,365 | 25,568 |

The improvement was **not** the process settling on its own. Each drop was a deliberate
harness change that removed an assignable cause we had identified. The decline is a
staircase of intentional step-downs, not common-cause drift.

# The Metric: Pair-Range, Not Run-Time

Every test case is run **twice** through Darmok. A single run-time on its own is not useful;
the useful quantity is the **pair-range** = `|green_A − green_B|`, the disagreement between
two Claudes processing the same test case.

- Run-time is **heterogeneous** across scenarios (≈150k ms vs ≈400k ms baselines) — you
  cannot pool it onto one chart or compute one set of limits for it.
- Pair-range **strips the scenario baseline out** (it's a difference), leaving only the
  harness's run-to-run reproducibility. That *is* homogeneous, so all scenarios go on one
  chart. No per-scenario chart is needed.

This corrected an earlier wrong instinct ("you need a chart per scenario") — that applies to
charting *time*, not *range*.

# The Statistics: Use the Moving Range, Not 3·SD

The chart uses Wheeler's XmR method, **not** mean ± 3·(standard deviation):

- UCL = `range_mean + 2.66 · MR_bar`, where `MR_bar` is the average moving range. The 2.66
  already embeds the 3-sigma equivalent. (Verified on the 6-run tab: `75,266.75 + 2.66 ·
  18,836.5 = 125,371.84`.)
- Outliers are **excluded from the limit calculation** but still plotted (`exclude_from_limits`
  column).

Why not 3·SD on the column of values? Because the global standard deviation captures *all*
variation — including the very shifts and trends you are trying to detect — so it inflates the
limits and hides signals. Our history is full of exactly that contamination: five deliberate
step-downs.

**Does the moving range hide signals too?** Yes, but by a *different* mechanism, and a much
less damaging one here:

- Global SD is wrecked by **sustained shifts** (which we have a lot of). It averages all five
  regimes into one fat band.
- MR is blind to sustained shifts (a shift touches only one moving range) but **can** be
  inflated by an **isolated spike** (one spike produces two large moving ranges). Defenses:
  use the **median** moving range, or screen obvious outliers out of the limit calc.

No estimator gives contamination-proof limits — the limits are always estimated from data
that may contain signals. The art is choosing the estimator least sensitive to the
contamination you actually have. Ours is sustained shifts, so MR wins.

A consequence of the staircase: **you cannot compute one set of limits across runs 44→72
with any method.** Each harness change is a known process change; you re-baseline after it.
The only valid baseline is the runs *since the last change*.

# The Decision: Done, or Tampering?

- **Tampering** (Deming's funnel) = adjusting a stable process in response to common-cause
  noise — it *adds* variation. It's only tampering if the process is already in control.
- The chart is what licenses the "stop" decision: freeze the harness, run the same tests
  ~6 more times with fixed inputs, and if all points sit inside the MR-based limits with no
  signals, stability is *proven* and further harness changes would be tampering.
- If a signal remains, that's one more assignable cause worth removing — and removing a
  *named, reproducible* cause is improvement, not tampering.

# Repurposing The Chart As An Input Detector

Once stable, the chart's job flips from tracking improvement to **detecting bad inputs**. The
pair-range scales with the *inputs*, so departures from the calibrated envelope are signals:

- **Order:** processing a negative test case before its corresponding positive one makes
  Claude infer the wrong thing, think longer, and the pair-range rises. The detector is
  *supposed* to fire on this. Baseline must be built on correctly-ordered pairs.
- **Size:** the pair-range **scales with test-case size** — a bigger case means more to infer,
  more searches, more judgement, so more variation. A large range is therefore a signal to
  **keep test cases smaller**.

So the chart is a **size/order envelope detector**, not a universal constant. "A pair differs
by ~30s" holds *within the intended envelope* (small, correctly-ordered cases); departures
are the signals.

**Discipline that follows:** the limits must be computed **only from in-envelope cases**. If a
baseline scenario is itself oversized or mis-ordered, it inflates `MR_bar` and the UCL and the
detector goes blind to the next bad input. That is what `exclude_from_limits` is for — the
excluded scenarios (the 581k `valid object name`, the 446k/523k `doesn't exist`) are the
big-variation ones. The UCL is not a law of nature; it encodes "test cases the size/shape of
the current corpus, ordered positive-before-negative." State the corpus the limits were built
on.

# Open Item: The Warm-Up Position Effect

We are **not** certain we're done. The **first** test case in a run is always the highest — it
does the heaviest lifting to create the test automation for new functionality (a "warm-up"
event). Its magnitude is driven by test-case size. Two levers reduce it:

1. **Break it into two simpler tests** (reduce size), or
2. **Improve the UML / interaction-main spec** (`test.md` etc.). Not everything is inferable
   from a test: Claude can understand the desired *outcome* but still has to figure out *how
   to code it*. When the gap is in the "how," the defect is **spec quality**, not test quality.

Statistical consequences:

- The warm-up is a **systematic position effect**, not noise — it recurs at the same position
  every run. **Do not pool position-1 ranges with the steady-state ones**, or you inflate the
  limits / create a permanent un-closable signal. **Stratify**: warm-up gets its own
  baseline; its chart then tells you whether splitting the test or improving the spec actually
  moved it.
- Attacking the warm-up is **improvement, not tampering**, because it's a named, reproducible
  cause sitting outside the steady-state band. Stop when it settles inside the steady-state
  limits.

# Division Of Labour: Chart Detects, Transcript Diagnoses

The chart flags that "the process worked harder here." It cannot tell you *which* lever to
pull. Whether the cause is **size** (→ split the test), **order** (→ fix the sequence), or a
**how-to-code gap** (→ fix the UML interaction spec) is a root-cause question answered by
reading the session JSONL — which is what the [pbc case studies](pbc-research),
`rgr-review-run`, and `rgr-review-commit` do.

- **PBC / pair-range chart = detector.**
- **JSONL walk = diagnosis**, routing to one of: test-size, order, or spec.

The key new axis: the **test** says *what* outcome is wanted; the **UML / interaction spec**
says *how* to build it. When Claude knows the what but improvises the how, the variation is a
spec defect, not a test defect — and only the transcript distinguishes them.

[pbc-research]: pbc-research
