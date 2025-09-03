---
layout: page
title:  About
permalink: /about/
---

Splitting up a monolith into a [modular monolith][1] (before going to micro-services) was the original technical problem I was trying to solve.
It's when the QA team gave the estimate of double the coding efforts that I went off on a tangent to understand why are QA teams the way they are.
In 2018 I thought that a QA team writing specs in a DSL would help drive development using TDD/BDD/SBE to rewrite the legacy code written in webMethods. 
I left the team in 2022 before ChatGPT came out and still thought that approach would work for COBOL.
Now I think those natural language DSL specs if done well combined with Claude Code can be used as prompts and so I call this approach Specification by Prompt (SBP).

The approach would involve:
- Ensuring that individual feature files specify edge conditions and ranges making it easier to infer ranges.
- Using Claude Code to generate implementation code from these specifications
- Maintaining the living documentation aspect while enabling rapid development using sub-agents. If Claude Code gets it wrong, rather than chatting with it, I'd refine the specification and have it try again.

# Personal Journey and Evolution

## The 2018-2022 Transformation Experience

My time with the QA teams from 2018-2022 was transformative in ways I didn't expect. What started as a technical problem (splitting monoliths) became a deep dive into organizational psychology and team dynamics.

The QA team's estimate of double the coding effort was a wake-up call. Instead of accepting it, I became obsessed with understanding why QA teams operate the way they do. This led me down a rabbit hole of learning about Dr. Deming, lean principles, and the psychology of change.

## Pre-AI vs Post-AI Thinking Evolution

**2018-2022 (Pre-AI Era)**: I believed natural language DSLs would drive TDD/BDD/SBE development for legacy system rewrites. The focus was on test-driven development and specification by example.

**2022+ (Post-AI Era)**: With the emergence of ChatGPT and Claude, I realized these same natural language specifications could serve as direct prompts for AI code generation. The approach evolved from test-driven to specification-driven development.

This shift represents more than just tooling - it's a fundamental change in how we think about the relationship between business requirements and code implementation.

## Lessons Learned

**What Worked**: The specifications my team wrote were so clear they could be reviewed by customers, executed by any tester, and used by junior developers on 40+ year old code with minimal supervision.

**What I Underestimated**: The time and effort required to change organizational culture. Technical solutions are easy compared to helping people see their work differently.

**Biggest Surprise**: The original test approach was created by developers, not QA. When the first QA analyst joined a decade ago, she simply continued their methodology and so did my team more than a decade later. This taught me that good practices often emerge organically and spread through institutional memory.

My intention is to complete the vscode plugin, the migration to micro-services and apply relevant design patterns all with help from Claude of course :).
Then I'll have claude document it all for reference later. 
I'll also ask claude to try and see what about the tests need to change to create the current code.
This would be to identify any missing coverage or identify extra descriptions of the behavior to be specified in the specs. 
Finally I'll delete the code and have claude recreate it incrementally, adding extra specs as needed to clarify the behavior.

[1]: https://www.youtube.com/watch?v=5OjqD-ow8GE