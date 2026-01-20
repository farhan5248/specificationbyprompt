# Methodology Integration

## Pipeline Flow

```
┌─────────────────┐         ┌─────────────┐         ┌─────────────────┐         ┌─────────────────┐
│   AsciiDoc      │ ──────► │             │ ──────► │   Cucumber      │ ──────► │   Main Code     │
│   Documentation │   MDD   │  UML Model  │   MDD   │   Feature Files │   SDD   │   (Java/TS)     │
│   (.asciidoc)   │ ◄────── │  (Central)  │ ◄────── │   (.feature)    │         │   (.java/.ts)   │
└─────────────────┘         └─────────────┘         └─────────────────┘         └─────────────────┘
       │                          │                        │                           │
       │                          │                        │                           │
       │    asciidoctor-to-uml    │     cucumber-to-uml    │      Claude Code          │
       │    uml-to-asciidoctor    │     uml-to-cucumber    │                           │
       ▼                          ▼                        ▼                           ▼
   Business-readable          Domain Model            Executable Tests           Implementation
   Documentation              (Single Source          (BDD Specs define          (Generated to pass
   (DDD Ubiquitous            of Truth)               structure & behavior)       BDD tests)
    Language)
```

1. **DDD → MDD**: Ubiquitous language specifications (`.asciidoc`) are transformed into a UML model. DDD provides bounded contexts, aggregates, and ubiquitous language.
2. **MDD → BDD**: UML model generates Cucumber test automation (`.feature` files and step definitions). SbE uses concrete examples as the bridge between business and technical artifacts.
3. **BDD → SDD**: AI coding agents read `.feature` tests and `.md` design specs and generate main code that implements them. TDD ensures code quality through the Red-Green-Refactor cycle.
4. **Round-Trip**: Changes can flow backwards - code changes update tests, tests update model, model updates docs. SbP orchestrates AI-assisted verification and generation across the pipeline.

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

## Domain-Driven Design (DDD)

SbP treats the *transformation pipeline itself* as the domain.

| Aspect | Standard Practice | SbP Implementation       |
|--------|-------------------|--------------------------|
| **Ubiquitous Language** | Shared vocabulary between devs & domain experts | Defined in `.asciidoc` specifications under "Ubiquitous Language" folder |
| **Bounded Contexts** | Autonomous domains with own models | 4 repositories: `sheep-dog-qa`, `sheep-dog-local`, `sheep-dog-cloud`, `sheep-dog-ops` |
| **Aggregates** | Clusters of entities as single unit | `UMLTestProject` → `UMLTestSuite` → `UMLTestCase` → `UMLTestStep` |
| **Value Objects** | Immutable domain concepts | `UMLElement`, `UMLTestData`, `UMLTestSetup` |
| **Repository Pattern** | Abstraction from persistence | `SourceFileRepository` (local files), `ServiceMySQLRepository` (cloud) |

**Key Files**
- Xtext grammar: `sheep-dog-local/sheepdogxtextplugin.parent/sheepdogxtextplugin/src/org/farhan/dsl/sheepdog/SheepDog.xtext`
- Ubiquitous Language: `sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/specs/ubiquitous-language/`
- Core domain: `sheep-dog-local/sheep-dog-dev/src/main/java/org/farhan/mbt/core/`

## Specification by Example (SbE)

After the domain is defined, examples are developed to ensure domain & tech alignment.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Concrete Examples** | Tables showing input/output | AsciiDoc tables with transformation examples |
| **Executable Specs** | Examples that run as tests | Feature files execute as Cucumber tests |
| **Shared Understanding** | Business & tech alignment | UML serves as shared model between documentation and code |
| **Living Documentation** | Specs stay current | Bidirectional transforms keep all artifacts synchronized |

**Key Files**
- Language mapping specs with concrete examples: `sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/specs/language-mapping/`
- Usage examples: `sheep-dog-qa/sheep-dog-specs/src/test/resources/asciidoc/specs/usage/`

## Model-Driven Development (MDD)

MDD is to generate **executable BDD tests** (Cucumber) from **human-readable specifications** (AsciiDoc) with , with UML as the transformation hub.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Central Model** | UML/EMF models as primary artifact | UML model created from DDD ubiquitous language specs (`.asciidoc`) |
| **Code Generation** | Generate code from models | Test automation (`.feature` and `.java`) generated from UML model |
| **Model Transformations** | M2M and M2T transformations | `asciidoctor-to-uml` → `uml-to-cucumber` transformation chain |
| **Platform Independence** | Models abstract away implementation | UML serves as platform-independent intermediate representation |
| **Round-Trip Engineering** | Sync model and code changes | Bidirectional transforms: model ↔ tests ↔ documentation |

**Key Files**
- Transformation chain: `asciidoctor-to-uml` → `uml-to-cucumber` Maven goals
- UML model classes: `sheep-dog-local/sheep-dog-dev/src/main/java/org/farhan/mbt/core/UML*.java`
- Generated test automation: `src-gen/test/resources/cucumber/specs/` and `src-gen/test/java/org/farhan/stepdefs/`

## Behavior-Driven Development (BDD)

The generated BDD test automation drives the development of the main code using **Claude Code**.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Gherkin Syntax** | Given-When-Then scenarios | `.feature` files generated from `.asciidoc` specs |
| **Cucumber Framework** | Execute specifications | Cucumber 7.14.1 with JUnit 5, Guice/Spring DI |
| **Living Documentation** | Tests as documentation | Bidirectional: docs generate tests AND tests can update docs |
| **Step Definitions** | Map Gherkin to code | `src-gen/test/java/org/farhan/stepdefs/` |
| **Step Objects** | Domain objects in tests | `UMLStepObject`, `AsciiDoctorStepObject`, `CucumberStepObject` |

**Key Files**
- Feature files: `sheep-dog-local/sheep-dog-dev/src-gen/test/resources/cucumber/specs/`
- Step definitions: `sheep-dog-local/sheep-dog-dev/src-gen/test/java/org/farhan/stepdefs/`

## Specification-Driven Development (SDD)

**Claude Code** uses the BDD specs to iteratively develop the main code, guided by two complementary specification types:
- **UML patterns** (`site/uml/`) - Define structural rules (packages, classes, methods, interactions, logging)
- **Feature files** (`.feature`) - Define behavioral expectations (Given-When-Then scenarios)

The SbP workflow implements the classic TDD/BDD **Red-Green-Refactor** cycle:
1. **Red** - MDD generates test code (`.feature` + step definitions) from specs; tests fail because main code doesn't exist yet
2. **Green** (`/sbp -f -c`) - Claude Code cross-references each scenario and generates main code to make tests pass
3. **Refactor** (`/sbp -v -m` then `/sbp -f -m`) - Verify main code against UML patterns, then fix violations to ensure structural compliance

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Specs as Source of Truth** | API specs (OpenAPI, etc.) drive implementation | UML pattern files (`site/uml/uml-*.md`) AND `.feature` files together define structure and behavior |
| **Contract-First** | Define contracts before implementation | UML patterns define class structure, packages, method signatures; `.feature` files define behavior |
| **Code Generation** | Generate stubs/skeletons from specs | Claude Code reads UML specs + `.feature` files and generates/fixes main code |
| **Validation** | Generated code validated against specs | `/sbp -v -m` verifies main code against UML patterns, Cucumber tests verify behavior |
| **AI-Assisted Generation** | Emerging practice | Claude Code uses SbP workflow: verify → fix → verify cycle |

**Key Files**
- UML structural patterns: `site/uml/uml-class-*.md`, `uml-package.md`, `uml-communication.md`, `uml-interaction.md`
- Feature files as behavioral specs: `.feature` files define expected behavior
- SbP workflow prompts: `site/sbp/sbp-main-verify.md`, `sbp-main-forward.md` - guide Claude Code to verify and fix
- Verification reports: `site/uml/uml-*.main-report.md` - track compliance status

## Test-Driven Development (TDD)

**Claude Code** generates tests not covered by BDD generated specs to fill in gaps in coverage using **Red-Green-Refactor** cycles.

| Aspect | Standard Practice | sheep-dog Implementation |
|--------|-------------------|--------------------------|
| **Red-Green-Refactor** | Write failing test → implement → refactor | UML patterns define expected structure; code generated using them |
| **Unit Tests** | Test individual units in isolation | Test runners organized by transformation direction |
| **Test Coverage** | Measure code coverage | JaCoCo plugin configured for coverage reporting |
| **Test-First** | Tests written before code | Specifications (`.asciidoc`) written before implementation |

**Key Files**
- Forward engineering workflow: `site/sbp/sbp-main-forward.md`
- UML patterns: `site/uml/uml-class-*.md` files
- Test runners: `sheep-dog-local/sheep-dog-dev/src/test/java/org/farhan/runners/`

