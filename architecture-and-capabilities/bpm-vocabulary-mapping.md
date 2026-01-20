# BPM Vocabulary Mapping

## Overview

This document maps the current testing-based vocabulary to BPM (Business Process Management) terminology. The goal is to transition toward business-domain-first language while maintaining compatibility with the existing UML Activity diagram approach.

## Why BPM Terminology?

- BPM and UML are closely related - BPMN (Business Process Model and Notation) was designed to complement UML
- Each test case is already a **path through a UML Activity diagram**
- BPM vocabulary emphasizes the business domain as the starting point
- Makes the methodology more accessible to non-testing stakeholders

## Core Vocabulary Mapping

| Current Testing Term | BPM/BPMN Term | Description |
|---------------------|---------------|-------------|
| **TestProject** | **Process** | A complete end-to-end workflow |
| **TestSuite** | **Sub-Process** or **Pool/Lane** | A grouping of related activities |
| **TestCase** | **Path** or **Flow** | A specific route through the activity diagram (one scenario) |
| **TestStep** | **Activity** or **Task** | A single action in the workflow |
| **TestData** | **Data Object** | Input/output artifacts flowing between activities |
| **TestSetup** | **Start Event** or **Gateway** | Initial conditions/branching logic |

## Developer Tooling Domain Example

| BPM Concept | Developer Tooling Example |
|-------------|---------------------------|
| **Process** | "Xtext Plugin Development" |
| **Sub-Process** | "Grammar Validation", "Code Generation", "IDE Integration" |
| **Task/Activity** | "Parse DSL File", "Validate Syntax", "Generate Java Class" |
| **Gateway (XOR)** | "If validation passes → generate; else → report errors" |
| **Data Object** | `.xtext` file, `.java` file, validation report |
| **Message Event** | IDE triggers (file save, build request) |

## Other Domain Examples

### Drug Claims Adjudication

| BPM Concept | Claims Example |
|-------------|----------------|
| **Process** | "Claim Adjudication" |
| **Sub-Process** | "Eligibility Check", "Benefit Calculation", "Payment Processing" |
| **Task/Activity** | "Verify Member ID", "Apply Copay Rules", "Generate EOB" |
| **Gateway (XOR)** | "If eligible → calculate benefits; else → deny claim" |
| **Data Object** | Claim form, member record, payment authorization |

### Electronic Health Records

| BPM Concept | EHR Example |
|-------------|-------------|
| **Process** | "Patient Encounter Processing" |
| **Sub-Process** | "Registration", "Clinical Documentation", "Billing" |
| **Task/Activity** | "Capture Vitals", "Record Diagnosis", "Generate Charge" |
| **Gateway (XOR)** | "If lab required → order tests; else → proceed to treatment" |
| **Data Object** | Patient record, lab order, clinical note |

## Migration Considerations

1. **Backward Compatibility**: Existing `.asciidoc` specs use testing vocabulary
2. **Xtext Grammar Updates**: Grammar rules need to accept both vocabularies during transition
3. **Code Generation**: UML class names (UMLTestProject, etc.) may need renaming or aliasing
4. **Documentation**: Update all references in `site/` documentation

## Related Work Items

- TODO: Create GitHub issue for vocabulary transition
- TODO: Define Xtext grammar changes
- TODO: Update UML class hierarchy
