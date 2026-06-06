---
title: Darmok - Harness for Claude Code
---

* TOC
{:toc}

---

# How To Read This Page

[Wheeler - Understanding Variation](wheeler-understanding-variation) defines the handful of numbers we watch on Claude's runs. **This page is the evidence behind them.** Each section below is one of those variables, and under it are the runs — in the order they happened — that taught us what the variable means. Read a section top to bottom and you see how our understanding of that one number grew.

The premise throughout (from [Goldratt - Diminishing A Limitation](goldratt-diminishing-a-limitation) and Wheeler): when two runs of the *same* test diverge, the cause is in what they **share** — the test, the spec, the harness — not in the model having a bad night.

One thing the runs changed our minds about: we started out believing every wide gap was a defect to hunt down and fix. It wasn't. Many are ordinary noise, and the real fixable signal is almost always the **input** — a test that is too big, too vague, or given out of order. That shift is what the timelines below show.

---

# green time pair range

*The gap between the two runs of the same test (`|green a − green b|`). Two runs of a clear, well-specified test land close together; a wide gap means something made them diverge.*

- [Rebuild 44/45](4445) — the first wide pairs, both traced to an **under-specified prompt step**, not the worker — and the early lesson that "which file got patched" tells you about the spec, not the prompt.
- [Rebuild 48/49](4849), [50/51](5051), [56/58](5658) — each wide pair was a **prompt/spec gap** (an annotation the report never produces; a prompt pointing at a file that doesn't exist; a silent prompt branch). The output was near-identical; the variance was all in the path the worker took.
- [Rebuild 52/53](5253) — the first pair to break the upper limit: a point *outside* the limit is a cleaner, stronger signal than a merely wide one.
- [Rebuild 54/55](5455) — a wide pair with **byte-identical output** exposed a *harness bug* (a coverage shortlist that never wrote a single byte). The signal catches broken infrastructure, not just the model — and CI never noticed because both runs passed.
- [Rebuild 65/66](6566) — a wide pair meant **architecturally different code**, a fork seeded by the very first scenario.
- [Rebuild 67/68](6768) — the turning point: a wide pair with **identical code and no findable cause**. A wide range is a signal to *investigate*, not proof of a defect — the verdict was "common cause, no action."
- [Rebuild 69/70](6970) — same final code, one expensive tool choice (a subagent vs an inline search): same outcome, different path.
- [Rebuild 73/74](7374), [79/80](7980) — the wide pair was **the clock, not the test**: the two runs ran hours apart and the gap was server speed (in 79/80 the *faster* run even produced *more* work). This is what forced us to control the timing.
- [Rebuild 75/76](7576), [77/78](7778) — common-cause wide pairs; 77/78's width was simply that the **test is big**, not defective.
- [Rebuild 83](83), [84](84), [85](85), [86](86) — once the two runs go **concurrently from the same commit**, the timing confound is gone; a wide range now points at how much each half explored, and these batches stay in control.

# green time pair range moving range

*How much the pair range jumps from one test to the next. Because tests are ordered shortest-to-longest, the pair range is expected to widen **gradually**; a sudden jump — not the gradual rise — is the signal.*

- No run has produced a finding here yet. This is a gap we'll fill as more batches come in — left visible on purpose.

# green time

*The green-phase time of a single run (we keep the shorter of the pair). Over the limit but under the timeout = a test too big or under-specified, so Claude has to guess. At the timeout = a contradiction, or Claude trying to build a dependency it isn't allowed to add.*

- [Rebuild 47](47) — green-phase **scope creep**: a run did far more editing than its failing test required (a validator run inside the fix step, then chasing every finding).
- [Rebuild 50/51](5051), [52/53](5253), [54/55](5455), [56/58](5658), [65/66](6566) — long green times from build-retries, exploration overhead, and improvised checks. No contradictions — just extra work before the same edit.
- [Rebuild 67/68](6768) — the long run wrote the *same* code; the length was **deliberation** (re-proving a fact the test had already pinned), not a harder scenario.
- [Rebuild 75/76](7576) — the first scenario is intrinsically the longest (it creates ~10 files), giving any divergence more room to open up.
- [Rebuild 77](77) — the green-time **control chart** itself: six runs all in control; the compile sub-phase is stable, the verify sub-phase carries the variance.
- [Rebuild 77/78](7778) — the clearest "too big" case: the widest *relative* range belongs to the tests that create brand-new logic. The fix is an input fix — split them smaller.
- [Rebuild 84](84), [86](86) — the longest scenario is the framework-founding first one; no timeouts, so no contradictions in this era.

# green time moving range

*How much the green time jumps between consecutive tests. Ordered shortest-to-longest, it should rise gradually; a leap flags a test that demanded too big a jump in understanding.*

- [Rebuild 77](77) — the only run to chart it so far: the green-time series was **stable**, the largest leap well under its limit, so the process is not drifting. More data is needed before this thread says much.

# scale & green tokens

*Output tokens measure how much work a run did, independent of how fast the server ran that day. They feed the `scale` that normalizes for server speed — and they tell a real work difference apart from a slow-server day.*

- [Rebuild 44/45](4445) → [77/78](7778) — every early study compared **two runs taken hours or days apart**, so server/time-of-day decode speed was baked into the pair range and couldn't be separated out. Tokens were usually comparable, so the variance was tool-call *count*, not text volume.
- [Rebuild 67/68](6768) — one run produced **far more tokens for the same commit**: green cost is about how much the worker *thinks* before writing, not how much it writes.
- [Rebuild 73/74](7374), [79/80](7980) — the canonical **server-noise** cases: the wide pair was almost entirely server decode rate (79/80's faster run even emitted more tokens). This motivated normalizing per-token rate and running pairs at the same time.
- [Rebuild 83](83) — derived the **token-scaled pair-range**: a similarity threshold decides whether the two halves did equivalent work, and the slower half is scaled to the faster's rate to isolate real work from rate overhead.
- [Rebuild 84](84) — tokens are a **second, orthogonal axis**: ranking by token divergence surfaced pairs the wall-clock range missed (a fast-decode day hid the extra work). Screen on both.
- [Rebuild 85](85) — but a green token is ~70–84% tool-orchestration churn, not thinking or output: the widest token-gap pair produced **byte-identical code**. So tokens measure *exploration depth*, not divergent output — the wall-clock range stays the primary signal.
- [Rebuild 86](86) — token-scaling can **over-promote** a tiny range; cross-check against the raw range so noise doesn't get surfaced as a top pair.

# warm-up position

*The first test in a run is special: it builds the test-automation scaffolding every later test reuses, so it is the longest and most variable. It's a different process and should be set apart from the rest.*

- [Rebuild 65/66](6566) — the first scenario **forks** (invent-and-delegate vs inline), and every later scenario copies it: a wide range three tests later was really decided at test one.
- [Rebuild 75/76](7576) — the first scenario ran "cold" and went **hunting for dependencies** it should have just created; fixed by having it read the specs first.
- [Rebuild 77](77) — the framework-founding first scenario has the longest green and should carry **its own baseline**, not be pooled with the rest.
- [Rebuild 86](86) — the lone over-limit point was the first scenario; its width is **one-time framework cost**, not a defect — stratify it out.

---

# Where This Leaves Us

Read together, the timelines say the harness is **in statistical control**: most wide pairs now resolve to ordinary noise or to the test simply being big, and the recurring near-special-cause is the warm-up scenario — a known, structural thing, not a defect. So the work has moved off the harness and onto the **inputs**: the size, ordering, and spec-detail of the test cases (see [Deming - Building Quality In](deming-building-quality-in) for who owns that, and [Wheeler - Understanding Variation](wheeler-understanding-variation) for the charts).

Two threads above — the two **moving ranges** — are still nearly blank. They're newer than most of these runs, so the history hasn't been written yet. As more batches come in, that's where the next understanding gets recorded.
