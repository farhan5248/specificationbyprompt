# Testers Driving the Development

## The NeoNurture Revisited

New tools tend to amplify whoever uses them first. Without deliberate redesign, Claude just makes developers faster — the same way better surgical tools make surgeons faster, not patients more capable. 

In 2010, Design that Matters created the [NeoNurture][3] — a neonatal incubator built entirely from Toyota car parts. Sealed-beam headlights provided warmth. Dashboard fans circulated air. Door chimes sounded alarms. It ran on a motorcycle battery. The insight wasn't just clever engineering. It was a deliberate redesign of *who could maintain it*. In developing countries, medical-grade incubators existed but routinely broke down — because the people who could fix them weren't there. Car mechanics were. So the engineers changed the interface: you didn't need to be a trained medical technician. You just needed to know how to replace a broken headlight. The mechanics couldn't build incubators. But they could keep them running.

The legal profession did the same. Lawyers created a bounded role for people with domain knowledge and the right training to handle a meaningful portion of legal work — paralegals. The profession had to choose to create that role. It didn't happen automatically.

Testers are skilled at inspection (verifying after the fact whether what was built matches what was intended) and specification (defining behavior upfront through concrete examples). If those examples are precise and executable, there's nothing to inspect afterward: the code either passes the specification or it doesn't. The tester moves from inspector to author. They're not checking the product. They're defining it. I think we need less of the first skill and more of the second.

The NeoNurture engineers didn't give mechanics better tools or design a product that made them more effective at solving the problem. They redesigned the problem itself so that others could solve it. I want to be like the NeoNurture engineer — someone who makes writing code look like what testers already do, so that when they do it, instead of only specifying behavior they would actually be implementing it.

---

## Can Testers Drive the Development

In [Darmok][1], I described what is now a Maven plugin that automates the red-green-refactor cycle. The test automation is generated deterministically from the DSL. Claude generates the main code and now also updates the test code that connects it. But in that work, I was still in the loop — sequencing the test cases, setting up the architecture, defining the guardrails. Since then, I've removed myself from the loop entirely. That's the key difference this post is about: *can a tester step into that space, without me?*

The interface between tester and Claude is the [ubiquitous language][2] — the shared vocabulary that already exists between the business, testers, and developers. A test case isn't just a quality check; in this process it's the specification. That's what drives the coding.

The developer still defines the architecture and builds the guardrails — the test automation framework, the interface definitions, the validation rules. Just as a mechanic can't engineer an incubator from scratch, a tester can't design a system architecture. But within those guardrails, can a tester drive the coding? Can they write test cases, communicate with Claude entirely in that form, and have it translate their specifications into working code?

---

## The Experiment

The experiment had two goals: 

- First, simulate bug fixes and new feature development by testers. To keep all communication in the ubiquitous language, one rule applied: any change had to be expressed as a test case. If Claude sees a difference, it communicates it as a test. If it wants to change code, it first writes a test that captures why. This forced the contrast to be expressed in the same language a tester would use — and made it possible to feed that contrast back into the process as the next test case to implement. 
  -  To simulate new feature development, I deleted all the code, then studied where Claude struggled and tried to identify what I had to put back. The code that does this is `darmok-maven-plugin:gen-from-existing`. 
  -  For bug fixes, once I had a rebuilt code base, I wanted it to compare the rebuilt branch and the reference branch. It would first describe a difference in functionality and then come up with a test case to fix the issue. The code that does this is `darmok-maven-plugin:gen-from-comparison.` 

- Second, identify variation across runs. These were the 3 ways I tried to control the process
  - Reworking the ubiquitous language to be more expressive; How do you express the difference between null and an empty string as a test?
  - Changing what went into the markdown specifications and what didn't. Is it a refactoring problem — corrected after the fact by the markdown specifications?
  - Deterministic tooling to handle what the language couldn't express cleanly. Is it something the deterministic test automation framework should handle upstream?

What Claude did well:

- Implement the main code, given a clear architecture and tests that validate against it
- Connect test automation to implementation
- Help with bug fixes and new features when paired with a person writing test cases
- Improve with better examples — the more patterns and examples available, the less variation across runs

What Claude struggled with; the test automation code.

- **Assuming changes were pre-existing failures** It would break the code but somehow conclude that those problems were already there.

  ![](caas-preexisting.png)

- **Emptying out assertions** It would get tripped by its own duplicate code and think it needed to clean-up and then start "cleaning-up" the test code

  ![](caas-emptyasserts.png)

- **Implementing the main code in the test code** Sometimes it attempted to implement the functionality within the test code itself.

  ![](caas-mainintest.png)

- **Updating the specs that monitor it** It has some python scripts to make sure that the method signatures and interfaces aren't changed. At one point, instead of fixing the problems identified by the scripts, it decided to change the rules.

  ![](caas-siteuml.png)

Git commits from the last run
- [The complete run including rebuilding and identifying gaps tests](https://github.com/farhan5248/sheep-dog-local/commits/Rebuild22/)
- [Rebuilding from scratch](https://github.com/farhan5248/sheep-dog-local/commit/aaec4d4102582d57c14357c31fe32d4540a98a95)
- [At the end of the run, I had to prompt it to move code from src/test to src/main](https://github.com/farhan5248/sheep-dog-local/commit/beb76d40cc73d46a67fe7af13e2465db1cb8a743)
- [Identifying differences between branches](https://github.com/farhan5248/sheep-dog-local/commit/70c77d59eee3b026af89732308d307840f05cd02)

---

## Guardrails Are the Work

Here's what the experiment showed:
- The answer to the hypothesis is yes — a tester can drive coding within guardrails and building them is the work.
- When variation appeared across runs, the fix wasn't in one place, it was all three

Despite all of the issues, it still managed to create the code. These problems don't happen most of the time, but there's always a new problem that shows up and it compounds if not caught. The good news is that there's a way to both prevent and inspect for these issues. 

- **Preventing** 
  - Having a richer grammar and more test cases that specify both what to have and what not to. 
  - Interfaces not written by Claude. I don't see a tester or Claude having to make the decision of where a component boundary lies. I use interfaces as contracts it must fulfil. 
  - Boilerplate mocks and test automation code. A good example or set of examples goes a long way compared to specifying rules of what it can or can't do or how to do something.
  - Avoiding too many DI layers (Cucumber/Guice + jar dependencies)
  - Separate examples for implementing the test-to-main interface classes — Claude conflated these with main code examples
- **Inspecting** It's basically examining what's in git and then asking it to correct the problem. I'd keep an eye on the git commits, stop the process, and continue from the last session manually to see what corrective action would be needed. The correction was typically just pointing out that it touched a directory/file that it shouldn't have, the minute you point it out and ask it to try again it recovers. I haven't automated this yet but it's the next thing I'll work on.

---

## From Infrastructure as a Service to Coding as a Service

This pattern has happened before — in the relationship between developers and operations.

Before IaaS, operations teams owned infrastructure. Developers raised tickets, waited for provisioning, and coordinated with ops for every environment change. Ops was the gatekeeper. Then came Infrastructure as Code. Developers began defining their own infrastructure requirements in configuration files — versioned, automated, repeatable. But this didn't eliminate the ops team. It transformed them into platform engineers. Their job shifted from provisioning to building the templates: pre-approved patterns, paved roads, service catalogs that developers self-serve from. The templates are the guardrails. Developers can provision anything the templates allow. They can't go outside the patterns without involving the platform team.

Just as ops engineers didn't disappear — they became platform engineers — developers don't disappear either. They become the engineers who build and maintain the scaffolding that makes tester contribution possible. The developer's job doesn't disappear. It shifts: from writing the code to defining the architecture, building the guardrails, and maintaining the templates that others self-serve from.

I've concluded that my old QA team, given how they write test cases with a DSL, could contribute to new features and bug fixes by pairing with Claude with support from the developers. 
I imagine platforms where testers use the ubiquitous language to describe behavior as testable hypotheses. Claude implements from that. The tester is in the loop. The developer has designed the process and stepped back, now free to work on more challenging problems.

---

[1]: /specificationbyprompt/architecture-and-capabilities/code-generation
[2]: /specificationbyprompt/architecture-and-capabilities/ubiquitous-language
[3]: https://www.ted.com/talks/timothy_prestero_design_for_people_not_awards

