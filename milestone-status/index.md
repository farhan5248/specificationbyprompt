---
title: Milestone Status
---

# Milestone Status

These are all the milestones in Github Issues needed to develop the workflow.

1. [Verification with Markdown and Claude agents and skills][1]
    - Create skills and pattern specifications to verify structure and behavior from a given test case.
    - Clean-up the xtext related code such that Claude can verify the project structure by recognising patterns.
    - Fill in gaps in test-case coverage by reverse engineering.
2. Generate test code per test case using MCP
    - mvn goals operate on a batches of test cases selected by tags, for feedback later.
    - mcp tools operate on an individual test case selected in an editor, for feedback now.
3. Generate main code using Maven and MCP
    - Create mvn goal and mcp tool to generate the main code structure using Markdown API, Claude API.
    - Integrate Beads, Spec-Kit (or equivalent) to orchestrate red-green-refactor cycles to implement behavior for single project.
    - Integrate Gas Town to orchestrate tasks over multiple projects
4. Simulate tester implementing new functionality fully automatically
    - Migrate Asciidoc specifications to Markdown.
    - Generate mock-service data.
5. Rebuild all code-bases
    - Starting from Xtext or Spring Initializr generated project, apply code generation for each feature.
6. Develop graph model viewer with neo4j, d3.js and React.js
    - Convert tests to graph model in Neo4j DB.
    - Visualise graph model using D3.js and React.js.
7. Generate tests from graph model
    - Consider using Graph Walker or make something simpler.
    - Leverage AI to infer expressions to reduces edges in model.
    - Regenerate all test cases.

[1]: verification-with-markdown-and-claude-agents-and-skills