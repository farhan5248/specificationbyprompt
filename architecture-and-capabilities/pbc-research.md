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

# Open Questions

- Common cause variation is mostly unexplored. The width of the common-cause band may itself be a measure of how tight the specification language is.
- How does the chart behave when the underlying harness changes (new DSL grammar, new test automation patterns)? Is there a re-baselining protocol?
- Can the reverse-prompt be generated automatically when a special-cause point is detected, so the tester gets feedback without a developer in the loop?

---

[1]: pbc-for-claude-code
