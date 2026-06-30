---
title: Pair-range Case Study — Rebuild87
---

# Pair-range Case Study — Rebuild87

Rebuild87 reran the **issue #444** "Validation for Issues" scenario set — the batch where the one bundled *"Test step must have a valid object name"* validation had already been split into focused Only/File/Workspace/TestStep-Full-Name tests. This case study takes the run's **two widest token-scaled green pair-ranges** (worktree `_a` vs `_b`, branched from the same red commit minutes apart) and asks, per pair, whether the spread is an **assignable cause** (the test case bundles too much or is ambiguous) or **common cause** (generation-rate jitter on equivalent work). Selection used the pair-range sheet's scaled `range` column (gid 596217876); note that **no scenario in Rebuild87 exceeds the control limit** (`range_UCL = 74,241`; widest scaled range = 58,878), so the chart flags "look here," not "out of control."

| Scenario | Commit | Green _a | Green _b | Pair-range (scaled) | Winner | Verdict |
|---|---|---|---|---|---|---|
| This object step definition parameter set doesn't exist validation | `1f45ce4` | 5:07 (307,759 ms) | 5:36 (336,961 ms) | 58,878 | _a | **Assignable** |
| Header row Cell names should start with a capital letter validation | `845efb8` | 4:29 (269,137 ms) | 4:11 (251,070 ms) | 40,553 | _b | **Common cause** |

---

## Pair 1 — `parameter set doesn't exist` (`1f45ce4`) — Assignable

### What We Observed

Shared red 1:38, shared refactor 1:08. The two green halves split **29 s raw** (`_a` 5:07 vs `_b` 5:36), and the mojo logged a **functional divergence between the pair**:

> *When step object is absent, A returns "" (no issue) but B returns `ROW_CELL_LIST_WORKSPACE` for the row; differentiating input is validate-action on a row whose parent step's step-object file does not exist.*

The two halves did not merely take different wall-clocks — they produced **different validation behavior** on the same input, because the spec does not pin what should happen when the row's parent step's step-object file is absent.

### Where The Time Went

| Proxy | `_a` (winner) | `_b` |
|---|---|---|
| Wall-clock (green) | 5:07 | 5:36 |
| Assistant turns (stop-reason events) | 28 | 32 |
| Green output tokens | 8,426 | **10,038** |
| Read calls | 11 | **17** |
| Grep calls | 7 | 7 |
| Write / Edit | 2 / 2 | 2 / 2 |
| `mvn verify` cycles | 2 | **3** |

### The 2 vs 3 mvn-cycle divergence (and the exploration that drove it)

| | `_a` | `_b` |
|---|---|---|
| Explore | Grep `getCellListAsString` → direct | Grep `class RowIssueDetector`, `class RowIssue`, `interface IRow`, `interface IStepParameters` — walked the hierarchy |
| Build/verify | mvn @ 23:26:59, 23:27:33 (**2**) | mvn @ 23:25:56, 23:27:37, 23:28:19 (**3**) |
| Outcome | settled on "absent ⇒ no issue (`""`)" | settled on "absent ⇒ `ROW_CELL_LIST_WORKSPACE`" |

`_b` spent its extra ~30 s and ~1,600 extra output tokens spelunking the row/cell/step-parameters interfaces to *decide what the absent-file case should mean*, then ran an extra verify cycle to confirm its choice — and still landed on a **different answer** than `_a`.

### Classification — Assignable

- **Token-scaled pair-range:** green output tokens `_a=8,426` vs `_b=10,038` — **+16.1 %, over the 15 % threshold**. Work is **not equivalent**; the range is a real generation-volume difference, not jitter.
- **Secondary proxies all agree:** `_b` did +6 Reads and **+1 whole `mvn verify` cycle**; the divergence is concentrated in the hierarchy-exploration phase.
- **Functional diff confirms it:** the two halves committed *different behavior*, so the variation is rooted in the test/spec, not the harness.

**Verdict: assignable.** The scenario silently bundles an under-specified edge case.

---

## Pair 2 — `Header row Cell names` (`845efb8`) — Common cause

### What We Observed

Shared red, shared refactor. Green split **18 s raw** (`_a` 4:29 vs `_b` 4:11). The mojo logged **"No functional diff between pair"** — both halves produced identical validation behavior.

### Where The Time Went

| Proxy | `_a` | `_b` (winner) |
|---|---|---|
| Wall-clock (green) | 4:29 | 4:11 |
| Green output tokens | 7,450 | 6,414 |
| Read calls | 12 | 12 |
| Grep calls | 8 | 6 |
| Write / Edit | 2 / 2 | 2 / 2 |
| `mvn verify` cycles | 2 | 2 |

### Classification — Common cause

- **Token-scaled pair-range:** `_a=7,450` vs `_b=6,414` — **13.9 %, within the 15 % threshold** → work equivalent. Scaling `_a` to the faster half's rate leaves a **rate overhead of −22 s** — i.e. the gap is generation-rate jitter, uncontrollable.
- **Profiles are near-identical:** same Read count, same edits, **same 2 mvn cycles**, no functional diff.

**Verdict: common cause.** The 18 s is jitter on equivalent work; fixing it would be tampering.

---

## Batch synthesis — a split

Rebuild87's two worst pairs **do not share a cause**:

- The **widest** pair (`parameter set doesn't exist`) is **genuinely assignable** — a spec ambiguity around an absent step-object file made the two halves *disagree on the right answer*, with one burning extra exploration + a verify cycle to resolve it.
- The **second** pair (`Header row Cell names`) is **common cause** — same work, no functional diff, pure rate jitter.

So the batch is **not** systematically over-bundled; it has **one** scenario carrying a hidden edge case. This is the same *class* of defect #444 was created to remove (ambiguity driving variation), surviving in a different test — confirming the value of re-running the chart after the split: the split fixed the original mega-test, and the chart now points at the *next* ambiguous one.

---

## The Fix, or Why No Fix

**Pair 1 (assignable) — disambiguate the spec.** `This object step definition parameter set doesn't exist validation` does not state the expected outcome when validate-action runs on a **row whose parent step's step-object file is absent**. Pin it: add a Then assertion (or split a focused scenario) declaring the single correct behavior — either "no issue" or `ROW_CELL_LIST_WORKSPACE`, not left to the implementer. That removes the decision the two halves were forced to make independently. *(File a focused GH issue under the #444 / mbt-assignable-cause track; per the run convention, this new assignable cause gets its own issue rather than reopening #444.)*

**Pair 2 (common cause) — no scenario fix.** The spread is generation-rate jitter; changing the scenario would be tampering. The only legitimate follow-up is at the **measurement** level: the widest scaled range (58,878) is still under `range_UCL` (74,241), so if these ambiguity-driven pairs should trip the chart, the selection threshold — not the scenario — is what to revisit.

No prompt, harness, or model change is proposed; those are held in statistical control.

---

## Mapping to the Research

| Predicted symptom (pbc-research) | Pair 1 (param-set) | Pair 2 (header row) |
|---|---|---|
| Ambiguous input ⇒ halves diverge on behavior | **Yes** — A `""` vs B `ROW_CELL_LIST_WORKSPACE` | No — identical behavior |
| Extra exploration / generation on the slower half | **Yes** — +6 Reads, +1,600 tokens, hierarchy walk | No — symmetric |
| Extra build/verify cycles to resolve uncertainty | **Yes** — 3 vs 2 `mvn verify` | No — 2 vs 2 |
| Range within control limits despite assignable cause | **Yes** — 58,878 < UCL 74,241 | Yes — 40,553 < UCL |

---

## What This Validates

- Pair-range + the token-scaled gate **separates** an assignable spec ambiguity from rate jitter inside the same batch.
- The **functional-diff warning** is a strong corroborating signal — when present, the assignable verdict is near-certain (the halves literally disagreed).
- Re-running the chart after a split (the #444 work) surfaces the **next** ambiguous scenario rather than declaring victory.

## Open Questions From This Case

- Should the absent-parent-step-object-file behavior be defined **once** centrally (cross-cutting all Workspace validations) rather than per-scenario?
- Rebuild87's worst point is assignable yet **under** the control limit — does the selection threshold need lowering so ambiguity-driven pairs trip the chart, or is the functional-diff warning the better trigger?

[2]: https://farhan5248.github.io/specificationbyprompt/architecture-and-capabilities/pbc-research
[3]: https://farhan5248.github.io/specificationbyprompt/architecture-and-capabilities/pbc-4445
