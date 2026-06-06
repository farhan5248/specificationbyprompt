---
title: The problems a PBC solves
---

* TOC
{:toc}

---

# The Question

If we can just spot a functional difference between two runs of the same test, why the need for all the statistics?

It's a fair challenge. The two halves of it sound airtight: if there's a functional diff, the spec was unclear, so tighten the test; if there's no diff, the test was clear and did what I intended. The diff is the actual code — why layer control charts on top of something you can already see?

The short answer: **correctness tells you about one run; variation tells you about the process.** Relying on a diff after each test is inspection, and inspection alone doesn't build quality into the process — it sits at the end and catches what it happens to catch. The failures that have actually bitten us are precisely the ones inspection misses, and they're visible only as variation against a baseline.

# What the Variation Is For

The motivation comes first, because it frames everything else. For a given codebase, if work is broken into small, equal-sized problems, then with the right harness each problem should take roughly the same time — within limits. The bigger the problem, the bigger the codebase, and the thinner the instructions and cheatsheets, the more the interpretation varies. So **variation is the measurable proxy for how well-tuned the whole system is** — harness, spec, context, and problem-sizing together. Study the variation for a project and you can tweak the system until you get the results you expect.

Equal-sized problems aren't a convenience; they're the experimental control that makes variation *attributable*. Size is measured, not guessed — scenarios are ordered by step count. The natural experiment is runs 31–40, which used a different order: tests would sometimes get implemented and sometimes not. Re-ordering by step count made that far less common. The control works.

Two limits on the control are worth stating up front:

1. **Step count fixes size, not difficulty.** Two equal-count scenarios can demand very different amounts of net-new logic. A short test can need a lot of new logic — that test has to be broken into two or three smaller problems, or it will keep varying no matter how it's ordered.
2. **There is a floor.** Past the model's sampling nondeterminism and server decode rate, residual variation is the model, not the system. Tuning past that floor is tampering — it adds work and can add variance. Knowing when to stop is part of the method.

# A Diff Is Necessary but Not Sufficient

A functional difference *is* a clean signal — act on it, no statistics required. But two things keep it from being the whole story.

**The cause can be anywhere in the shared context, not just the test.** When two runs of the same test diverge, the cause is in what they share — the test, the UML/spec files, the prompt template, the examples in `interaction-main.md`, the harness. A diff says "something in the shared system is loose"; the transcript says which lever to pull. "Improve the spec" can be the wrong move.

**The diff is low-recall.** Both halves are anchored by the same red test, so the *output* converges even when the test is ambiguous. The widest-divergence pairs routinely produce identical or cosmetic-only code ([Rebuild 85](85): the widest token-gap pair was byte-identical; [Rebuild 84](84): the strongest ambiguity signal was hidden under matching output). If you only act on functional diffs, you catch the rare loud cases and miss the common quiet ones. The ambiguity shows up in the *path and work* — time, tokens, pair range — not in the bytes.

# "No Diff" Does Not Mean Everything Went as Expected

1. **It could have cheated for part of the run.** Before the file allowlist existed, Claude would hardcode results and finish in ~60 s against a ~200 s floor. Both halves cheated identically, so there was no functional diff and the build was green. It still showed up — as a sharp swing in the **pair-range moving range**. The cheat then propagated (green feeds prior passing tests forward as examples) into a plateau, until one test broke ranks and actually implemented the code, producing a second swing. This is the canonical proof: functional diff silent, pass/fail silent, speed *cheering* — only the chart fired, because the moving range watches for *change*, not *state*. (See [the cheat as a regime change](#the-cheat-the-clearest-proof).)
2. **It could have been lucky.** A lucky convergence masks an ambiguous test — and because the passing result is stored as an example, the bad reading detonates on a *later* test that depends on the other interpretation. The test that diffs clean is never the one that breaks. Only watching the series catches a deferred, displaced fault; a per-pair diff structurally cannot.

# A Diff Does Not Mean the Spec Needs Improving

Example: Claude implemented business logic in the test code, and because no example applied, the code it improvised varied between halves — a functional difference. The assumed knowledge should have come from `interaction-main.md`, but Claude was looking for plain `interaction.md` and never found it.

That's a **silent context-delivery failure**, the same class as the [Rebuild 54/55](5455) JaCoCo bug: there the cheatsheet was never *written*; here it existed under a name Claude didn't look for. In both, nothing errored, the build was green, the test could pass — and the only thing that revealed the broken context pipe was the variation. The fix was harness-side (the filename), not "make the test clearer." Quality of context doesn't show up as a test failure; it shows up as spread.

# Two Charts, Two Owners

The variation is read on two charts, and the combination routes the fix to the right person:

| pair range | green time | Meaning | Loop in |
|---|---|---|---|
| High | High | Ambiguous *and* demanding | **Tester** — disambiguate / split |
| High | Low | Ambiguous but cheap | **Tester** — disambiguate |
| Low | High | Agreed but both slow — the spec leaves the *how* out | **Developer** — fill in the spec / fix setup |
| Low | Low | Clear, well-sized, reproducible | **Nobody** — in control |

This is why a poor harness doesn't "cry wolf" at the tester. Claude digging through gaps in `interaction-main.md` lands in the **Low pair / High green** quadrant — the developer's, by construction. Having green time as a separate chart from the pair range is what prevents the misrouting.

The limits come from the **average moving range**, not the standard deviation, because a sustained shift or trend inflates the global SD and hides the next signal. Limits are re-baselined only after a deliberate harness change, never mid-run — a cheat or drift is a special cause *relative to the established process*, and re-baselining mid-run would turn the dysfunction into the new normal.

# The Cheat: The Clearest Proof

The hardcode cheat is the single best answer to the opening question, because every cheap detector fails and only the statistics fire:

- **Functional diff: silent** — both halves cheated identically, byte-identical output.
- **Pass/fail: silent** — tests passed, build green.
- **Speed check: misleading** — faster than usual, looks great.
- **Pair-range level: silent** on the plateau — both halves agree.
- **Pair-range moving range: fired** — on the transitions into and out of the cheat, because it's the one thing watching for change.

A diff-only or output-only check would have blessed the cheat and let it propagate through the whole suite. The control chart caught it at the edge. In one case the behavior of later tests had functional differences because earlier tests had no business logic implemented for them in the main code; it was all in the test code.

# Where This Leaves the Question

A diff is the rare loud case; the chart is what sees the quiet ones — and the quiet ones are where our real failures lived: the cheat, the lucky mask that bit a later test, the misrouted business logic, the cheatsheet under the wrong filename. None of them produced a diff you could act on, and most passed their tests. Inspection after each run can't promise quality, because it only catches what it happens to look at. Studying the variation builds the quality into the process — which is the whole point.

This page is the *why*. [Wheeler - Understanding Variation][wheeler] is the *how*: it defines exactly which charts go on Claude's runs, how the limits are computed, and how to tell a real signal from the noise of a stochastic worker. [Darmok - Harness for Claude Code][darmok] is the evidence — the runs where each of these gaps actually surfaced.

---

[deming]: deming-building-quality-in
[goldratt]: goldratt-diminishing-a-limitation
[wheeler]: wheeler-understanding-variation
[darmok]: darmok-harness-for-claude-code
