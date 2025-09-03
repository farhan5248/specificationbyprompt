---
layout: page
title: Ubiquitous Language
---

# Technical Implementation of Ubiquitous Language for QA Teams

This section details the technical approach for implementing ubiquitous language in QA transformation, covering tool selection, framework implementation, and automation strategies.

## Implementation Overview

The implementation of ubiquitous language was a natural language DSL, specifically an external DSL. The code generated was an internal DSL to make it easier to map from platform-specific implementation back to the business scenario.

Regardless of whether the test was intended to be automated or not in the current process, everything was written in that natural language DSL. This way, teams could decide later which tests to automate with minimal back and forth.

## Tool Selection Rationale

### Why Natural Language DSL vs Programming Languages?

Teaching large QA teams to code would be more challenging (detailed in the transformation stories), but consider the ski slope analogy from Sooner Safer Happier about taking everyone to the top of the hill. Instead, refining a language they already used to communicate with Business Systems Analysts and developers [reused skills they already had][2], requiring less tweaking to how they work.

The one constraint added to their language was related to [finite state machines as described by Bob Martin][6]. This enabled easy adoption of model-based testing and its benefits.

Most tests were system or end-to-end tests covering multiple technologies (Java, GGS, COBOL, PLSQL, webMethods etc). Which programming language should an entire diverse team standardize on?

### Why IDE vs Document-Based Tools?

**The Problem with Word/Excel/Confluence:**
Initially, teams used Microsoft Word and Excel files that were parsed and converted to automation. This was like coding with Microsoft Notepad and finding errors at compile time instead of while typing.

**The IDE Solution:**
When teams learned about jidoka and poka-yoke principles, they moved to [Xtext framework][8] which provides full Eclipse IDE support. Benefits included:

- **Code completion and content assist**: Browse through all possible steps from a page like browsing methods in a class
- **Real-time error detection**: Immediate feedback instead of runtime failures
- **Cross-reference capabilities**: Look up other scenarios and navigate through test suites
- **Language workbench benefits**: Full IDE support for domain-specific languages

You can read more about [language workbenches on Martin Fowler's site][7].

## Framework Implementation Details

### Xtext Framework Benefits

The [Xtext framework][8] provided an API to query all test cases written by the entire team. This is similar to the [Java Parser API][9] used for automatic Java code generation.

Through this API, teams could:
- Extract any data from all tests written by all testers
- Transform test specifications into any desired format
- Ensure the language used to describe the business domain mapped to the system being tested
- Generate automation code while maintaining business-readable specifications

### Automation Strategy

**Avoiding Common Pitfalls:**
The approach started with keyword-driven test automation but avoided its typical issues (devolving into a programming language) by maintaining the natural language constraint.

**Quality Benefits:**
If tests weren't going to be automated, was the extra detail wasteful for manual execution? No, because:
- Non-SMEs could help execute detailed manual tests
- Reduced toil and [overburden/muri][1] on specialist testers
- Maintained consistency between automated and manual test approaches

## Modern AI Integration

Though not considered at the time of original implementation, specifications written in this ubiquitous language can now be used as prompts for [specification by prompt][3] methodology with Claude Code, enabling AI to write the bulk of implementation code directly from business-readable test specifications.

This creates a powerful cycle:
1. QA writes business-readable specifications
2. Specifications serve as living documentation
3. Same specifications become AI prompts for code generation
4. Generated code maintains traceability to business requirements

## AI Code Generation Strategy

### Why Maintain Test Automation with AI Code Generation?

A critical question arises: if AI can read the specifications directly, why do we need test automation? And if we need it, why not have AI create the tests itself?

The answer is like asking a mental patient to write their own discharge evaluation from a mental institution - there's an inherent conflict of interest.

AI code generation is non-deterministic and prone to hallucinations. If you gave one automated test specification to 5 developers, you'd expect 5 different implementations of the main code - that's normal and acceptable. 

However, one test specification shouldn't have 5 different interpretations of the expected results. This is why I take the deterministic approach of creating test automation automatically. That code generation process is tested thoroughly and serves as an auditor of the AI coding agent's work.

### The Auditing Approach

This creates a system of checks and balances:
- **Specifications**: Written by domain experts (QA/business analysts) in natural language
- **Implementation**: Generated by AI from specifications  
- **Verification**: Deterministic test automation validates AI-generated code against original specifications

The test automation becomes the authoritative interpreter of business requirements, ensuring consistency and catching AI misinterpretations.

## Implementation Outcomes

The technical implementation enabled:
- **Quality built-in**: Tests running alongside developers in lower environments
- **Reduced inspection dependency**: Business component tests before QA environment delivery
- **Cross-technology coverage**: Single language for diverse technology stacks
- **Team scalability**: Non-specialists can contribute to test execution
- **Future-ready approach**: Specifications ready for AI-assisted development

[1]: /demingdriventesting/migrating-from-defect-inspection-to-prevention/muri
[2]: /demingdriventesting/communicating-the-strategy-to-qa/the-neo-nurture-incubator
[3]: /specificationbyprompt/index
[4]: https://martinfowler.com/bliki/UbiquitousLanguage.html
[5]: https://youtube.com/clip/UgkxwDpbV3Wzrdz0mNow9cglz9_KJuxLmj25?si=6Sx67uKN7UoKukVM
[6]: https://blog.cleancoder.com/uncle-bob/2018/06/06/PickledState.html
[7]: https://martinfowler.com/articles/languageWorkbench.html
[8]: https://eclipse.dev/Xtext/
[9]: https://javaparser.org/