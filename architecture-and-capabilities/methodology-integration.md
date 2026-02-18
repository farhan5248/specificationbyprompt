---
title: Methodology Integration
---

The SbP methodology is made up of **MDD** and **SDD**
1. **MDD:** Traditional code generation using DSL, UML and Graph models for language, code and test case generation.
2. **SDD:** AI code generation used to fill in challenges in MDD approach.

![Pipeline Overview](https://www.plantuml.com/plantuml/png/TL6nhfj04EpvYfLhKzX9cXIHOvCkhkme3SrnTyR8nowwMsH5qVltjph0vuTv58oO7SqEixl0odFVkYpS6koKLorugrbXxIpypq2UlTAtsbEFva2EFFozDco72NbZ_S3kdSYojon3OWkhmrKiR02j42wExb39-Awy2P239cD1Zug-Cueji0qSrYL6dCXMX6uzmCW5CAJ32bI0c8bzqsLPohmw5aMSjiQCF_0qt9HO54M9Fpqt5wLvC3B6p8LR4Pv-btWcSeCckBdcIucdwjVSB5HsBlnQuf_ZivaKFjTyAt7_cgmQayMNn8JSD_MLyn-FF8A9d7OB9d9Q9fX8CNuSgo9cQe4kSSDtQB3h-KEdfx7JiDZfPyct2wKUo3HOYxYso56erMQXsC9sZlwfojbaFDn1BRIxvBfTQFZycfEV6ewXKqTJCi543li54ZNj4amaBnKS5wI6vGjoGtSKVUnhPz78cTu1)


**MDD Pipeline**
1. **DDD Artifacts** - Business domain knowledge
2. **DSL Tool** - Xtext Framework defines an editor to write Ubiquitous language specifications. DDD provides concepts like bounded contexts, aggregates, and ubiquitous language.
    - Language grammar: `sheep-dog-main/sheep-dog-local/sheepdogxtextplugin.parent/sheepdogxtextplugin/src/org/farhan/dsl/sheepdog/SheepDog.xtext`
3. **SbE Artifacts** - Markup files describing the software behavior. Currently AsciiDoctor files.
    - Domain examples: `sheep-dog-main/sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/specs/*.asciidoc`
    - Domain objects: `sheep-dog-main/sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/stepdefs/*.asciidoc`
4. **UML Tool** - This is the red cycle in red-green-refactor. The test automation is created without AI and Claude isn't allowed to modify it.
    - UML transformation code: `sheep-dog-main/sheep-dog-local/sheep-dog-dev/src/main/java/org/farhan/mbt/*.java`
    - UML single source of truth: `sheep-dog-main/sheep-dog-local/sheep-dog-dev/target/uml/*.uml*`
5. **BDD Artifacts** - Sets of related tests are created with just one failing one and other similar passing ones.
    - Feature files: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src-gen/test/resources/cucumber/specs/*.feature`
    - Step definitions: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src-gen/test/java/org/farhan/stepdefs/*.java`
    - Dependency injection interfaces: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src-gen/test/java/org/farhan/objects/`
    - Jacoco test reports: `sheep-dog-main/sheep-dog-local/sheep-dog-test/target/site/jacoco-with-tests/`
 
**SDD Pipeline**
1. **BDD Artifacts** - Guice or Spring related classes created by Claude to connect test interfaces to main code.
    - Test code: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src/test/java/`
2. **Coding Agent** - This is the green cycle. Claude attempts to make tests pass using guidance from Markdown files and similar passing tests.
    - General project patterns and examples: `sheep-dog-main/site/`
    - Project specific UML patterns: `sheep-dog-main/sheep-dog-local/sheep-dog-test/site/`
    - Main code: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src/main/java/`
3. **Deployable Artifact** - Springboot service, Java Library, Eclipse Plugin, VS Code Extension
4. **Coding Agent** - This is the refactor cycle. Claude does a code review and updates the green cycle code.
    - SbP framework guidance: `sheep-dog-main/site/arch/arch-*.md`
5. **TDD Artifacts** - JUnit tests are created by Claude to cover retrying on failure etc. This isn't implemented yet fully because it'll sometimes make a test that passes instead of fixing the code :(
    - Unit tests for technical failures: `sheep-dog-main/sheep-dog-local/sheep-dog-test/src/test/java/org/farhan/runners/surefire`
