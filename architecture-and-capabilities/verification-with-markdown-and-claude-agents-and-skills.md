# Verification with Markdown and Claude Agents and Skills

This summarizes how Claude verifies source code against pattern specifications using skills and agents.

---

## How Verification Works

1. **Pattern files** (`site/uml/uml-*.md`) define expected code patterns
2. **Claude skills/agents** compare source code against these patterns
3. **Reports** (`.report.md`) are generated showing compliance and violations

The methodology is defined in `code-verification.md` with a workflow: Verify → Update (`-u` flag) → Apply fixes (`-a` flag).

---

## Pattern File Types

| Type | What it defines |
|------|-----------------|
| `uml-class-*.md`, `uml-package-*.md` | Structural patterns: naming conventions, method signatures, class structure |
| `uml-communication.md`, `uml-interaction.md` | Behavioral patterns: method calls, object creation, library usage |
| `arch-*.md` | Architecture documentation and design decisions |
| `impl-*.md` | Implementation patterns with `{Variable}` placeholders |

---

## Claude Skills

| Skill | What it does |
|-------|--------------|
| `/fengmain` | Verifies main source code follows `site/uml/uml-*.md` patterns |
| `/fengsite` | Validates that UML files follow the `code-verification.md` template |
| `/fengtest` | Forward engineers test automation via `forward-engineer.bat` scripts |

---

## Claude Agents

| Agent | What it does |
|-------|--------------|
| `feng` | Verifies source code compliance against UML patterns; maintains cross-references between arch/uml/impl files |
