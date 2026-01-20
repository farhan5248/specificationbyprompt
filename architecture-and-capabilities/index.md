---
title: Architecture & Capabilities
---

# Architecture & Capabilities

Start with the [methodology integration][2] to understand how SbP integrates DDD, MDD, TDD, BDD, SbE, and SDD. Then review the [Ubiquitous Language][3] technical considerations. The remaining pages are development milestones for new features.

1. [Ubiquitous Language Test Code Generation][0]
2. [Verification with Markdown and Claude agents and skills][1]
    - Create skills and pattern specifications to verify structure and behavior from a given test case.
    - Clean-up the xtext related code such that Claude can verify the project structure by recognising patterns.
    - Fill in gaps in test-case coverage by reverse engineering.
3. Generate test code per test case using MCP
    - mvn goals operate on a batches of test cases selected by tags, for feedback later.
    - mcp tools operate on an individual test case selected in an editor, for feedback now.
4. Generate main code using Maven and MCP
    - Create mvn goal and mcp tool to generate the main code structure using Markdown API, Claude API.
    - Integrate Beads, Spec-Kit (or equivalent) to orchestrate red-green-refactor cycles to implement behavior for single project.
    - Integrate Gas Town to orchestrate tasks over multiple projects
5. Simulate tester implementing new functionality fully automatically
    - Migrate Asciidoc specifications to Markdown.
    - Generate mock-service data.
6. Rebuild all code-bases
    - Starting from Xtext or Spring Initializr generated project, apply code generation for each feature.
7. Develop graph model viewer with neo4j, d3.js and React.js
    - Convert tests to graph model in Neo4j DB.
    - Visualise graph model using D3.js and React.js.
8. Generate tests from graph model
    - Consider using Graph Walker or make something simpler.
    - Leverage AI to infer expressions to reduces edges in model.
    - Regenerate all test cases.

[0]: ubiquitous-language-test-code-generation
[1]: verification-with-markdown-and-claude-agents-and-skills
[2]: methodology-integration
[3]: ubiquitous-language
