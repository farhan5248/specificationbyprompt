# Methodology Integration

## Pipeline Flow

The SbP methodology is made up of **MDD** and **SDD**
1. **MDD:** Traditional code generation using DSL, UML and Graph models for language, code and test case generation.
2. **SDD:** AI code generation used to fill in gaps in MDD approach.

![Pipeline Overview](https://www.plantuml.com/plantuml/png/TL6nhfj04EpvYfLhKzX9cXIHOvCkhkme3SrnTyR8nowwMsH5qVltjph0vuTv58oO7SqEixl0odFVkYpS6koKLorugrbXxIpypq2UlTAtsbEFva2EFFozDco72NbZ_S3kdSYojon3OWkhmrKiR02j42wExb39-Awy2P239cD1Zug-Cueji0qSrYL6dCXMX6uzmCW5CAJ32bI0c8bzqsLPohmw5aMSjiQCF_0qt9HO54M9Fpqt5wLvC3B6p8LR4Pv-btWcSeCckBdcIucdwjVSB5HsBlnQuf_ZivaKFjTyAt7_cgmQayMNn8JSD_MLyn-FF8A9d7OB9d9Q9fX8CNuSgo9cQe4kSSDtQB3h-KEdfx7JiDZfPyct2wKUo3HOYxYso56erMQXsC9sZlwfojbaFDn1BRIxvBfTQFZycfEV6ewXKqTJCi543li54ZNj4amaBnKS5wI6vGjoGtSKVUnhPz78cTu1)


**MDD Pipeline**
1. **DDD Artifacts** - Business domain knowledge
2. **DSL Tool** - Xtext Framework defines an editor to write Ubiquitous language specifications. DDD provides bounded contexts, aggregates, and ubiquitous language.
    - Language grammar: `sheep-dog-main/sheep-dog-local/sheepdogxtextplugin.parent/sheepdogxtextplugin/src/org/farhan/dsl/sheepdog/SheepDog.xtext`
3. **SbE Artifacts** - AsciiDoctor
    - Domain examples: `sheep-dog-main/sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/specs/*.asciidoc`
    - Domain objects: `sheep-dog-main/sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/stepdefs/*.asciidoc`
4. **UML Tool** - EMF Framework is used to create **PIT** (Platform Independent Test Model) from which test automation artifacts are generated.
    - UML transformation code: `sheep-dog-main/sheep-dog-local/sheep-dog-dev/src/main/java/org/farhan/mbt/*.java`
    - UML single source of truth: `sheep-dog-main/sheep-dog-local/sheep-dog-dev/target/uml/*.uml*`
5. **BDD Artifacts** - Cucumber Framework is used to create automated acceptance tests
    - Feature files: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src-gen/test/resources/cucumber/specs/*.feature`
    - Step definitions: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src-gen/test/java/org/farhan/stepdefs/*.java`
    - Dependency injection interfaces: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src-gen/test/java/org/farhan/objects/`
    - Acceptance tests: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src/test/java/org/farhan/runners/failsafe`
 
**SDD Pipeline**
1. **BDD Artifacts** - Automated acceptance tests drive coding agent development
2. **Coding Agent** - Claude Code Coding Agent attempts to make tests pass using guidance from Markdown files
    - SbP red to green skill: `sheep-dog-main/site/sbp/sbp-main-forward.md`
    - SbP framework usage examples: `sheep-dog-main/site/impl/impl-*.md`
    - UML patterns: `sheep-dog-main/sheep-dog-local/sheep-dog-test/site/uml/uml-*.md`
3. **Deployable Artifact** - Springboot service, Java Library, Eclipse Plugin, VS Code Extension
    - Main code: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src/main`
4. **Coding Agent** - Claude Code updates Markdown files guidance or refactors code to be compliant adding additional non-acceptance tests
    - SbP green to refactor skill: `sheep-dog-main/site/sbp/sbp-main-verify.md`
    - SbP update guidance docs skill: `sheep-dog-main/site/sbp/sbp-main-reverse.md`
    - SbP framework guidance: `sheep-dog-main/site/arch/arch-*.md`
5. **TDD Artifacts** - JUnit tests are created by Claude to cover retrying on failure etc
    - Unit tests for technical failures: `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src/test/java/org/farhan/runners/surefire`

## Domain-Agnostic Methodology

The current business domain is **software development tooling** (Eclipse plugins, VSCode extensions, Maven plugins, MCP tools):

- **Domain:** Testing vocabulary (TestProject, TestSuite, TestCase, TestStep) reflects the shift-left verification origins. Expand Xtext to validate `uml-*.md` files; transition vocabulary from testing terms toward BPM (Business Process Management) terms to emphasize business domain as starting point.
- **Grammar Vocabulary:** Xtext grammar defines the specification language using `.asciidoc`. In the future it will be a single documentation language covering IDE tooling terms. Claude will start directly from markdown specs.
- **Generated output:** Includes IDE extensions for code generation and validation

The SbP methodology is not limited to software development tooling. The same approach applies to any business domain with well-defined processes - what changes is the vocabulary and target output:

| Domain | Grammar Vocabulary | Generated Output |
|--------|-------------------|------------------|
| Software Development | IDE tooling terms | Code generation plugins |
| Drug Claims Adjudication | Claims processing terms | Adjudication logic |
| Electronic Health Records | EHR data/workflow terms | Record processing code |

## Model-Driven Development (MDD)

MDD is to generate **executable BDD tests** (Cucumber) from **human-readable specifications** (AsciiDoc) with , with UML as the transformation hub.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Central Model** | UML/EMF models as primary artifact | UML model created from DDD ubiquitous language specs (`.asciidoc`) |
| **Code Generation** | Generate code from models | Test automation (`.feature` and `.java`) generated from UML model |
| **Model Transformations** | M2M and M2T transformations | `asciidoctor-to-uml` → `uml-to-cucumber` transformation chain |
| **Platform Independence** | Models abstract away implementation | UML serves as platform-independent intermediate representation |
| **Round-Trip Engineering** | Sync model and code changes | Bidirectional transforms: model ↔ tests ↔ documentation |

## Specification-Driven Development (SDD)

**Claude Code** uses the BDD specs to iteratively develop the main code, guided by two complementary specification types:
- **UML patterns** (`sheep-dog-main/site/uml/*.md`) - Define structural rules (packages, classes, methods, interactions, logging)
- **Feature files** (`.feature`) - Define behavioral expectations (Given-When-Then scenarios)

The SbP workflow implements the classic TDD/BDD **Red-Green-Refactor** cycle:
1. **Red** - MDD generates test code (`.feature` + step definitions) from specs; tests fail because main code doesn't exist yet
2. **Green** (`/sbp -f -c`) - Claude Code cross-references each scenario and generates main code to make tests pass
3. **Refactor** (`/sbp -v -m` then `/sbp -f -m`) - Verify main code against UML patterns, then fix violations to ensure structural compliance

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Specs as Source of Truth** | API specs (OpenAPI, etc.) drive implementation | UML pattern files (`sheep-dog-main/site/uml/uml-*.md`) AND `.feature` files together define structure and behavior |
| **Contract-First** | Define contracts before implementation | UML patterns define class structure, packages, method signatures; `.feature` files define behavior |
| **Code Generation** | Generate stubs/skeletons from specs | Claude Code reads UML specs + `.feature` files and generates/fixes main code |
| **Validation** | Generated code validated against specs | `/sbp -v -m` verifies main code against UML patterns, Cucumber tests verify behavior |
| **AI-Assisted Generation** | Emerging practice | Claude Code uses SbP workflow: verify → fix → verify cycle |

## Domain-Driven Design (DDD)

SbP treats the *transformation pipeline itself* as the domain.

| Aspect | Standard Practice | SbP Implementation       |
|--------|-------------------|--------------------------|
| **Ubiquitous Language** | Shared vocabulary between devs & domain experts | Defined in `.asciidoc` specifications under "Ubiquitous Language" folder |
| **Bounded Contexts** | Autonomous domains with own models | 4 repositories: `sheep-dog-qa`, `sheep-dog-local`, `sheep-dog-cloud`, `sheep-dog-ops` |
| **Aggregates** | Clusters of entities as single unit | `UMLTestProject` → `UMLTestSuite` → `UMLTestCase` → `UMLTestStep` |
| **Value Objects** | Immutable domain concepts | `UMLElement`, `UMLTestData`, `UMLTestSetup` |
| **Repository Pattern** | Abstraction from persistence | `SourceFileRepository` (local files), `ServiceMySQLRepository` (cloud) |

## Specification by Example (SbE)

After the domain is defined, examples are developed to ensure domain & tech alignment. The **DSL Tool** (Xtext) provides the editor for writing SbE artifacts.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Concrete Examples** | Tables showing input/output | AsciiDoc tables with transformation examples |
| **Executable Specs** | Examples that run as tests | Feature files execute as Cucumber tests |
| **Shared Understanding** | Business & tech alignment | UML serves as shared model between documentation and code |
| **Living Documentation** | Specs stay current | Bidirectional transforms keep all artifacts synchronized |

## Behavior-Driven Development (BDD)

The **UML Tool** (EMF) generates BDD test automation artifacts from SbE specifications. These drive the development of the main code using **Claude Code**.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Gherkin Syntax** | Given-When-Then scenarios | `.feature` files generated from `.asciidoc` specs |
| **Cucumber Framework** | Execute specifications | Cucumber 7.14.1 with JUnit 5, Guice/Spring DI |
| **Living Documentation** | Tests as documentation | Bidirectional: docs generate tests AND tests can update docs |
| **Step Definitions** | Map Gherkin to code | `sheep-dog-main/sheep-dog-cloud/sheep-dog-dev-svc/src-gen/test/java/org/farhan/stepdefs/` |
| **Step Objects** | Domain objects in tests | `UMLStepObject`, `AsciiDoctorStepObject`, `CucumberStepObject` |

## Test-Driven Development (TDD)

The **Coding Agent** generates TDD artifacts - tests not covered by BDD generated specs to fill in gaps in coverage using **Red-Green-Refactor** cycles.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Red-Green-Refactor** | Write failing test → implement → refactor | UML patterns define expected structure; code generated using them |
| **Unit Tests** | Test individual units in isolation | Test runners organized by transformation direction |
| **Test Coverage** | Measure code coverage | JaCoCo plugin configured for coverage reporting |
| **Test-First** | Tests written before code | Specifications (`.asciidoc`) written before implementation |

