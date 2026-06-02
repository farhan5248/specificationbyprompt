---
title: Darmok - Harness for Claude Code
---

* TOC
{:toc}

---

# Where This Started — The Hypothesis

The premise, set out in [Goldratt - Diminishing A Limitation](goldratt-diminishing-a-limitation) and its method companion [Wheeler - Understanding Variation](wheeler-understanding-variation), was a bet borrowed from Deming, Wheeler, and Goldratt: that the run-to-run variation of a stochastic worker (Claude), measured against a *deterministic* harness, could be read as a **control chart for the quality of the inputs** — the specification, the prompt, the harness — rather than as a measure of model noise.

What I believed going in:

1. **The variation lives in the system, not the worker.** Like the red-bead game: if two independent runs against the same test diverge widely, the cause is something they *share* — the spec, the prompt template, the harness — not the model having a bad night. A wide pair-range is an **assignable cause** I can name and remove.
2. **Two charts, two failure modes.** A pair-range chart catches **ambiguity** (the spec admits more than one valid implementation; both runs pass but cost different amounts). A single-run chart catches **contradiction** (the new test conflicts with existing behavior; one run balloons). The 45-second-per-iteration test tax is what lifts the signal above network noise.
3. **Each wide pair is a thing to fix.** Investigate the transcript, find the assignable cause, change the system, watch the variance tighten. Repeat.

That third belief is the one this journey tested to destruction. It was right for a long stretch — and then it stopped being right, in a way the methodology itself predicted.

What follows is one section per case study, in run order, grouped into three acts. Each names the problem it found, the fix it proposed, and whether that fix was implemented.

---

# Act 1 — The Productive Hunt (Runs 44–66)

Every pair surfaced a concrete assignable cause, and every cause had a home in the system. This is the methodology working exactly as hypothesized.

## pbc-4445 — the under-specified search step

[pbc-4445](4445) (Rebuild44/45, two cases). **Problem:** `green.md` said "examine the failing code path" without saying *how*, so Claude satisfied it inconsistently — sometimes the JaCoCo cross-reference grep, sometimes `ls` then a pivot away. Wide pair-range on both an ambiguous-file case and a same-file case, proving the cause was the *prompt*, not just the spec. **Fix:** split `green.md` into `identify.md` + `fix.md`, have red populate JaCoCo, spell out the cross-reference ([#384](https://github.com/farhan5248/sheep-dog-main/issues/384)). **Implemented.**

## pbc-47 — the remediation check that found two more gaps

[pbc-47](47) (Rebuild47, a 12-scenario audit, no pair). **Problem:** #384 worked (10/12 used JaCoCo), but the audit surfaced two new prompt gaps — `fix.md` scope-creep (running refactor validators and editing compliance-only files past the test-passing point) and `identify.md` framing JaCoCo as "what the failing test touched" rather than "find similar *passing* tests as templates." **Fix:** scope fence in `fix.md` + template-finder reframe in `identify.md` (commit `569383e`). **Implemented.**

## pbc-4849 — mining an annotation the report never produced

[pbc-4849](4849) (Rebuild48/49). **Problem:** `identify.md` told Claude to extract a `title="<test-name>"` JaCoCo annotation that this project's pom does not emit (aggregate-only coverage, single session), so runs burned time chasing a missing attribute. A producer/consumer mismatch. **Fix:** precompute `jacoco-shortlist.md` in `GreenPhase` and reference it from the prompt ([#404](https://github.com/farhan5248/sheep-dog-main/issues/404)). **Implemented** — though, as pbc-5455 later revealed, the producer silently never ran.

## pbc-5051 — the prompt pointed at a file that doesn't exist

[pbc-5051](5051) (Rebuild50/51). **Problem:** `identify.md` cited `${umlDir}/uml-interaction.md`, which doesn't exist; the answer (`RowIssueDetector`, by name, with a code template) lives in `uml-interaction-main.md`, which neither run opened. One run got it right by luck of read-order. **Fix:** point the prompt at the real file (fix the path / glob both halves / add a read-ordering hint). **Proposed; no implementing commit cited.**

## pbc-5253 — no guard against regressing the sibling test

[pbc-5253](5253) (Rebuild52/53, the only out-of-UCL row on its tab). **Problem:** `fix.md` never told Claude to check the negation-paired sibling Test-Cases sharing the runner's `@RGR` tag; one run edited a path that regressed the sibling and paid a build-retry, on top of search-strategy variance. **Fix:** a `fix.md` behavioral instruction to scan the runner's `.feature` for negation pairs before submitting (no new precompute — the tag already scopes the siblings). **Proposed; no issue filed.**

## pbc-5455 — the harness bug hiding behind a noisy baseline

[pbc-5455](5455) (Rebuild54/55). **Problem:** the #404 shortlist producer **had never written a single byte** since it shipped — `DocumentBuilderFactory` tried to load JaCoCo's relative `report.dtd`, which isn't shipped, threw `FileNotFoundException`, and the exception was silently caught and returned as `null`. The narrowing artifact the prompt leaned on never existed. Both runs still passed; only the pair-range exposed it. **Fix:** disable external-DTD loading and remove the silent catch in `GreenPhase`. **Implemented.**

## pbc-5658 — a silent prompt branch meets a tool quirk

[pbc-5658](5658) (Rebuild56/58). **Problem:** `identify.md` had no explicit exit for the no-COMPILATION-ERROR path, so both runs improvised a `jacoco.xml` check — and one form of the improvised `Glob` (relative pattern + `path=`) tripped Claude Code's gitignore filter on `**/target`, returning "No files found" and sending one run into a 196-second retry loop. **Fix:** explicit no-error exit in `identify.md` ([#414](https://github.com/farhan5248/sheep-dog-main/issues/414)) and a read-every-listed-class-first rule in `fix.md` ([#415](https://github.com/farhan5248/sheep-dog-main/issues/415), with [#411](https://github.com/farhan5248/sheep-dog-main/issues/411)). **Filed/implemented.**

## pbc-6566 — architecture by coin-flip, then cascade

[pbc-6566](6566) (Rebuild65/66). **Problem:** one run put business logic in production code and delegated; the other inlined it into the test layer. Root cause: the branch's *first* validation scenario on a clean base is a coin-flip between the two, and every later scenario copies the local precedent. A stronger model (both Opus) did not supply the architectural rule. **Fix:** a green-verify conformance check ([#425](https://github.com/farhan5248/sheep-dog-main/issues/425)) backstopped by a deterministic regex at refactor ([#423](https://github.com/farhan5248/sheep-dog-main/issues/423)). **Filed.**

---

# Act 2 — The Turn (Runs 67–68)

## pbc-6768 — the first time there was nothing to fix

[pbc-6768](6768) (Rebuild67/68). **Problem:** none. Both runs produced a byte-identical 5-file commit; one re-derived from parser source a fact the other read straight off the test fixture's expected value. No spec gap, no prompt defect, no producer mismatch — **common-cause variation** in model deliberation depth. **"Fix":** not a fix — a system *experiment*, per-phase `--effort` control ([#426](https://github.com/farhan5248/sheep-dog-main/issues/426)), expected to shift the distribution, not remove the variance. **Filed as an experiment.** This case carried the first explicit "Are we in control?" reflection: *in control ≠ minimal variance*, and chasing points inside the limits is tampering.

---

# Act 3 — Drying Up, and the Proof (Runs 73–80)

The assignable causes thin out. Most wide pairs now resolve to server-side noise the system cannot touch, and the one remaining fix is the last of its kind. The act closes with the empirical proof of statistical control.

## pbc-7374 — the assignable cause that dissolved

[pbc-7374](7374) (Rebuild73/74). **Problem:** a 58-second green-phase spread that *looked* like a worker doing more work — and wasn't. Decomposing the JSONL showed active compute differed by −0.7s; the entire spread was server time-to-first-token. An earlier draft had wrongly blamed prompt under-specification. **Fix:** none to spec or prompt — a *measurement* fix: chart active time, not raw wall-clock. **Note:** this active-time proposal was later **abandoned** ([#419](https://github.com/farhan5248/sheep-dog-main/issues/419)) — idle is negligible and not cleanly measurable from single-timestamp events, and wall-clock *is* the work signal; the measurement thread resolved instead to XmR control charts on wall-clock.

## pbc-7576 — both verdicts in one pair

[pbc-7576](7576) (Rebuild75/76, two cases). **Problem:** Case A (`object doesn't exist`, 88.7s) — identical code, one passing build each; the gap was pre-run fixture simulation depth with no instruction that could resolve it → **common cause**. Case B (`suite name capital`, 47.3s) — one run spent 3.5 minutes hunting `~/.m2` and dependency jars for classes it had *already decided to create* → **assignable**. **Fix:** Case A — no scenario fix (one correctness-neutral build-as-oracle reframe applied anyway); Case B — `green-compile` now reads `uml-package.md` + the interaction specs *before* the log (the shared session carries "spec'd ⇒ create" into verify), and `green-verify` Rule 1 closes the `Bash` hole that let the jar-hunt escape. **Both applied.** This is the case that shows the classification gate cuts both ways.

## pbc-77 — the control chart that ended the hunt

[pbc-77](77) (Rebuild77, the same scenario run **six times** on one fixed plugin version). **Problem:** none to find — the point was to chart. Six green-phase times as an XmR individuals series: mean 7.04 min, all six points inside the natural limits [5.41, 8.68 min], the largest moving range well under its limit. **The process is in statistical control.** **Fix:** none — and that is the result. The case states it plainly: the **harness frontier is closed**; further prompt edits to chase the residual green-verify spread would be tampering and can *increase* variance. The only remaining levers are the two **inputs** the harness consumes — UML-spec richness and test-case quality (path size and ordering). It also flags that 0-green runs are a distinct free-rider failure mode, a separate population to chart on its own.

## pbc-7778 — common cause, and the input lever named

[pbc-7778](7778) (Rebuild77/78). **Problem:** same five files, one extra `mvn` cycle, more greps — exploration nondeterminism converging on the same spec. **Common cause.** **Fix:** none to worker/spec/prompt. The lever is the *input*: across the Opus runs this scenario carries the widest *relative* range, and the high-variance scenarios are exactly the ones that create **net-new detector/parsing logic** — the largest solution space. Splitting those net-new-logic tests into smaller single-behavior Test-Cases shrinks the space and narrows the band toward its floor. The idle/active JSONL split is also walked back here as unreliable; lean on work proxies.

## pbc-7980 — the purest no-action case

[pbc-7980](7980) (Rebuild79/80). **Problem:** the single widest pair on its sheet (70s), and the runs took the *same* route — same files, same tool counts, same two builds — yet the **faster run produced *more* output tokens**. You cannot do more work in less time and have the gap be about work. Pure server decode throughput (a 1 AM run decoding at roughly half the midday rate); all three phases inflated uniformly, the fingerprint of a slow-server day. **Common cause.** **Fix:** none — measurement-level only (decode-normalized / SPC-gated selection). The cleanest demonstration that "no action" is the whole answer.

---

# Where This Ended — In Control, Eyes on the Inputs

After ~30 runs the assignable-cause hunt dried up. The shape of the ending is in the data, not in a hunch:

- **The pooled pair-ranges are in control.** Across the frozen 77-80 runs, the per-scenario pair-ranges sit in-band — that is what says the harness is in statistical control (the unit is the pair-range, pooled; individual scenarios don't matter). pbc-77's six-run single-scenario XmR is one scenario examined closely, a corroborating deep-dive, not the whole basis. That in-band reading is the license to stop tuning the harness; continuing to edit prompts to chase points *inside* the limits is the textbook Deming/Wheeler tampering failure — it adds work and can widen the variance.
- **The residual variation is largely uncontrollable.** pbc-7374, pbc-7778, and pbc-7980 all resolve to server-side time-to-first-token / decode throughput. pbc-7980's faster-run-made-more-tokens is the decisive tell: no spec, prompt, or harness change touches it.
- **The big ranges are the big inputs.** The scenarios that stick out are precisely the ones creating net-new logic — the largest, most complex *test cases*, not a flaw in the harness. The variance tracks input size and complexity.

So the answer to *"why have I stopped touching the system and am now focused on the inputs?"* is not resignation. It is the methodology reaching its designed conclusion:

1. **The system is in control, so touching it is tampering.** This is read off the pooled pair-ranges over the frozen 77-80 runs, not assumed (pbc-77 corroborates it for one scenario in depth).
2. **The remaining system-side variation is server noise**, which no *input* change can remove. The one sanctioned harness change left is measurement, not tuning: [#153](https://github.com/farhan5248/sheep-dog-main/issues/153) runs the two halves of a pair concurrently from the exact same commit, so a slow-server night (pbc-7980) hits both runs equally and cancels in the range instead of inflating it. That removes a confound; it doesn't chase a point.
3. **The one class of variation still reducible is input-driven** — and pbc-77 and pbc-7778 name the levers explicitly: enrich the UML spec with the specific contracts a retry reveals (targeted, never "paste the whole codebase," which only trades exploration noise for prefill noise), and control test-case size and ordering (model-based path generation toward bounded, homogeneous work units).

The hypothesis I started with — *variation against a deterministic harness is a control chart for input quality* — turned out to be true in a stronger sense than I expected. It did not just help me fix the harness. It told me, with a real control chart, **when the harness was done** — and pointed the next phase of work at the test cases and specs, exactly where the chart says the remaining signal lives.

The control-chart mechanics behind this conclusion — pair-range as an XmR moving-range signal, why limits come from the moving range and not the global standard deviation, and the in-control / tampering boundary — are worked out in [Process Behavior Charts: The Approach](wheeler-understanding-variation).

---

[1]: goldratt-diminishing-a-limitation
[2]: wheeler-understanding-variation
