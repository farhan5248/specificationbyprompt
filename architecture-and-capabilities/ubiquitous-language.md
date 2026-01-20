---
layout: page
title: Ubiquitous Language
---

# Technical Considerations of DDD Ubiquitous Language for QA Teams

This section details the technical approach for implementing ubiquitous language in QA transformation, covering tool selection, framework implementation, and automation strategies.

## Implementation Overview

The implementation of ubiquitous language was a natural language DSL, specifically an external DSL. 
The code generated was an internal DSL to make it easier to map from platform-specific implementation back to the business scenario.

Regardless of whether the test was intended to be automated or not in the current process, everything was written in that natural language DSL. 
If tests weren't going to be automated, was the extra detail wasteful for manual execution? No, because:
- Non-SMEs could help execute detailed manual tests which reduced [toil/overburden/muri][1] on SME testers
- Maintained consistency between automated and manual test approaches. This way, teams could decide as late as possible which tests to automate with minimal back and forth between tester and test automation developer.

## Tool Selection Rationale

### Why Natural Language DSL vs Programming Languages?

Teaching large QA teams to code would be challenging as described in the analogy about taking everyone to the top of the hill all at once to teach them to ski from [Sooner Safer Happier][10]. 
Instead, refining a language they already used to communicate with Business Systems Analysts and developers [reused skills they already had][2], requiring less modification to how they work.

The other reason was, most tests were system or end-to-end tests covering multiple technologies (Java, GGS, COBOL, PLSQL, webMethods etc). 
Which programming language should an entire diverse team standardize on and what happens when those languages get replaced with modern ones?
Using a natural language, allowed us to be technology agnostic. 

The one constraint added to their language was related to [finite state machines as described by Bob Martin][6]. 
This enabled easy adoption of model-based testing and its benefits such as incremental granular test case generation used as prompts for agentic coding tools.

One challenge was ensuring that the DSL didn't devolve into a programming language.
Every now and then folks would try to treat it as a programming language which made the specifications awkward to read. Robot Framework for example allowed you to embed variables and program code anywhere in a robot file. You couldn't read the test without having to simulate the embedded code to understand the test expectation.

### Why IDE vs Document-Based Tools?

Initially, teams used Microsoft Word and Excel files that were parsed and converted to automation. 
This was like coding with Microsoft Notepad and finding errors at compile time instead of while typing.

When the teams learned about jidoka and poka-yoke principles, they moved to the [Xtext framework][8] which provides full Eclipse IDE support. Benefits included:

- **Code completion and content assist**: Browse through all possible steps from a page like browsing methods in a class. Get suggestions for next steps in a scenario.
- **Real-time error detection**: Immediate feedback instead of runtime failures in test automation about fields not existing
- **Cross-reference capabilities**: Look up other scenarios by right-clicking an element and find references.
- **Language workbench benefits**: Full IDE support for domain-specific languages such as automated creation of test automation.

The [Xtext framework][8] also provided an API to query all test cases written by the entire team. 
This is similar to the [Java Parser API][9] used for automatic Java code generation. Through this API, teams could:
- Extract any data from all tests written by all testers
- Transform test specifications into any desired format for plain data injection into databases
- Generate automation code while maintaining business-readable specifications
- Ensure the language used to describe the business domain mapped to the system being tested

You can read more about [language workbenches on Martin Fowler's site][7].

## Modern AI Integration

Though not practiced at the time of original implementation, specifications written in this ubiquitous language can now be used as prompts for agentic coding tools, enabling AI to write the bulk of implementation code directly from business-readable test specifications.

This creates a powerful cycle:
1. QA writes business-readable specifications
2. Specifications serve as living documentation
3. Same specifications become AI prompts for code generation
4. Generated code maintains traceability to business requirements

### The Auditing Approach

A critical question arises: if AI can read the specifications directly, why do we need test automation? And if we need it, why not have AI create the tests itself?

The answer is like asking a mental patient to write their own discharge evaluation from a mental institution - there's an inherent conflict of interest.

AI code generation is non-deterministic and prone to hallucinations. If you gave one automated test specification to 5 developers, you'd expect 5 different implementations of the main code - that's normal and acceptable. 

However, one test specification shouldn't have 5 different interpretations of the expected results. This is why I take the deterministic approach of creating test automation automatically. That code generation process is tested thoroughly and serves as an auditor of the AI coding agent's work.

This creates a system of checks and balances:
- **Specifications**: Written by domain experts (QA/business analysts) in natural language
- **Verification**: Deterministic test automation drives the development of AI-generated code
- **Implementation**: Generated by AI from specifications  

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
[10]: https://itrevolution.com/product/sooner-safer-happier/
