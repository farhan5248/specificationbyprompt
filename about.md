---
layout: page
title:  About
permalink: /about/
---

# About

A [modular monolith][1] migration where QA estimated double the coding effort sparked my question: why does testing take as long?
In 2018 I thought that a QA team writing specs in a DSL would help drive development using TDD/BDD/SbE to rewrite the legacy code written in webMethods. 

The goal of SbP (Specification by Prompt) is to fully automate code creation starting with SbE (Specification by Example). Instead of a person starting with a good example (test case or scenario), an AI coding agent starts with the same but it's now a prompt. 

The **sheep-dog-main** codebase implements the SbP methodology and doesn't just *use* DDD, MDD, TDD, BDD, SbE, and SDD - it [**automates the synchronization**][2] between their artifacts through bidirectional transformations. The sheep-dog codebase is also **self-developing** - it uses itself to build the tools that implement SbP. 

[1]: https://www.youtube.com/watch?v=5OjqD-ow8GE
[2]: architecture-and-capabilities/methodology-integration.md