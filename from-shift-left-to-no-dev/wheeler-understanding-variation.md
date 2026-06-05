---
title: Wheeler - Understanding Variation
---

* TOC
{:toc}

---

# The Hunch

A harness that lets a tester drive Claude has to give a *reverse prompt* — feedback when an input is too vague, too big, or contradictory — without crying wolf on ordinary noise ([Goldratt - Diminishing A Limitation][9] is why that matters). I wanted that signal to be more deterministic than running two Claudes and eyeballing the difference, rather than relying solely on AI to audit itself.

When I started, I was eyeballing Plan-Do-Study-Act cycles to see whether a Process Behavior Chart could be put on Claude's runs. The bet is that a clear, well-sized test, implemented twice, produces comparable work both times, so a *wide* gap between the two is a signal — and, like the red bead game, that the variation lives in the **system** (the harness, the tests, the order they're given in) rather than in the worker.

This page defines that experiment: *which* charts to put on Claude's runs and *why those* rather than other alternatives — mapping Wheeler's method onto this setup. It deliberately holds no results. The worked examples, where a real run crosses a limit and gets diagnosed, live in [Darmok - Harness for Claude Code][8]. This page is the approach; that one is the evidence.

# What We Measure

Every test case is run **twice** — two git worktrees, A and B, running concurrently from the same parent commit ([#153](https://github.com/farhan5248/sheep-dog-main/issues/153)) so the two halves share everything but the worker's nondeterminism. From the two green-phase times we derive a small set of variables. Naming them precisely matters, because *green time*, its *moving range*, the *pair range*, and the *pair range's moving range* are easy to confuse.

| Variable | Definition | In control / stable | Above the UCL |
|---|---|---|---|
| **green a / green b time** | green-phase wall-clock in worktree A / B | *raw inputs* | — |
| **green tokens** | output tokens emitted in the green phase | feeds the `scale`; **the A/B token difference is itself a reproducibility axis** — equal tokens across a pair ⇒ a wide *time* pair range is real input variance not server; *unequal* tokens across a pair with a *narrow* time range ⇒ a real work divergence the wall-clock hid | — |
| **scale** | per-test factor (from tokens) normalizing green time for the day's server decode rate; starts at 1, set once a run is reviewed | comparable runs map to ~the same scale | — |
| **green min time** | min(green a, green b) — the cleaner of the pair | *intermediate* | — |
| **green time** | scaled green min time (green min × scale); charted as individuals **over the ordered sequence** | a normal-sized step for its place in the sequence | **under the timeout → too big / under-specified test** (Claude has to infer — e.g. a Given/When/Then needing more than ~3 steps it wasn't given); **at the timeout → contradiction, or — likelier now — Claude building a forbidden dependency in-project** because a tester can't change project deps |
| **green time moving range** | \|green time₍ₙ₎ − green time₍ₙ₋₁₎\| | consecutive tests step up by a similar amount — a well-graded sequence | this test was **too big a leap** from the previous one |
| **green time pair range** | \|green a − green b\| (raw — server cancels) | the two worktrees agree — a **clear / well-specified** test | the worktrees diverged — a **vague / ambiguous** test (or one run got lucky) |
| **green time pair range moving range** | \|pair range₍ₙ₎ − pair range₍ₙ₋₁₎\| | run-to-run jitter is **consistent** — the steady noise floor for the project at that time of day | jitter **jumped** for this test — flags it even if its raw pair range looked acceptable |

Two things to fix in your head before the charts:

- **`green time` is the *scaled* lower of the pair.** Take `green min time` = min(green a, green b) — the cleaner run — and multiply by the per-test `scale` derived from `green tokens`, which normalizes for the server's decode rate that day so green times are comparable across runs.
- **The pair range needs no scaling.** Because A and B run concurrently on the same commit, the server is identical for both, so `green time pair range` is pure run-to-run reproducibility — the server cancels. (This is what [#153](https://github.com/farhan5248/sheep-dog-main/issues/153) bought: the slow-server-night confound is gone from the pair.)

The pair range is charted **pooled across all the scenarios** — the *range* strips each scenario's inherent duration out (it's a difference), leaving only reproducibility, so heterogeneous scenarios sit on one chart. The green time, by contrast, is charted in **sequence order** (next section).

# Two Charts, One Source: the Input

The variables feed two control charts, both indexed by the **test sequence** — tests are ordered shortest-to-longest (today by hand, walking `sheep-dog-grammar/scenarios-graph.graphml`).

**1. The green-time chart** plots `green time` (the scaled minimum) as individuals plus its moving range. It asks *is any single test asking too much?*

- A point **over the UCL but under the max timeout** is a **too-big / under-specified** test: the spec is thin enough that Claude has to infer its way to an answer (a Given/When/Then short of the ~3 steps it needs). A model-based-testing tool can prevent outright contradictions, but if the model isn't detailed enough it can't stop Claude inferring from a thin test — so this signal persists.
- A point **at the max timeout** is a **contradiction** (the new test fights one that already passes and Claude plays whack-a-mole) or — likelier now, given the checks in place — **Claude trying to build a dependency it isn't allowed to add**, constructing the library inside the project from scratch.
- A spike in the **moving range** is a **too-big leap** between consecutive tests. Counterintuitively the short early tests take the longest and the long later ones the least (they reuse established patterns), and the very first is a **warm-up** that builds the scaffolding — stratify it out. Past the warm-up the moving range should be flat; keeping it flat means sequencing each test to differ by a similar amount (≈x steps), which is how you catch a test that demands too big a jump in understanding.

**2. The pair-range chart** plots `green time pair range` as individuals plus its moving range. It asks *is any single test ambiguous?*

- A high **pair range** means the two worktrees diverged — a **vague** test that admits more than one implementation (or one run got lucky; a low pair range on a big test can be luck, not clarity, which is exactly why the green-time chart is needed alongside it).
- The **moving range** of the pair range should be stable — the ordinary jitter is roughly constant for a project at a given time of day; a jump flags a test whose reproducibility is off relative to its neighbours.

The unifying point that makes the whole approach worth it: **a point outside the limits is a signal to change the input, not the worker.** A too-big, ambiguous, contradictory, or badly-ordered test creates more variation than any amount of tuning the harness could remove. The worked examples — real points crossing these limits, traced back to their inputs — live in [Darmok - Harness for Claude Code][8].

# The Pair Range Has Two Axes: Time and Work

The pair range as defined above is a *wall-clock* difference, and wall-clock is work × decode-rate. That product hides a failure mode. On a fast-decode day two worktrees can do **materially different amounts of work** — one explores far more of the codebase before the same edit — yet finish within seconds of each other, because the heavier half simply decoded faster. The time pair range reads "reproducible"; the test was not.

The fix is a second axis: the **token pair difference**, \|green a tokens − green b tokens\|. Output tokens track work directly and are immune to decode-rate masking, so the token difference catches a divergence the time range misses — and, symmetrically, a wide time range with near-equal tokens is decode jitter, not a divergence (the existing `scale` already leans on this direction). The two axes are orthogonal: a scenario can sit near the bottom of the time-range chart and at the top of the token-difference chart. **Screen on both.** Ranking the corpus only by wall-clock range will pass over exactly the tests whose two runs disagreed most about how much work the spec demands — the strongest ambiguity signal there is, because it is the halves disagreeing about scope, not just timing.

This is the dual of the luck caveat already noted for the time chart: a *low* range on a *big* test can be luck rather than clarity. Here a *low time range* on an *ambiguous* test can be decode speed rather than agreement — and the token axis is what tells them apart. The worked example (Rebuild84's `7476bbb` and `1dd042e`: bottom of the range chart, top on token divergence, the heavier half spelunking the issue-detector interface tree the leaner half skipped) is in [Darmok - Harness for Claude Code][8].

A caveat the later decomposition forces, though: the "work" the token difference catches is **exploration / orchestration depth, not divergent output.** [pbc-85](85) decomposed green tokens to ~75–84% tool-orchestration churn, ~14–27% artifact, ~1% thinking, and found the token difference **decoupled from the produced code** — the heaviest-divergence pair was byte-identical, the rest cosmetic-only. So the token axis is a real **path-reproducibility** signal (did the two halves explore the same amount?), but it is *not* evidence the halves built materially different artifacts — they converge on the same files. Read it as "one half hunted longer," not "the spec admits two implementations"; the latter still has to be confirmed by reading the transcript and diffing the output.

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

- **The common-cause band itself.** Its width may be a measure of how tight the specification language is. The remaining floor is the model's sampling nondeterminism plus server-side decode-rate noise — uncontrollable from any input. The time-of-day confound is now **fully removed and validated**: [#153](https://github.com/farhan5248/sheep-dog-main/issues/153), realized as the in-run pair ([#434](https://github.com/farhan5248/sheep-dog-main/issues/434)) and the concurrent pair ([#439](https://github.com/farhan5248/sheep-dog-main/issues/439)), runs both halves together at the same time of day off the same red commit, so a slow-server night hits both equally and cancels in the pair range; batches [pbc-83](83)–[pbc-85](85) confirm the band holds in control with the confound gone. The `scale` (from green tokens) normalizes the *individuals* across runs/days. What stays open is the band *width* as a spec-tightness measure.
- **Should the pair range be charted on tokens rather than wall-clock? — Answered ([pbc-85](85)): no, not as a replacement.** Decomposing green output tokens showed they are **~75–84% tool-orchestration churn** (re-emitted TodoWrite lists, Grep/Read/ToolSearch args), ~14–27% artifact, and ~1% thinking — and the token *difference* across a pair is **decoupled from the artifact**: Rebuild85's *widest* token-gap pair produced **byte-identical code**, the narrower ones differed only cosmetically. So a token-pair-range chart detects **exploration / path nondeterminism** (one half hunting longer for the same insertion point), not spec-ambiguity-driven *divergent output* — both halves converge on the same files regardless. It stays a useful **secondary** screen for exploration-style variation and the input to `scale`; the **wall-clock pair range remains the primary** reproducibility-of-outcome detector. Charting on tokens *instead* would track verbosity, not clarity.
- **The contradiction extreme is rare by construction.** The cleaned test suite means an outright contradiction almost never arises (that was the early hand-authored era); a green-time timeout today is far more often Claude building a forbidden dependency in-project. Deliberately producing contradictions to exercise that end of the chart is part of the on-demand test generation below (rgr-gen-from-comparison, being integrated into Darmok).
- **Can the reverse-prompt be generated automatically** when a point crosses the limit, so the tester gets "your test admits two implementations / is too big" feedback without a developer in the loop?
- **Rational subgrouping of the inputs.** A model-based-testing tool (GraphWalker) that controls path size and ordering would make the work units uniform by construction — generating bounded, similar-sized paths instead of the wildly unequal ones a minimal path cover yields. That is system work on the *inputs* (redesigning the input population), not tampering with the harness. The controlled length-vs-thinking-time data this assumes doesn't exist yet; producing it is exactly what that work is for. Today the relationship is only observational (the order/size effect in [Deming - Building Quality In][1] and the net-new-logic ranges in [Darmok - Harness for Claude Code][8]).

---

[1]: deming-building-quality-in
[3]: https://www.buzzsprout.com/1758599/episodes/18909101
[8]: darmok-harness-for-claude-code
[9]: goldratt-diminishing-a-limitation
