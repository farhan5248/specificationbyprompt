---
title: PBC for Claude Code - Research
---

* TOC
{:toc}

---

# Context

This page is the detailed companion to [A Process Behavior Chart for Claude Code][1]. The post argues that variation across Claude Code runs, when measured against a deterministic harness, becomes a control chart for specification quality rather than a measure of model noise. This page shows the data behind that claim.

The research has two parts, each detecting a different kind of specification problem:

1. **Ambiguous test detection**, by studying *pairs* of Claude runs against the same input.
2. **Contradiction detection**, by studying *individual* runs.

---

# Part 1: Ambiguous Test Detection (Pair-Run Analysis)

*To be written.*

Planned content:

- The setup: two independent Claude sessions, same GitHub issue, same tests, isolated work-trees.
- The metric: wall-clock time delta between paired runs.
- The control limits: how the average and standard deviation are computed across the historical pair set.
- The signal: pairs whose time delta exceeds three standard deviations.
- Worked example: a pair where the signal fired, the functional difference Claude expressed as a test, and which work-tree passed it.
- Charts from Google Sheets and Grafana panel screenshots.
- Logs from the two runs side-by-side.

---

# Part 2: Contradiction Detection (Single-Run Analysis)

*To be written.*

Planned content:

- The setup: a single Claude run against a test that conflicts with existing functionality.
- The metric and what its in-control behavior looks like.
- The signal that distinguishes "Claude is struggling" from "the specification is contradictory."
- Worked example with logs.
- Charts.

---

# Method Notes

Working notes on the XmR chart methodology, captured as the questions came up while building the spreadsheets. Wheeler's *Understanding Variation* and Deming's *Out of the Crisis* are the references behind these.

## Range vs Moving Range

Two different views of variation, and they answer different questions.

**Range** is *within* a single point. It's the spread across the measurements that make up that one data point. In the combined `metrics_combined_3540` chart, range is `max(green35..green40) − min(green35..green40)` for one scenario. It tells you how much the 6 runs disagreed about *that* scenario.

**Moving Range (MR)** is *between* consecutive points. It's the spread between this row's metric and the previous row's metric — `|range_row_n − range_row_{n-1}|`. It tells you how much the chart's metric jumped from one scenario to the next.

A different way to say it: range is "how much variation inside a subgroup," MR is "how much the subgroup-level number drifted as you moved through the sequence."

Why MR is used to set the limits: Wheeler's argument is that successive-pair differences are the cleanest estimate of common-cause noise. They only span one sampling interval, so they're not contaminated by long-run drift or by individual special causes the way an overall variance would be. `mean(MR) × 1.128⁻¹` is roughly the standard deviation of the underlying process if it's stable — but it's a more honest estimate when the process is *not* stable, which is when you most need the chart.

Why range is the *charted metric* in this case: with 6 measurements per scenario, range is a natural single-number summary of how much disagreement exists across the runs for that scenario. High range means the scenario where the runs diverged. Low range means the scenario where the runs agreed. Divergence *is* the signal for "ambiguous test," not central tendency.

A purist note: with 6 replicates per scenario, an X-bar/R chart pair (mean chart plus range chart) is technically more orthodox than collapsing to "range as the X variable on an XmR." For this story — "did the runs agree on this scenario?" — range-as-X is the right framing.

## Why High-Range Points Don't Always Cross UCL

The first time you build one of these charts, you expect intuitively that the highest-range points should be the ones that cross the upper control limit. Sometimes they don't, and the reason is structural rather than statistical.

Range is *one* data point. It's the metric for a single scenario. By itself it can't "cross UCL" any more or less likely just because it's high — it's a single number relative to a threshold.

`MR_bar` is *all* data points. It's an aggregate over the chart. So when you have one high range, it's a single point that may cross UCL. When you have *several* high ranges, two things happen:

1. Each high range is individually still a candidate to cross UCL.
2. Each high range also creates a big jump *into* and *out of* itself in the MR column. Those jumps inflate `MR_bar`. Which raises UCL. Which makes it harder for any of them to cross.

That's the trap. The chart self-defends against signal when special causes are common in the data being used to set its limits. Add a 200,000 ms range outlier, and the limit moves up by roughly 530,000 ms (the multiplier is 2.66) — much further than the outlier itself. The bigger the outlier, the further the bar moves out of reach.

This is why Wheeler insists on the iterative-limit pass. The first computation gives provisional limits that are too loose because they include the special causes. Identify the obvious outliers, mark them excluded from the limit calculation but keep them on the chart, recompute. The bar drops. More points may now flag. Repeat. The limits converge to "what variation looks like when the process is behaving."

The mental shift: the UCL isn't a description of what's normal in your data. It's a description of what would be normal *if the process were stable*. The job of the chart is to find the points that betray instability — and those points have to be removed from the limit calculation for the chart to keep doing its job. The `exclude_from_limits` boolean column in each metrics TSV is what implements this.

## When to Stop Iterating

Two stopping rules, used together. Wheeler is firm that neither alone is sufficient.

**1. The mathematical stopping rule.** After a recompute, no new points cross UCL. The chart has converged: the remaining points all sit inside limits derived from themselves. If you exclude another one, the limits tighten and a new "outlier" appears, and the cycle never ends. That cycle is the tell that you've crossed into common-cause territory and started chasing noise.

**2. The story stopping rule.** Every point you exclude must have a plausible assignable cause — a specific, identifiable reason why this point is categorically different from the others. "First scenario sets up the test automation framework" is a story. "The preceding scenario over-engineered the implementation" is a story. "Network was flaky that day" is a story. "It just looks high" is not a story — it's pattern-matching on noise, and that's where Deming says you start tampering.

Wheeler's discipline: before excluding a point, you should be able to point at *what changed* about the process at that point. If you can't, the point belongs in the limits, no matter how much it looks like an outlier. He says this explicitly because the seductive thing about iterative limits is that every dataset has an extremum — you can keep tightening forever — and at some point you're just relabeling common-cause variation as special cause.

Three concrete checks I apply when working through a chart:

- Does an excluded point have a one-sentence cause that wouldn't apply to the others? "Heavy setup work for the first scenario" passes. "It's the largest range" fails.
- Are you excluding more than ~10–15% of the data? If yes, you're probably looking at two distinct processes that should be charted separately, not one process with many special causes.
- Have you reached the math stopping rule (no new flags after recompute)? If not, keep going. If yes, stop even if you suspect more is special — the chart can't tell you anything more without additional context.

One extra signal worth knowing: if the remaining in-control points show a *pattern* — a run of 8 above the centerline, a steady drift, a sawtooth — that's also a special-cause signal even though no individual point crosses UCL. Western Electric rules formalize this. With ~12 points the data is too thin to reliably detect such patterns, but it's a reason not to over-tighten. Give the process room to reveal patterns rather than forcing every fluctuation to be a flag.

The honest answer to "how do you know you're not chasing common cause" is: you don't, with certainty. The discipline is to require a story for every exclusion and stop when the math converges. If both rules are satisfied and more still seems hidden, the answer is "collect more data," not "tighten the limits."

---

# Open Questions

- Common cause variation is mostly unexplored. The width of the common-cause band may itself be a measure of how tight the specification language is.
- How does the chart behave when the underlying harness changes (new DSL grammar, new test automation patterns)? Is there a re-baselining protocol?
- Can the reverse-prompt be generated automatically when a special-cause point is detected, so the tester gets feedback without a developer in the loop?

---

[1]: pbc-for-claude-code
