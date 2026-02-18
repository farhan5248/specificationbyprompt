---
title: Code Generation
---

* TOC
{:toc}

---

# What Darmok does

These are my notes on the current version of a small program I call Darmok.
Darmok is currently a powershell script which puts back the java code in `src/main/java` after I delete the implementation.
It iterates though a series of test cases going through the red-green-refactor cycle for each of them.

## What is Rebuilt

- **Issues Package**: `src/main/java/org/farhan/dsl/issues` can be put back completely after I empty out the method bodies
- **Language Package**: `src/main/java/org/farhan/dsl/lang` isn't currently being auto-generated. Its tests are non-Gherkin style tests which I'll be converting this week.
- **MBT Code**: `src-gen/test` is off-limits for Claude and has the `.feature` test scenario files and and `.java` glue code and interfaces. I'll move more of the boiler plate code from `src/test/java` to this directory.
- **Non MBT Code**: `src/test/java` is also off-limits for Claude. It has code that connects the generated test automation to the main code. Though Claude can generate this, it occasionally hardcodes responses just to make the test pass so I excluded it for the first set of fully automated runs. I'll update the MBT code generation to address that. It's because of this, that I have to empty out the method bodies rather than outright deleting the class or the code won't compile.

## Before Darmok Runs

Before I attempted to delete everything, I went through a few cycles of renaming.
This was going to be important for when I asked Claude to create tests in the DSL to fill in gaps; I'll explain more in **After Darmok Runs**.

I asked Claude to read a definition of my domain, in this case the Xtext language grammar `sheep-dog-main/sheep-dog-local/sheepdogxtextplugin.parent/sheepdogxtextplugin/src/org/farhan/dsl/sheepdog/SheepDog.xtext` and compare it to the main code class, public method and enum names. 
Its output was the contents of the `sheep-dog-main/sheep-dog-local/sheep-dog-test/site/uml` directory.
I also had to provide it with other terms which altogether act as variables such as `{Language}` or `{Issue}`

The complete list of variables for `sheep-dog-test` is `site/uml/uml-overview.md`. 
Below is an example of the `org.farhan.dsl.lang.SheepDogUtility` class
Anything that didn't fit a pattern, I decided to make those utility methods `private` and let them be duplicated.

```
# {Language}Utility

Static helper methods for grammar element operations. Separates utility operations from grammar model classes, keeping interfaces focused on data access.

## get{Assignment}AsString

**Desc**: Converts a list of grammar elements into a formatted string representation for display or comparison purposes.

**Rule**: SOME method names follow get{Assignment}AsString pattern.
 - **Name**: `^get{Assignment}AsString$`
 - **Return**: `^String$`
 - **Parameters**: `^\(List<I{Type}>\s+\w+\)$`
 - **Modifier**: `^public\s+static$`

**Examples**:
 - `public static String getCellListAsString(List<ICell> list)`
```

## After Darmok Runs

Even though I had tests, they weren't all good enough for Claude to infer the complete implementation.
Before I deleted and recreated the code, I asked it to use **JaCoCo** reports and walk through the code and find parts of the code that had coverage but whose logic wouldn't be implied by the current tests. It was interesting because though I thought my test was clear about the expectations, I had implemented things that it wouldn't have thought of. 

After watching a few runs, I decided to take another approach. I tried to rebuild parts of the code with the tests I had.
I deleted parts selectively, one method body at a time to focus it.
I asked Claude to compare the branches and it pointed out what was missing and made a test for that.
Then I deleted the code and it tried again and identified the next gaps, sometime for the same method body but for a branch it missed the last time.

I'm interested in this approach because I'm curious about how easily I can get it to take over any project of mine and figure out what the testing gaps are. 
I'd have it go in a loop until it concluded there were no gaps and I had a set of characterisation tests for the project.

## Red Green Refactor

- The red phase is the test automation generation using traditional code generation techniques from model driven development.
- The green phase is a simple prompt telling Claude to fix the failing test.
- The refactor phase is a skill that Claude uses to remove duplication.

### Red Cycle

The red cycle does 4 things
1. Invoke the **sheep-dog-dev-svc-maven-plugin** to convert asciidoctor files to the UML model for tagged tests
2. Invoke the plugin again to convert the UML model to Cucumber with Guice code
3. Create a test runner class if it's not already there
4. Run the tests to make check for a failure. The green cycle is skipped if there isn't one. This happens when Claude overdoes the previous tests. Also some tests negative tests.

I'll be converting the powershell script into a Maven goal. 
It'll create more of the boiler plate code in `src/test/java/org/farhan/common` but put it in `src-gen/test` so that the only thing left in `src/test/java` is mapping code that Claude can't use to cheat :).

### Green Cycle

The red cycle prepares the tests for the green cycle.
Just before this prompt is run, the red cycle has made sure that there's a failing test with similar passing tests if available.

```
Run `mvn verify -Dtest={runnerClassName} -Dmaven.test.failure.ignore=true` in {projectPath} and fix any failing tests:
  a. **IMPORTANT** Only modify code in src/main/java **NOT** in src/test/java
  b. Use `{projectPath}\site\uml\uml-interaction.md` or the `{projectPath}\site\uml\uml-class-*` files as guidance when creating new classes and methods. 
  c. Use `{projectPath}\target\site\jacoco-with-tests` to guide your analysis of what has previously been implemented for similar tests
  d. Use logging statements to help you debug
  e. Run mvn test to make sure all the project tests pass
```

### Refactoring Cycle

I don't specify too much here. I assume there's other tools out there that will probably take care of this and so I'd use one of them for this cycle. For now though, the refactor cycle does these tasks:
1. Eliminate duplication with utility files. There's one main utility class. Over the several runs, I've watched Claude figure out what it can refactor out in to that utility class. Each time I rebuild the code, I leave that utility class alone and the subsequent green cycles need less refactoring.
2. Eliminate duplication between class methods. Though the class body is deleted each time, Claude does a consistent enough job of breaking up methods into smaller ones, it's not always needed but just a personal preference of mine.
3. Apply code quality improvements to deduplicated code for nesting, logging, error messages, defensive coding, import optimization etc. Once I have a tool/service to do this, I'll move the previous 2 tasks into the green cycle.

---

# Factors Affecting the Quality and Speed

## Agents and Models

### Haiku vs Sonnet vs Opus

Sonnet and Sonnet works best. I tried Opus and Opus and the code isn't that much better but it takes noticably longer.
Haiku for the green cycle with Opus or Sonnet for refactoring is noticably faster but there's more refactoring done. 
For example it'll hardcode and duplicate a lot of stuff. Then in the refactor cycle that's all cleaned up. The duplication seems to come from it doing the least work to make the test pass. 

Let's say I have n scenarios in a feature file, most times Sonnet would understand what needs to be done and implement the first scenario so well that the other scenarios would just pass. Haiku though would need to make a change for each scenario and as a result go back and rework the previous scenarios, the code would get bigger and then the refactor cycle would clean it up. With Haiku, I need to do all three cycles because clean-up is needed for often. With Sonnet, I can do red and green cycles for multiple scenarios before needing a refactor cycle once per feature file.

These branches in **sheep-dog-local** map to the combinations
- Rebuild 10: Sonnet Sonnet
- Rebuild 9: Haiku Sonnet
- Rebuild 8: Haiku Opus

### Multi-Agent experiments

I've tried these two experiments
1. I tried having two agents concurrently work on a feature on separate branches but ran into two problems. First I hit my session limit faster and secondly, I realised I'd have merge conflicts to deal with for common classes like the utility one. I can avoid the latter but didn't want to experience the former too often.
2. Given that I tried rebuilding the project a few times, I asked Claude to compare the branches and copy the best implementations into the baseline that I used. What was interesting is that no branch followed all the rules perfectly. But when Claude considered all 4 (Rebuild 7 - 10) it copied all the best practices from each. This made me wonder if instead of refactoring after every red-green cycle, if I should pick the best patterns from multiple agents competing to make the best code?

## Graph and UML Models

### Graph Model Traversal

### UML Patterns

## DSL Vocabulary and Grammar

### Capturing the intention

I haven't had to write much of a description for my features or scenarios.
I invest my time in having a good automated test and already plan on upgrading my DSL to be more expressive.

That said, I feel having a good description at the top of the feature file to capture the intention makes up for gaps in my DSL.
1. feature intention
2. happy path
3. exception paths, typically don't need implementation

### Statistical Process Control

Some short test cases take longer than a long test case. I wonder if that's because the longer test case is more descriptive or is it something else?

Perhaps I have a hammer and am just looking for a nail but I think I need to make a chart to look for common cause and special cause variation here! It'll be a fun exercise to find the causes of what makes some tests take longer than the average, sometimes twice as long. I'd then feed that data back into the overall process so that it can try a sequence of smaller tests or automatically add an example to the uml-interaction.md file to help it jump to the conclusion if it consistently struggles with a test case. 
