# Coding As A Service

## Summary

In 2010, engineers built a baby incubator from Toyota car parts so local mechanics could fix it — not because mechanics could build incubators, but because the engineers redesigned the problem so they could contribute. The legal profession did the same thing with paralegals. Lawyers didn't disappear. They created a bounded role for people with domain knowledge to handle a meaningful portion of the work within defined limits. The profession had to choose to do that.

I wanted to test whether I could remove myself from the loop entirely — and whether a tester could step into that space. Almost 8 years ago, my QA team of 30+ folks started writing test cases in a DSL, describing behavior as testable hypotheses. I've spent the last two weeks building and testing the guardrails that let Claude implement code from that style of test cases. The result: I'm confident my old team could contribute to bug fixes and new feature development today — meaningfully, the same way a paralegal contributes to legal work or a mechanic keeps an incubator running. And my role? It shifts — from writing the code to building the scaffolding that makes this possible.

While I'm confident in a tester's ability to work this way and Claude's ability to make the production code, I still don't trust it to make the test automation on its own. I'll continue to primarily depend on MBT/MDD for that, using Claude to fill in the gaps. I've written more about it here.

---

## The NeoNurture Revisited

New tools tend to amplify whoever uses them first. Without deliberate redesign, Claude just makes developers faster — the same way better surgical tools make surgeons faster, not patients more capable. 

In 2010, Design that Matters created the [NeoNurture][neo] — a neonatal incubator built entirely from Toyota car parts. Sealed-beam headlights provided warmth. Dashboard fans circulated air. Door chimes sounded alarms. It ran on a motorcycle battery. The insight wasn't just clever engineering. It was a deliberate redesign of *who could maintain it*. In developing countries, medical-grade incubators existed but routinely broke down — because the people who could fix them weren't there. Car mechanics were. So the engineers changed the interface: you didn't need to be a trained medical technician. You just needed to know how to replace a broken headlight. The mechanics couldn't build incubators. But they could keep them running.

The legal profession did the same. Lawyers created a bounded role for people with domain knowledge and the right training to handle a meaningful portion of legal work — paralegals. The profession had to choose to create that role. It didn't happen automatically.

Testers are skilled at inspection (verifying after the fact whether what was built matches what was intended) and specification (defining behavior upfront through concrete examples). If those examples are precise and executable, there's nothing to inspect afterward: the code either passes the specification or it doesn't. The tester moves from inspector to author. They're not checking the product. They're defining it. We need less of the first skill and more of the second.

The NeoNurture engineers didn't give mechanics better tools or design a product that made them more effective at solving the problem. They redesigned the problem itself so that others could solve it. I want to be like the NeoNurture engineer — someone who makes writing code look like what testers already do, so that when they do it, instead of only specifying behavior they would actually be implementing it.

---

## Can Testers Drive the Development

In [Darmok][1], I described what is now a Maven plugin that automates the red-green-refactor cycle. The test automation is generated deterministically from the DSL. Claude generates the main code and now also updates the test code that connects it. But in that work, I was still in the loop — sequencing the test cases, setting up the architecture, defining the guardrails. Since then, I've removed myself from the loop entirely. That's the key difference this post is about: *can a tester step into that space, without me?*

The interface between tester and Claude is the [ubiquitous language][2] — the shared vocabulary that already exists between the business, testers, and developers. A test case isn't just a quality check; in this process it's the specification. That's what drives the coding.

The developer still defines the architecture and builds the guardrails — the test automation framework, the interface definitions, the validation rules. Just as a mechanic can't engineer an incubator from scratch, a tester can't design a system architecture. But within those guardrails, can a tester drive the coding? Can they write test cases, communicate with Claude entirely in that form, and have it translate their specifications into working code?

That's the question the experiment set out to answer.

---

## The Experiment

The experiment had two goals. First, simulate new feature development: delete the implementation and have Claude rebuild it from test cases, the same way a tester would drive a new feature by writing tests and having Claude implement from them. Second, identify variation across runs: compare the resulting branches against each other to find where Claude produced different implementations for the same specification — and figure out what would address it. 

When the DSL is too generic, different runs produce different code. How do you express the difference between null and an empty string as a test? Is that a language problem — does the ubiquitous language need a richer vocabulary? Is it a tooling problem — something the deterministic test automation framework should handle upstream? Or is it a refactoring problem — corrected after the fact by the markdown specification guardrails?

To keep all communication in the ubiquitous language, one rule applied: any difference found between branches had to be expressed as a test case — not explained in English, not in Java. If Claude sees a difference, it communicates it as a test. If it wants to change code, it first writes a test that captures why. This forced the contrast to be expressed in the same language a tester would use — and made it possible to feed that contrast back into the process as the next test case to implement.

What Claude can do:

- Implement the main code, given a clear architecture and tests that validate against it
- Connect test automation to implementation
- Help with bug fixes and new features when paired with a tester writing test cases
- Improve with better examples — the more patterns and examples available, the less variation across runs

What did it struggle with? In general, the test automation code.

**Assuming changes were pre-existing failures** It would break the code but somehow conclude that those problems were already there.

![](caas-preexisting.png)

**Emptying out assertions** It would get tripped by its own duplicate code and think it needed to clean-up and then start "cleaning-up" the test code

![](caas-emptyasserts.png)

**Implementing the main code in the test code** Sometimes it attempted to implement the functionality within the test code itself.

![](caas-mainintest.png)

**Updating the specs that monitor it** It has some python scripts to make sure that the method signatures and interfaces aren't changed. At one point, instead of fixing the problems identified by the scripts, it decided to change the rules.

![](caas-siteuml.png)

It also took a long time (10+ minutes as opposed to <5 when I created the test automation for it) when working with dependency injection or the boundary between a mock and the main code. It actually attempted to implement the mock in the main code.

---

## Guardrails Are the Work

Here's what the experiment showed.

The answer to the hypothesis is yes — a tester can drive coding within guardrails. But only if the guardrails exist. And building them is the work.

When variation appeared across runs, the fix wasn't in one place. It was all three:

- Reworking the ubiquitous language to be more expressive
- Changing what went into the markdown specifications and what didn't
- Deterministic tooling to handle what the language couldn't express cleanly

There's no single lever — the scaffolding has to work together.

Despite all of the issues, it still managed to create the code. These problems don't happen most of the time, but there's always a new problem that shows up and it compounds if not caught. The good news is that there's a way to both prevent and check for these issues. 

- **Preventing** Using templates or more examples. A good example or set of examples goes a long way compared to specifying rules of what it can or can't do or how to do something. Having a richer grammar and more test cases that specify both what to have and what not to.
- **Inspecting** It's basically examining what's in git and then asking it to correct the problem. I'd keep an eye on the git commits, stop the process, and continue from the last session manually to see what corrective action would be needed. The correction was typically just pointing out that it touched a directory/file that it shouldn't have, the minute you point it out and ask it to try again it recovers. I haven't automated this yet but it's the next thing I'll work on.

SPC — Statistical Process Control — is part of the guardrail too. By treating Claude's outputs as a process, you can identify special cause variation — hallucination, drift from spec — and correct it systematically rather than reviewing every output manually.

---

## From Infrastructure as a Service to Coding as a Service

This pattern has happened before — in the relationship between developers and operations.

Before IaaS, operations teams owned infrastructure. Developers raised tickets, waited for provisioning, and coordinated with ops for every environment change. Ops was the gatekeeper. Then cloud platforms arrived and infrastructure became a service — developers could provision what they needed on demand, without owning hardware or waiting for ops. The relationship changed.

Then came Infrastructure as Code. Developers began defining their own infrastructure requirements in configuration files — versioned, automated, repeatable. But this didn't eliminate the ops team. It transformed them into **platform engineers**. Their job shifted from provisioning to building the templates: pre-approved patterns, paved roads, service catalogs that developers self-serve from. The templates are the guardrails. Developers can provision anything the templates allow. They can't go outside the patterns without involving the platform team.

The parallel to this post is exact:

| Ops → Platform Engineering | Developer → Scaffolding Engineer |
|---|---|
| Ops team owned infrastructure | Developer owns the code |
| IaaS: infrastructure on demand | Coding as a Service: code generation on demand |
| IaC: developers define infrastructure as code | Coding as Testing: testers define behavior as test cases |
| Templates are the guardrails | Test automation, UML interfaces, markdown specs are the guardrails |
| Ops job shifts to maintaining the platform | Developer job shifts to maintaining the guardrails |

Just as ops engineers didn't disappear — they became platform engineers — developers don't disappear either. They become the engineers who build and maintain the scaffolding that makes tester contribution possible. The developer's job doesn't disappear. It shifts: from writing the code to defining the architecture, building the guardrails, and maintaining the templates that others self-serve from.

My old QA team, given how they write test cases with a DSL, could contribute to new features and bug fixes by pairing with Claude and doing BDD. Not from scratch. Not alone. But meaningfully.

I imagine platforms where testers use the ubiquitous language to describe behavior as testable hypotheses. Claude implements from that. The tester is in the loop. The developer has designed the process and stepped back, now free to work on more challenging problems.

---

## Research Notes

### NeoNurture Baby Incubator
- Created by Design that Matters, 2010
- Built entirely from Toyota car parts: sealed-beam headlights for warmth, dashboard fans for air circulation, door chimes for alarms, powered by motorcycle battery or cigarette lighter adapter
- Key insight: you didn't need to be a trained medical technician to fix it — you just needed to know how to replace a broken headlight
- Problem it solved: 3.9 million infants die within a month of birth in the developing world annually; high-tech incubators existed but broke down and couldn't be repaired due to lack of parts/expertise
- Named #1 of "50 Best Inventions of 2010" by Time magazine
- Note: remained a prototype — never manufactured at scale; hospitals in developing countries didn't adopt it
- Sources: [Design that Matters](https://www.designthatmatters.org/past-projects), [RISD: Neonatal Incubator Built from Car Parts](https://www.risd.edu/news/stories/neonatal-incubator-built-car-parts), [TED Speaker Timothy Prestero](https://www.ted.com/speakers/timothy_prestero)

### IaaS and IaC — Ops to Platform Engineering
- Before IaaS: ops teams owned infrastructure, developers raised tickets and waited
- IaaS (AWS, Azure, etc.): infrastructure on demand — developers provision without owning hardware
- IaC (Terraform, CloudFormation, Ansible): infrastructure defined as code — versioned, automated, repeatable
- Key shift: ops didn't disappear — became **platform engineers**, job shifted to building templates/paved roads
- AWS Service Catalog: pre-approved templates developers self-serve from — templates ARE the guardrails
- Developers can provision anything templates allow; can't go outside patterns without platform team
- Parallel: developer creates test automation framework/UML interfaces/markdown specs → tester self-serves from them
- Sources: [IaaS - Atlassian](https://www.atlassian.com/microservices/cloud-computing/infrastructure-as-a-service), [IaC - HashiCorp](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code), [AWS Service Catalog](https://oneuptime.com/blog/post/2026-02-12-aws-service-catalog-governed-self-service/view), [Siloed Infra to IaaS - Team Topologies](https://teamtopologies.com/news-blogs-newsletters/siloed-infrastructure-to-iaas-a-team-topologies-driven-shift)

### Paralegals — Democratizing Legal Work
- Lawyers hold the expertise: strategy, judgment, structure
- Paralegal role created so people with domain knowledge and narrower training could handle a meaningful portion of legal work within defined boundaries
- The profession had to *choose* to create that role — it didn't happen automatically
- Closest analogy to the developer/tester model: a skilled professional creates a bounded role for someone else to contribute without needing the full expertise
- Sources: [What Is A Paralegal - Nursing School Hub](https://www.nursingschoolhub.com/what-is-a-nurse-paralegal/), [Guild - Wikipedia](https://en.wikipedia.org/wiki/Guild)

---

[neo]: https://www.designthatmatters.org/past-projects
[1]: /specificationbyprompt/architecture-and-capabilities/code-generation
[2]: /specificationbyprompt/architecture-and-capabilities/ubiquitous-language

