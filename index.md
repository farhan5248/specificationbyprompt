---
layout: home
title: Specification By Prompt
permalink: /
---

This goal is to develop a workflow which uses VS Code to give test case authors feedback earlier by using agents, MDD and MBT.
As I write test cases for my own code, I want feedback on:
- **coverage:** Is it already? 
- **impact:** How much of the regression will change? Maybe this is too big a change to make?
- **syntax:** Can test code be generated? If not, content assist and validation messages should offer suggestions.
- **completeness:** Does more data need to be added for test setup or mocking?
- **consistency:** Do the tests specify behaviour that unintentionally contradicts existing functionality? I'd need to generate the main code for this.

The extension is basically a custom markdown editor built with Xtext. 
It enforces the use of a natural language DSL to write the tests which are used as prompts for Claude Code.
The concept is to leverage the Gherkin/BDD specifications that QA teams already write and use them as detailed prompts for Spec Driven Development.


1. [Milestone Status][1]: It'll take me a few weeks to complete this. In the meantime, I maintain a Claude generated summary of my progress by combining the code base, agents, commands and the github issues milestone.
2. [Walkthrough of the tools][2]: There's new tools and frameworks being developed all the time. For now, parts of my work might be replaceable with tools like Beads or Gas Town. Claude generates these assessment reports of how tools I'm interested in can be used in my workflow. I'll start refactoring my work to use them in February most likely.
3. [How to Bake a Change][3]: I'll revisit this last, after the workflow is developed to document how I'd redo migrating a QA team from mass inspection to defect prevention to shift left on verfication testing.

[1]: milestone-status/index
[2]: walkthrough-of-tools/index
[3]: how-to-bake-a-change/index
