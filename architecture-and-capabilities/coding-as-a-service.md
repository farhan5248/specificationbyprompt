# Coding As A Service

## LinkedIn Post

In 2010, engineers built a baby incubator from Toyota car parts so local mechanics could fix it — not because mechanics could build incubators, but because the engineers redesigned the problem so they could contribute.

The legal profession did the same thing with paralegals. Lawyers didn't disappear. They created a bounded role for people with domain knowledge to handle a meaningful portion of the work within defined limits. The profession had to choose to do that.

When DevOps arrived, ops teams didn't disappear either. They became platform engineers — building the templates and guardrails that developers self-serve from. Infrastructure as Code didn't replace ops. It changed what ops does.

I think the same shift is coming for developers and testers.

8 years ago, my QA team of about 30+ folks started writing test cases in a DSL. They described behavior as testable hypotheses — specific, falsifiable, pass or fail. I've spent the last year experimenting with the scaffolding that lets Claude implement code from such test cases, with guardrails it can't accidentally move. At this point I'm confident they could contribute to bug fixes or new feature development today. Not from scratch. Not alone. But meaningfully — the same way a paralegal contributes to legal work, or a mechanic keeps an incubator running.

The developer's job doesn't disappear. It shifts — to building the scaffolding that makes this possible. Someone just has to choose to build it.

---

## Section 1: The NeoNurture Revisited

In 2010, Design that Matters created the [NeoNurture][neo] — a neonatal incubator built entirely from Toyota car parts. Sealed-beam headlights provided warmth. Dashboard fans circulated air. Door chimes sounded alarms. It ran on a motorcycle battery. The insight wasn't just clever engineering. It was a deliberate redesign of *who could maintain it*. In developing countries, medical-grade incubators existed but routinely broke down — because the people who could fix them weren't there. Car mechanics were. So the engineers changed the interface: you didn't need to be a trained medical technician. You just needed to know how to replace a broken headlight. The mechanics couldn't build incubators. But they could keep them running.

There's a common assumption that new tools simply empower whoever already holds expertise. Think of the samurai and the gun. The story goes that samurai resisted firearms to protect their way of life. But that's not quite right. When the Portuguese introduced the matchlock to Japan in 1543, samurai adopted it rapidly — Oda Nobunaga was fielding hundreds within a decade. The gun didn't meet resistance from warriors. It met resistance from *rulers*. The Tokugawa shogunate suppressed firearms after 1600 to maintain the class hierarchy that depended on them. The gatekeeping wasn't about skill. It was about control.

Something similar happens in software. Developers who say "you need me — I'm just more productive with Claude" aren't necessarily protecting their position intentionally. But the effect is the same: testers and product owners stay exactly where they were. AI tools used naively just make developers more productive, without changing who can contribute.

Testers have two skills. The first is inspection — verifying after the fact whether what was built matches what was intended. The second is specification — defining behavior upfront through concrete examples. If those examples are precise and executable, there's nothing to inspect afterward: the code either passes the specification or it doesn't. The tester moves from inspector to author. They're not checking the product. They're defining it. We need less of the first skill and more of the second.

I want to be like the NeoNurture engineer — someone who makes writing code look like what testers already do, so that when they do it, they think they're specifying behavior but they're actually implementing it.

---

## Section 2: Coding as Testing

In a [previous post][1], I described two milestones. The first was **no-test-dev**: testers generating their own test automation without a test automation developer, using a DSL that a computer program could translate directly into code. The second was **no-dev**: Claude generating the main code without a developer, driven by those same test cases one at a time through a red-green-refactor loop.

That work established that Claude can implement code when given the right tests and constraints. But I was still in the loop — setting up the architecture, defining the test automation framework, making the guardrails. The question this post asks is the next step: *can a tester take over the coding within those guardrails, without me?*

Testers have a language. Not just any language — the [ubiquitous language][2] from Domain Driven Design: the shared vocabulary that already exists between the business, testers, and developers. They write test cases in it. They describe behavior through examples. They frame outcomes as pass or fail. A test case doesn't have to be just a quality check — it can be a complete specification of what software should do. Using that laanguage reuses skills they already have. That's the interface I want to use between them and Claude.

Not replacing developers. The developer still defines the architecture and builds the guardrails — the test automation framework, the interface definitions, the validation rules. Just as a mechanic can't engineer an incubator from scratch, a tester can't design a system architecture. But within those guardrails, can a tester drive the coding? Can they write test cases, communicate with Claude entirely in that form, and have it translate their specifications into working code?

That's the question the experiment set out to answer.

[neo]: /demingdriventesting/communicating-the-strategy-to-qa/the-neo-nurture-incubator
[1]: /specificationbyprompt/from-shift-left-to-no-dev
[2]: /specificationbyprompt/architecture-and-capabilities/ubiquitous-language

---

## Section 3: What I've Learnt

The experiment had two goals. First, simulate new feature development: delete the implementation and have Claude rebuild it from test cases, the same way a tester would drive a new feature by writing tests and having Claude implement from them. Second, identify variation across runs: compare the resulting branches against each other to find where Claude produced different implementations for the same specification — and figure out what would address it.

When the DSL is too generic, different runs produce different code. How do you express the difference between null and an empty string as a test? Is that a language problem — does the ubiquitous language need a richer vocabulary? Is it a tooling problem — something the deterministic test automation framework should handle upstream? Or is it a refactoring problem — corrected after the fact by the markdown specification guardrails?

To keep all communication in the ubiquitous language, one rule applied: any difference found between branches had to be expressed as a test case — not explained in English, not in Java. If Claude sees a difference, it communicates it as a test. If it wants to change code, it first writes a test that captures why. This forced the contrast to be expressed in the same language a tester would use — and made it possible to feed that contrast back into the process as the next test case to implement.

What did it struggle with?

- The boundary between test code and main code
- The boundary between mocks and real implementations
- Dependency injection
- Non-determinism — when left to generate test automation freely, it made choices that were hard to reason about and harder to trust

<!-- TODO: elaborate with notes from Slack -->

---

## Section 4: Guardrails Are the Work

Here's what the experiment showed.

The answer to the hypothesis is yes — a tester can drive coding within guardrails. But only if the guardrails exist. And building them is the work.

When variation appeared across runs, the fix wasn't in one place. It was all three: reworking the ubiquitous language to be more precise, changing what went into the markdown specifications and what didn't, and deterministic tooling to handle what the language couldn't express cleanly. There's no single lever — the scaffolding has to work together.

What Claude can do:

- Implement the main code, given a clear architecture and tests that validate against it
- Connect test automation to implementation
- Help with bug fixes and new features when paired with a tester writing test cases

What must not be left to Claude:

- **Test automation** — must be created deterministically. Claude can't be trusted to write its own test cases without introducing non-determinism.
- **Mocks** — must be defined deterministically. If Claude writes its own mocks, it can make them pass in ways that don't reflect real behavior.
- **Interfaces** — generated from UML models, not invented by Claude on the fly.
- **The guardrails themselves** — Claude must not be able to move its own constraints. It cannot modify test expectations, change what is asserted, or create new getters to work around validation scripts.

SPC — Statistical Process Control — is part of the guardrail too. By treating Claude's outputs as a process, you can identify special cause variation — hallucination, drift from spec — and correct it systematically rather than reviewing every output manually.

The developer's job doesn't disappear. It shifts. Instead of writing the code, the developer defines the architecture, builds the scaffolding, and maintains the guardrails that make tester contribution possible. Human beings still have to define the behavior and the structure. Claude connects the two.

My old QA team, given how they write test cases with a DSL, could contribute to new features and bug fixes by pairing with Claude and doing BDD. Not from scratch. Not alone. But meaningfully.

<!-- TODO: elaborate on language/markdown/tooling findings with Slack notes -->

This isn't unprecedented. The legal profession did it with paralegals. Lawyers hold the expertise — they define strategy, structure arguments, make judgement calls. But they created a bounded role for people with domain knowledge and the right training to handle a meaningful portion of the work within those boundaries. The profession had to choose to create that role. It didn't happen automatically.

That leaves one question — not a technical one. The samurai didn't give up the sword when guns arrived. They adopted guns as tools while keeping the sword as their identity. Developers can do the same: embrace Claude as a productivity tool while keeping exclusive access to code. The door stays closed.

Or they can choose to be the NeoNurture engineer. Open the door. Redesign the work so that others can contribute. The testers are already standing there with the right language. Someone just has to let them in.

---

## Section 5: From Platform Engineering to Coding as a Service

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

Just as ops engineers didn't disappear — they became platform engineers — developers don't disappear either. They become the engineers who build and maintain the scaffolding that makes tester contribution possible.

I imagine platforms where testers use the ubiquitous language to describe behavior as testable hypotheses — not vague requirements like "the system should continue to behave as expected," but specific, falsifiable statements. True or false. Pass or fail. Claude implements from that. That's coding as a service. That's coding as testing. The tester is in the loop. The developer has designed the process and stepped back, now free to work on more challenging problems.

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

### Samurai, Guns, and the Sword
- Guns (matchlock/tanegashima) introduced to Japan by Portuguese in 1543
- Samurai did NOT resist firearms — adopted them rapidly as practical tools of war
- Oda Nobunaga fielding hundreds of matchlocks by the late 1540s
- Key advantage of guns: a peasant conscript could learn to operate one in weeks vs. years of samurai training
- **The sword was identity, not just a weapon.** The katana was said to embody the samurai's spirit — a symbol of honor and status. Samurai were expected to always carry two swords.
- Samurai never chose the gun *over* the sword — they held both. Gun = practical tool. Sword = identity.
- During the Tokugawa peace (1600–1868), samurai doubled down on sword culture — reinventing their identity around it via art and ceremony even without wars to fight
- **The sword was taken, not surrendered.** In 1868, the Meiji government *forbade* samurai from carrying swords and removed their privileged status. It was not a choice.
- **Implication for the post**: developers can embrace Claude (the gun) as a productivity tool while still keeping exclusive access to code (the sword) as their identity. Someone has to *choose* to redesign the door — be the NeoNurture engineer, not the Tokugawa shogunate.
- Sources: [Samurai - Wikipedia](https://en.wikipedia.org/wiki/Samurai), [Firearms of Japan - Wikipedia](https://en.wikipedia.org/wiki/Firearms_of_Japan), [Samurai and Firearms - Artelino](https://www.artelino.com/articles/samurai-firearms.asp), [Bushido: Way of the Samurai - NGV](https://www.ngv.vic.gov.au/essay/bushido-way-of-the-samurai/)

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
