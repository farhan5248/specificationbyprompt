---
layout: home
title: Specification By Prompt
permalink: /
---

This goal is to develop a workflow to generate the main code fully automatically like a build process.
I'm currently able to do this using custom IDE extensions (Eclipse and VS Code), Claude Code, Eclipse Xtext Framework and Eclipse Modelling Framework.

The extensions are basically custom markup editors built with Xtext. Right now they're based on AsciiDoctor but I'll move to Markdown.
They enforce the use of a natural language DSL to write the tests which are used in prompts for Claude Code. The prompt is simple, fix the failing test.

1. [Architecture & Capabilities][1]: Overview of the Sheep Dog projects and their usage.
2. [How to Bake a Change][2]: I'll revisit this last, after the workflow is developed to document how I'd redo migrating a QA team from mass inspection to defect prevention to shift left on verfication testing using AI.

[1]: architecture-and-capabilities/index
[2]: how-to-bake-a-change/index
