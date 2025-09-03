---
layout: home
title: Specification By Prompt
permalink: /
---

This site describes how I would go about breaking up a monolith or modernising legacy code by using the QA team's natural language DSL as prompts for Claude Code. 
I'll try to mimic the structure of [Learn Microservices with Spring Boot][2] but from the perspective of QA.
Each feature file would be treated as a prompt for Claude Code to write the code from scratch. 
The concept is to leverage the existing Gherkin/BDD specifications that QA teams already write and use them as detailed prompts for AI-driven development.

1. [Walkthrough of the tools][3]: Framework integration overview with design choices and use cases
2. [How to Bake a Change][1]: Process description with tool references rather than principles
3. [Rebuilding the code base][4]: Rebuilding sheep-dog-main using behavior specifications and CLAUDE files

[1]: how-to-bake-a-change/index
[2]: https://mosy.tech/products-overview/
[3]: walkthrough-of-tools/index
[4]: rebuilding-the-code-base/index
