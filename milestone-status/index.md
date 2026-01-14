---
title: Milestone Status
---

# Milestone Status

These are all the milestones in Github Issues needed to develop the workflow.

1. [Verification with Markdown and Claude agents and skills][1]
    - Create skills and pattern specifications to verify structure and behavior from a given test case.
    - Clean-up the xtext related code such that Claude can verify the project structure by recognising patterns.
    - Fill in gaps in test-case coverage by reverse engineering.
2. Refactor services for MCP
    - Split sheep-dog-dev and the service into 2
    - Create pattern specifications for all remaining projects
    - Complete MCP service
3. Integrate Beads, Gas Town etc
4. Generate new functionality fully automatically
    - Migrate Asciidoc specifications to Markdown 
    - Generate mock-service data
5. Rebuild all code-bases
    - Starting from Xtext or Spring Initializr generated project, apply code generation for each feature
6. Develop graph model viewer with neo4j, d3.js and React.js
    - Convert tests to graph model in Neo4j DB
    - Visualise graph model using D3.js and React.js
7. Generate tests from graph model
    - Consider using Graph Walker or make something simpler
    - Leverage AI to infer expressions to reduces edges in model
    - Regenerate all test cases

[1]: verification-with-markdown-and-claude-agents-and-skills