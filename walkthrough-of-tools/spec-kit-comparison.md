# Spec-Kit vs Sheep-Dog Approach: Comparison Report

## Key Findings

### Spec-Kit
- Pure AI-driven approach with no traditional modelling tools
- Uses natural language specs in `.specify/` directories
- AI generates everything: structure, implementation, and validation
- Simple 7-step workflow focused on "intent-first" development

### Your Approach is a hybrid with stronger guarantees:
- Combines traditional modelling (UML patterns, Xtext, EMF) with AI
- Deterministic test generation acts as an "auditor" of AI code
- Round-trip engineering maintains consistency
- feng agent enforces pattern compliance

### The Critical Insight
Your "auditing philosophy" from the ubiquitous-language docs is the key differentiator:

> "If you gave one automated test specification to 5 developers, you'd expect 5 different implementations - that's acceptable. However, one test specification shouldn't have 5 different interpretations of the expected results."

This separation of concerns (deterministic tests validating non-deterministic AI) is something Spec-Kit lacks.

### Sheep-Dog Approach - Key Advantages

1. **Hybrid Architecture**: Combines Xtext/EMF (deterministic) with AI (adaptive)

2. **Separation of Concerns**: The auditing philosophy is the critical differentiator - deterministic test generation validates non-deterministic AI code, preventing "the mental patient writing their own discharge evaluation"

3. **Hallucination Containment**: By using traditional tools for structure and AI only for method body logic, the surface area where AI can hallucinate is reduced

4. **Pattern Enforcement**: The feng agent and `code-verification.md` provide formal verification that Spec-Kit lacks entirely

### For the Future Hybrid (Traditional + AI)

The proposed combination of:
1. **sheep-dog-dev jar** for deterministic structure generation
2. **AI inference** to complete method bodies from test cases

...is more rigorous because:
- Structure is fixed (no hallucination risk)
- Tests are deterministically generated (act as oracle)
- AI's scope is contained to logic inference only

---

## Executive Summary

This report compares GitHub's **Spec-Kit** (Spec-Driven Development toolkit) with the **Sheep-Dog** approach (Specification by Prompt methodology) across four dimensions.

---

## Report 1: Spec-Kit Overview

### What is Spec-Kit?
An open-source toolkit implementing **Spec-Driven Development** - a methodology that elevates specifications from planning documents into executable blueprints for AI code generation.

### Core Workflow (7 Steps)
1. **Constitution** - Establish project governing principles
2. **Specification** - Define functional requirements/user stories
3. **Clarification** - Resolve ambiguities
4. **Planning** - Determine technical architecture
5. **Validation** - Audit plans for completeness
6. **Task Breakdown** - Generate ordered implementation tasks
7. **Implementation** - Execute tasks systematically

### Key Characteristics
- Integrates with 16+ AI agents (Claude, Copilot, Cursor, etc.)
- Stores specs in `.specify/` directories (memory, specs, scripts, templates)
- Uses markdown-based specification format
- Pure AI-driven: no traditional modelling tools (EMF, UML)
- Focus on "intent-first" rather than structural models

---

## Report 2: Current Approach Comparison (UML/Arch/Impl Files)

### Your Current System

| Component | Purpose | Location |
|-----------|---------|----------|
| `uml-*.md` | Structural patterns (classes, packages, interactions) | `site/uml/` |
| `arch-*.md` | Architectural guidance (logging, validation, etc.) | Root directory |
| `impl-*.md` | Technology-specific implementations | Root directory |

### Key Differentiators

| Aspect | Your Approach | Spec-Kit |
|--------|---------------|----------|
| **Specification Format** | UML patterns in markdown with {Variable} placeholders | Natural language user stories |
| **Structural Modeling** | Explicit class/package/interaction diagrams | Implicit - emerges from implementation |
| **Verification** | Deterministic verification via `code-verification.md` | AI validates against specs |
| **Pattern Enforcement** | feng agent verifies code compliance | No formal pattern checking |
| **Bidirectionality** | Round-trip: AsciiDoc <-> UML <-> Code | One-way: Spec -> Code |

### Your Unique Strengths
1. **Formal Pattern Language**: `{Type}`, `{Language}`, `{Aspect}` variables enable reusable patterns
2. **Deterministic Verification**: Code verification generates compliance reports
3. **Round-Trip Engineering**: Bidirectional transformation (docs <-> code)
4. **Explicit Architecture**: Separation of concerns (uml/arch/impl)

### What You'd Need to Change for Spec-Kit Alignment
- **Nothing required** - your approach is complementary, not competing
- Could add: Natural language spec layer on top of UML patterns
- Your UML patterns could inform spec-kit's "Planning" phase

---

## Report 3: Hybrid Approach - AI + Traditional Modelling

### Does Spec-Kit Use Traditional Modelling?
**No.** Spec-Kit relies **solely** on:
- Natural language specifications
- AI interpretation of requirements
- No Eclipse Modelling Framework (EMF)
- No UML tools or formal models
- No Xtext/language workbenches

### Your Hybrid Approach Advantages

Your approach combines:

| Traditional Tools | AI Tools |
|------------------|----------|
| Xtext language workbench | Claude Code for implementation |
| EMF-based UML models | AI interprets specs as prompts |
| Deterministic code generation | AI fills method bodies |
| IDE integration (Eclipse/VSCode) | AI assists with logic inference |

### The Auditing Philosophy (from your docs)

> "If you gave one automated test specification to 5 developers, you'd expect 5 different implementations of the main code - that's normal and acceptable. However, one test specification shouldn't have 5 different interpretations of the expected results."

This is your key insight that Spec-Kit lacks:
- **Spec-Kit**: AI generates both tests and implementation (potential conflict of interest)
- **Your Approach**: Deterministic test generation audits non-deterministic AI code

---

## Report 4: Traditional + AI Code Generation Combination

### Your Proposed Approach
1. **Traditional tools** (sheep-dog-dev jar): Generate code structure deterministically
2. **AI inference**: Complete method bodies from test case logic

### How This Differs from Spec-Kit

| Aspect | Your Hybrid | Spec-Kit Pure AI |
|--------|-------------|------------------|
| Structure generation | Deterministic (templates) | AI-generated |
| Method bodies | AI-inferred from tests | AI-inferred from specs |
| Verification | Tests are authoritative | Specs are authoritative |
| Hallucination risk | Contained to method bodies | Affects entire codebase |

### Practical Advantages of Your Hybrid

1. **Reduced Hallucination Surface**
   - AI only fills in logic, not structure
   - Class names, method signatures, dependencies are fixed

2. **Testability Built-In**
   - Test automation is deterministically generated
   - Acts as oracle for AI-generated logic

3. **Traceability**
   - UML patterns trace to implementations
   - feng agent maintains cross-references

4. **Incremental Adoption**
   - Can apply AI to specific methods
   - Traditional generation handles boilerplate

---

## Comparative Summary Table

| Dimension | Spec-Kit | Sheep-Dog (Current) | Sheep-Dog (Future Hybrid) |
|-----------|----------|---------------------|---------------------------|
| Specification Format | Natural language | UML patterns + AsciiDoc | Same + AI prompts |
| Code Generation | Pure AI | Traditional templates | Template structure + AI bodies |
| Verification | AI self-validates | Deterministic reports | Deterministic tests audit AI |
| Modelling Tools | None | Xtext, EMF, Eclipse | Same + AI assistance |
| Bidirectionality | No | Yes (round-trip) | Yes |
| Pattern Enforcement | No | feng agent | feng agent |
| Hallucination Control | Trust AI | N/A | Tests as oracle |

---

## Recommendations

### Keep from Your Current Approach
1. **UML pattern files** - These provide structure Spec-Kit lacks
2. **Deterministic verification** - Your code-verification.md is a differentiator
3. **Round-trip engineering** - Valuable for maintaining consistency
4. **feng agent** - Pattern compliance is unique

### Consider Adopting from Spec-Kit
1. **Constitution files** - Could formalize your CLAUDE.md approach
2. **Task breakdown step** - Explicit task decomposition before implementation
3. **Multi-agent support** - Your patterns could work with other AI tools

### For Your Hybrid Future
Your approach of:
- Traditional tools for structure (sheep-dog-dev jar)
- AI for method body inference from test cases

...is **more rigorous** than Spec-Kit because:
1. You maintain separation of concerns (test vs implementation)
2. Deterministic test generation prevents AI from validating itself
3. Structure is fixed, reducing hallucination impact

---

## Conclusion

**Spec-Kit** is a pure-AI, specification-first approach with no traditional modelling.

**Your approach** is a hybrid that combines:
- Traditional modelling (UML patterns, Xtext, EMF)
- Deterministic code generation (sheep-dog-dev)
- AI assistance for implementation logic
- Formal verification (feng agent)

Your approach has **stronger theoretical foundations** for preventing AI hallucinations because you separate the concerns of:
1. Structure definition (deterministic)
2. Test generation (deterministic)
3. Implementation logic (AI-assisted, validated by tests)

Spec-Kit's simplicity makes it accessible, but your hybrid approach provides better guarantees for safety-critical or complex enterprise systems.
