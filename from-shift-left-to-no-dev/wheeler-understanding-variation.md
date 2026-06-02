---
title: Wheeler - Understanding Variation
---

* TOC
{:toc}

---

# The Hunch

A harness that lets a tester drive Claude has to give a *reverse prompt* — feedback when an input is too vague, too big, or contradictory — without crying wolf on ordinary noise ([Goldratt - Diminishing A Limitation][9] is why that matters). I wanted that signal to be more deterministic than running two Claudes and eyeballing the difference, rather than relying on AI to audit itself.

A couple of weeks ago I was on John Willis's [Profound][3] podcast. At the time I was eyeballing Plan-Do-Study-Act cycles to see whether a Process Behavior Chart — Wheeler's tool, the same one used on a manufacturing line — could be put on Claude's runs. The bet is that a clear, well-sized test, run twice, produces comparable work both times, so a *wide* gap between the two is a signal — and, like the red bead game, that the variation lives in the **system** (the harness, the tests, the order they're given in) rather than in the worker.

This page defines that experiment: *which* charts to put on Claude's runs and *why those* rather than the obvious alternatives — mapping Wheeler's method onto this setup. It deliberately holds no results. The worked examples, where a real run crosses a limit and gets diagnosed, live in [Darmok - Harness for Claude Code][8]. This page is the approach; that one is the evidence.

# What We Measure

Every test case is run **twice**. A single run's wall-clock time is not very useful on its own; the useful quantity is the **pair-range** — the absolute difference between the two runs' green-phase times for the same test. Two Claudes implementing the same clear, well-sized test should take roughly the same time; when they don't, the gap is the signal.

The pair-range is charted **pooled across all the scenarios**, on one chart, even though the scenarios have very different absolute durations. That is legitimate because the *range* strips the per-scenario baseline out — it is a difference, so the scenario's inherent size cancels, leaving only the run-to-run reproducibility of the harness. (Charting the raw *times* this way would not work; those are heterogeneous and would have to be charted per scenario.)

For the pair-range to measure only reproducibility, the two runs must share everything but the worker's nondeterminism — same parent commit, same harness version, and ideally the same wall-clock window, so a slow-server night can't inflate one half (see Open Questions / [#153](https://github.com/farhan5248/sheep-dog-main/issues/153)).

# Two Signals, One Source: the Input

Two charts that look like two different problems but point at the same place — the input.

- **The pair chart** flags a test whose two runs diverge far more than the rest. The cause can be an **ambiguous** test — the spec admits more than one valid implementation, both runs pass, but they build different things — or a test that is perfectly **clear and simply too big**: more to infer, more to search, more judgement, and that extra solution space is what makes the two runs wander apart. Either way the lever is the *test*: disambiguate it, or split it smaller.

- **The single-run chart** flags a test whose one run balloons past the upper limit — **contradiction or thrash**: the new test conflicts with what already passes, so Claude goes back and forth, fix the new test and break an old one, paying the per-iteration build tax over and over. This signal is **defined but not yet exercised**: the test suite is deliberately cleaned so no test contradicts existing behavior, so the situation can't currently arise (the one contradiction I saw happened before I started tracking data). It is here for completeness, to be revisited once the model-based-testing tool can generate test cases on demand and create the situation deliberately.

The unifying point that makes the whole approach worth it: **a point outside the limits is a signal to change the input, not the worker.** A too-big, ambiguous, contradictory, or badly-ordered test creates more variation than any amount of tuning the harness could remove.

# Why the Moving Range, Not the Standard Deviation

This is the load-bearing methodological choice, and the one most people get wrong. The limits are **not** the mean ± 3 × the standard deviation of the data. They come from the **average moving range** — the mean of the absolute differences between successive points:

- Upper limit (individuals) = mean + 2.66 · mR̄, where mR̄ is the average moving range. The 2.66 already embeds the 3-sigma equivalent.
- Upper limit (the moving-range chart itself) = 3.27 · mR̄.

The reason is that the global standard deviation is **contaminated by the very signals you are trying to detect**. A sustained shift or a trend inflates the global SD enormously, which widens the limits, which then hides the next signal. The moving range only spans one sampling interval, so it estimates the short-term, common-cause noise cleanly and is largely blind to long-run drift. Wheeler's argument is that mR̄ is the *more honest* estimate precisely when the process is *not* stable — which is when you most need the chart.

This is also why the limits must be **re-baselined after every change to the harness**: a series that spans several deliberate harness versions is several different processes stacked together, and a global SD over the whole span is meaningless. Limits come only from the runs since the last change, never across one.

# Why High-Range Points Don't Always Cross the Limit

The first time you build one of these charts you expect the highest-range points to be the ones above the upper limit. Often they aren't, and the reason is structural, not statistical.

A single high range is one data point against a threshold. But each high range *also* creates a big jump into and out of itself in the moving-range column, and those jumps inflate mR̄, which raises the limit — by more than the outlier itself. **The chart self-defends against signal when special causes are common in the data setting its own limits.**

This is why Wheeler insists on the **iterative-limit pass**: the first computation gives limits that are too loose because they include the special causes. Mark the obvious outliers as excluded from the *limit calculation* but keep them plotted, recompute, and the limit drops so more points can flag. Repeat until it converges. The limit is not a description of what's normal in your data — it's a description of what *would* be normal if the process were stable. An `exclude_from_limits` flag is what implements this.

# When to Stop Iterating

Two stopping rules, used together — neither alone is enough.

1. **The mathematical rule.** After a recompute, no new points cross the limit. The chart has converged; excluding another point would just tighten the limits and manufacture a new "outlier" forever. That endless cycle is the tell that you've crossed into common-cause territory.
2. **The story rule.** Every excluded point must have a plausible, specific assignable cause — *"the first scenario sets up the test framework"* is a story; *"it just looks high"* is not. If you can't name what changed about the process at that point, it belongs in the limits no matter how extreme it looks.

Three checks: does an excluded point have a one-sentence cause that wouldn't apply to the others? Are you excluding more than ~10–15% of the data (if so you're probably looking at two processes that should be charted separately)? Has the math converged? And one pattern signal worth knowing even when nothing crosses the limit: a run of points all on one side, a steady drift, or a sawtooth is itself a special-cause signature (the Western Electric rules).

This is where **tampering** gets its precise meaning. Once the process is in control, continuing to investigate points *inside* the limits — or tweaking the harness to chase them — is tampering (Deming's funnel): it adds work and tends to *increase* variation. The honest answer to "am I sure this is common cause?" is that you require a story for every exclusion and stop when the math converges; if more still seems hidden, the answer is "collect more data," not "tighten the limits."

# Re-baselining and the Input Envelope

Once the chart shows the harness is in control (the evidence for that is in [Darmok - Harness for Claude Code][8]), the chart's job flips from *find the next harness bug* to *find the bad input*. The limits then encode "test cases the size and shape of the current corpus, ordered so each is a small step from the last" — they are not a law of nature. So the limits must be computed from **in-envelope cases only**; a baseline that includes an oversized or mis-ordered test inflates mR̄ and blinds the detector to the next one. The first test in a run is a known **warm-up** (it creates the test-automation scaffolding), a systematic position effect that should be **stratified** onto its own baseline rather than pooled with the steady-state tests.

# The Chart Detects; the Transcript Diagnoses

The chart only ever says "the process worked harder here." *Which* input lever to pull — disambiguate the spec, split a too-big test, fix the ordering, or enrich the UML spec where the test says *what* but not *how* — is a root-cause question answered by reading the run's JSONL transcript, not by the timing. The division is strict: **chart = detector, transcript = diagnosis.** The case studies in [Darmok - Harness for Claude Code][8] are that diagnosis worked through, one run at a time.

# Open Questions

- **The common-cause band itself.** Its width may be a measure of how tight the specification language is. The remaining floor is the model's sampling nondeterminism plus server-side time-to-first-token / decode-rate noise — uncontrollable from any input. (Thinking time *does* scale with the input — a bigger test means more to infer — but within a pair the input is identical, so that cancels; what doesn't cancel is the decode-rate swing between two runs taken at different times of day.) The fix is to kill that confound at the source rather than normalize it out afterward: run the two halves of a pair **concurrently from the same commit** ([#153](https://github.com/farhan5248/sheep-dog-main/issues/153)), so a slow-server night hits both equally and cancels in the range.
- **Exercising the single-run chart.** The contradiction signal can't be triggered with the current cleaned test suite; generating tests on demand to create it is part of the model-based-testing work below.
- **Can the reverse-prompt be generated automatically** when a point crosses the limit, so the tester gets "your test admits two implementations / is too big" feedback without a developer in the loop?
- **Rational subgrouping of the inputs.** A model-based-testing tool (GraphWalker) that controls path size and ordering would make the work units uniform by construction — generating bounded, similar-sized paths instead of the wildly unequal ones a minimal path cover yields. That is system work on the *inputs* (redesigning the input population), not tampering with the harness. The controlled length-vs-thinking-time data this assumes doesn't exist yet; producing it is exactly what that work is for. Today the relationship is only observational (the order/size effect in [Deming - Building Quality In][1] and the net-new-logic ranges in [Darmok - Harness for Claude Code][8]).

---

[1]: deming-building-quality-in
[3]: https://www.buzzsprout.com/1758599/episodes/18909101
[8]: darmok-harness-for-claude-code
[9]: goldratt-diminishing-a-limitation
