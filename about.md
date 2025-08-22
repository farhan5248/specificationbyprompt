---
layout: page
title:  About
permalink: /about/
---

Splitting up a monolith into a [modular monolith](https://www.youtube.com/watch?v=5OjqD-ow8GE) (before going to micro-services) was the original technical problem I was trying to solve.
It's when the QA team gave the estimate of double the coding efforts that I went off on a tangent to understand why are QA teams the way they are.
In 2018 I thought that a QA team writing specs in a DSL would help drive development using TDD/BDD/SBE to rewrite the legacy code written in webMethods. 
I left the team in 2022 before ChatGPT came out and still thought that approach would work for COBOL.
Now I think those natural language DSL specs if done well combined with Claude Code can be used as prompts and so I call this approach Specification by Prompt (SBP).

This site describes how I would go about breaking up a monolith or modernising legacy code by using the QA team's natural language DSL as prompts for Claude Code. 
I'll try to mimic the structure of [Learn Microservices with Spring Boot](https://mosy.tech/products-overview/) but from the perspective of QA.
Each feature file would be treated as a prompt for Claude Code to write the code from scratch. 
The concept is to leverage the existing Gherkin/BDD specifications that QA teams already write and use them as detailed prompts for AI-driven development.

The approach would involve:
- Ensuring that individual feature files specify edge conditions and ranges making it easier to infer ranges.
- Using Claude Code to generate implementation code from these specifications
- Maintaining the living documentation aspect while enabling rapid development using sub-agents. If Claude Code gets it wrong, rather than chatting with it, I'd refine the specification and have it try again.

Why do I think this works? 
The tests my team wrote were so clear that
1. They could be given to the customer for review
2. They could be understand by another tester without any assumed knowledge to be executed
3. They could be automatically converted into test automation provided the mapping to the architecture
4. They could be used by junior developers to write the code

I should mention that the way my team wrote their tests was developed by the original developers of the system before there ever was a QA team.
Then when the first QA analyst was hired (I spoke to her) she just continued their way of working.
So are the tests my team wrote unit tests because the developers came up with the process or acceptance tests because my QA team runs it? 
To me, it's a unit test as defined by Ian Cooper in his presentation.

My intention is to complete the vscode plugin, the migration to micro-services and apply relevant design patterns all with help from Claude of course :).
Then I'll have claude document it all for reference later. 
I'll also ask claude to try and see what about the tests need to change to create the current code.
This would be to identify any missing coverage or identify extra descriptions of the behavior to be specified in the specs. 
Finally I'll delete the code and have claude recreate it incrementally, adding extra specs as needed to clarify the behavior.