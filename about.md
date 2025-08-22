---
layout: page
title:  About
permalink: /about/
---

Splitting up a monolith into a [modular monolith](https://www.youtube.com/watch?v=5OjqD-ow8GE) (before going to micro-services) was the original technical problem I was trying to solve.
It's when the QA team gave the estimate of double the coding efforts that I went off on a tangent to understand why are QA teams the way they are.

This site describes how I would go about breaking up a monolith by using the QA team's natural language DSL as prompts for Claude Code. Instead of doing Specification by Example as originally intended in 2018, I now want to try what I'm calling Specification by Prompt. 
Each feature file would be treated as a prompt for Claude Code to write the code from scratch. The concept is to leverage the existing Gherkin/BDD specifications that QA teams already write and use them as detailed prompts for AI-driven development.

The approach would involve:
- Ensuring that individual feature files specify edge conditions and ranges making it easier to infer ranges.
- Using Claude Code to generate implementation code from these specifications
- Maintaining the living documentation aspect while enabling rapid development. If Claude Code gets it wrong, rather than chatting with it, I'd refine the specification and have it try agian.

I currently have some examples of how this would be done once a QA team works a certain way but I'm taking a break.
I need to clean-up the code first and document design choices etc, with help from Claude of course :).
Once I'm done with porting over the Xtext plug-in to VS Code and integrating it with Claude code I'll come back to this.

I'll try to mimic the structure of [Learn Microservices with Spring Boot](https://mosy.tech/products-overview/) but from the perspective of QA.
I'll develop examples of how to generate the mock services or data using the REST API.





