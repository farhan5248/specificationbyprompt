---
layout: page
title: Hybrid Tools Landscape
---

# Hybrid Tools Landscape: MBT + MDD + AI

This page analyzes tools that attempt to combine model-based testing, model-driven development, and AI code generation - the three pillars of the Sheep-Dog hybrid approach.

## The Three Requirements

1. **MBT**: Test case generation from graph models (deterministic)
2. **MDD**: Main code structure generation from UML models (deterministic)
3. **AI Inference**: Method body completion from test case logic (adaptive)

## The Honest Answer

**No tools currently combine all three requirements.** The Sheep-Dog approach appears to be unique in combining these elements with deterministic tests acting as an oracle for AI-generated code.

---

## Closest Contenders (Top 5)

### 1. iEcoreGen (Research - December 2025)

**The closest match to the Sheep-Dog philosophy.**

From the [arXiv paper][1]:
- Combines **EMF/Xtext templates** (deterministic structure) with **LLMs** (method body completion)
- Uses Ecore models as structural blueprints
- LLMs complete unimplemented methods from docstring specifications
- **Surpasses pure-LLM approaches** on pass@k metrics

**Gap**: No MBT/graph-based test generation - tests are not the oracle

---

### 2. GraphWalker + Manual AI Integration

From the [ACM industrial case study][2]:
- Mature MBT tool for graph-based test case generation
- Generates test paths from directed graphs
- **No AI integration** - purely deterministic

**Gap**: No code generation, no AI - requires building the integration manually

---

### 3. Tessl

From [Martin Fowler's analysis][3]:
- Aims for "spec-as-source" with bidirectional syncing
- Marks generated code with `// GENERATED FROM SPEC - DO NOT EDIT`
- Most ambitious of the spec-driven tools

**Gap**: Pure AI - no deterministic modeling, no MBT, non-deterministic generation

---

### 4. MBTsuite

From [MBTsuite.com][4]:
- Generates test cases from UML models
- Supports path/edge coverage criteria
- Integrates with test management tools

**Gap**: Test generation only - no main code generation, no AI

---

### 5. Qodo (Test-Driven Generation)

From the [Qodo blog][5]:
- AI generates tests first, then code to pass them
- Closest to "AI inference from test cases" concept

**Gap**: Pure AI - no deterministic structure, no formal models

---

## Comparison Matrix

| Requirement | iEcoreGen | GraphWalker | Tessl | MBTsuite | Qodo | Sheep-Dog |
|-------------|-----------|-------------|-------|----------|------|-----------|
| MBT from graph models | No | Yes | No | Yes | No | **Yes** |
| MDD structure from UML | Yes | No | No | No | No | **Yes** |
| AI method body inference | Yes | No | Yes | No | Yes | **Yes** |
| Deterministic test generation | No | Yes | No | Yes | No | **Yes** |
| Tests audit AI code | No | N/A | No | N/A | Partial | **Yes** |

---

## Why Sheep-Dog is Unique

The Sheep-Dog approach is the only one that:

1. **Uses deterministic test generation as an oracle** - Tests aren't AI-generated, preventing the "mental patient writing their own discharge evaluation" problem
2. **Keeps structure deterministic while AI fills logic** - Class names, method signatures, and dependencies are fixed by traditional tools
3. **Maintains separation of concerns** - Deterministic tests validate non-deterministic AI code

---

## The Research Gap

The [iEcoreGen paper][1] is the closest academic work, but it still:
- Uses LLM-generated tests (not deterministic)
- Doesn't leverage MBT graph models
- Misses the key insight about "auditing" AI with deterministic tests

The limitation noted in [Red Hat's article on spec-driven development][6]:

> "Code generation from spec to LLMs isn't deterministic; this poses challenges when it comes to upgrades and maintenance."

The Sheep-Dog solution: **Make everything deterministic except the method bodies, and use deterministic tests to validate those.**

---

## Implications for the Future

The hybrid approach suggests a path forward where:

1. **Traditional MDD tools** (Xtext, EMF, GraphWalker) handle structure and test generation
2. **AI tools** (Claude, GPT) handle logic inference within fixed boundaries
3. **Deterministic tests** serve as the authoritative interpreter of requirements

This combines the best of both worlds: the correctness guarantees of model-driven development with the flexibility of AI code generation.

[1]: https://arxiv.org/abs/2512.05498
[2]: https://dl.acm.org/doi/fullHtml/10.1145/3452383.3452388
[3]: https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
[4]: https://mbtsuite.com/
[5]: https://www.qodo.ai/blog/ai-code-assistants-test-driven-development/
[6]: https://developers.redhat.com/articles/2025/10/22/how-spec-driven-development-improves-ai-coding-quality
