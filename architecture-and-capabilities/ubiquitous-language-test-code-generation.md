---
title: Ubiquitous Language Test Code Generation
---

# Ubiquitous Language Test Code Generation

Tools for behaviour-driven development that transform test specifications into executable code using a domain-specific language based on Domain Driven Design Ubiquitous Language principles.

## Projects

### Core Libraries

| Project | Description |
|---------|-------------|
| **sheep-dog-dev** | Transformation engine converting specifications to UML models and generating test code |
| **sheep-dog-test** | Shared interfaces and validation logic for specification quality checks |

### Language Support

| Project | Description | IDE |
|---------|-------------|-----|
| **sheepdogcucumber.parent** | Parser for Cucumber/Gherkin feature files | None |
| **sheepdogxtextplugin.parent** | Language support for AsciiDoc specifications | Eclipse |
| **xtextasciidocplugin.parent** | Language support via LSP | VS Code |

### Build Integration

| Project | Description | Mode |
|---------|-------------|------|
| **sheep-dog-dev-maven-plugin** | Maven plugin for transformations | Local |
| **sheep-dog-dev-svc-maven-plugin** | Maven plugin calling cloud service | Cloud |

### Cloud Services

| Project | Description |
|---------|-------------|
| **sheep-dog-dev-svc** | REST microservice for transformations with database persistence |
| **sheep-dog-dev-mcp** | MCP server for AI assistant integration |

## Project Dependencies

![Project Dependencies](https://www.plantuml.com/plantuml/png/ZL5DImCn4BtlhnZMyueY1VKWnTf3eOBOtYMTdTs6PfEGZxLG_EycMTTjQMqzX8GtRzwyl1bRnuppZSZiXalD36l043ecQq7F33UrKLwM4oMKWIkwhKOLxL5rOB3wYFYvvewoxASA-KGPWZbV6MOusRmNWbq60CCyoEsQI1UbgWj7rkN0BCJ7txIIsiGAzqbIuA17twp8N0VB93lH7ik-zbtEPx1KIYI3zzM87c1tTdBHi9PaeOMlkKowJIi_X48cplVP5a_teb2-l30JTKT7VU1-zZBzb_cWitMqTTta_L0YV1Hr0fb5pak5npsjHLNIOOpn3x0Wqt9CowwgwevkO7SnawGOxYWNrmv5yPGssw_h5uYkXd8uyJsatoD9B9okYXeUuNNorYD3uW5_dowMWsp_hp79qZVY6m00)
