---
title: Goldratt - Diminishing A Limitation
---

* TOC
{:toc}

---

# Beyond the Goal

In *Beyond the Goal* Goldratt's central argument is that a new technology delivers benefits only to the extent that it diminishes a real limitation, and that capturing those benefits requires four questions to be answered honestly:

1. What is the power of the technology?
2. What limitation does it diminish?
3. What rules, behaviors, and policies did we adopt to accommodate that limitation, back when we couldn't diminish it?
4. What new rules should we use now?

Goldratt's favourite cautionary tale is ERP. Companies spent years and small fortunes installing it, then kept all of their old batch-and-queue, monthly-cycle, departmental-silo policies in place. The technology that could have enabled smaller batches and faster response simply automated the old behavior. The fourth question is the one that matters, and it's the one most organizations skip.

I decided to apply those four questions to Claude Code.

# The Limitation It Diminishes

**The power of the technology** is that it translates between natural language and code in both directions. It is, in the most literal sense, a universal translator between the people who understand the business and the machines that run it.

**The limitation it diminishes** is the natural-language-to-code translation bottleneck that has historically forced every contribution to a codebase to route through a developer. For decades, the rule has been: if you don't speak code, you can't change the code. Other roles in the system of work have been organized around accommodating that constraint.

**The rules we adopted to accommodate the limitation** are everywhere once you start looking for them. Only developers touch the codebase. Testers inspect after the fact instead of specifying upfront. Service desk agents file tickets, they don't contribute fixes. Business analysts write requirements documents that get re-translated into code by someone else. Code review is a developer-to-developer ritual. Even "shift left" somehow has come to mean shifting work earlier *within the development team*, not outside of it.

**The new rules** are that testers, BSAs, and even service desk agents should be able to contribute pull requests directly, expressing their changes in the ubiquitous language they already use. Developers shift from writing the code to building and maintaining the guardrails — the architecture, the interfaces, the test automation framework — that make safe self-service possible for everyone else. Claude is not a productivity tool bolted onto the developer role; it is a lever that re-distributes *who gets to participate* by re-assigning the work upstream. ([Deming - Building Quality In][1] is the other half of this — *how* the code gets built once it's been specified; this page is about *who* specifies it and how far upstream that can move.)

# Non-Developers Driving the Development

I've written about the new-rules half of this argument in detail in [Testers Driving the Development][4], so I won't rebuild the whole case here. The short version:

The 2010 NeoNurture incubator was built out of Toyota car parts so that car mechanics, not medical technicians, could keep it running in places where medical technicians weren't available. The engineers didn't give the mechanics better medical training; they redesigned the *interface to the problem* so that the people who were already there could solve it.

Testers are skilled at two things: inspection (verifying after the fact that what was built matches what was intended) and specification (defining behavior upfront through concrete examples). When the examples are precise and executable, there's nothing left to inspect afterward — the code either passes the specification or it doesn't. The tester moves from inspector to author. We need less of the first skill and more of the second, and Claude Code is what makes that move economically viable.

The IaaS-to-platform-engineering arc is the precedent. Before Infrastructure as Code, operations teams owned infrastructure and developers raised tickets. After IaC, operations didn't disappear — they became platform engineers, building the templates and paved roads that developers self-serve from. I think developers are about to make the same move with respect to testers and other domain contributors, and the contribution doesn't stop at testers — once the interface to the problem is the specification, the BSA and product owner can drive changes from further upstream still.

# Preventing Unintended Interpretations

The lever only works if it is safe to pull. I would not tell a QA team to start contributing to a codebase through Claude Code on faith — if they don't read code, they have no way to know whether what was implemented is what they intended. So the harness has to give a non-programmer *useful feedback*: a reverse prompt that says when a request was too vague, too big, or contradictory for Claude to implement without guessing. And it earns that trust only by **not crying wolf** — a harness that flags ordinary run-to-run noise as a problem is as useless as one that flags nothing. Useful feedback without the false alarms is the whole requirement.

Building that control system — a process behavior chart on Claude's runs that separates a genuine bad-input signal from the noise of a stochastic worker — is the subject of [Wheeler - Understanding Variation][2]. It is the precondition for everything above: without it, "let the tester drive" is a hope; with it, it's a process.

# Conclusion

The platform I imagine makes it easier for non-programmers to contribute to the codebase earlier in the process. I'd start with testers because they already write specifications in a DSL, but the argument generalizes. Instead of augmenting how developers alone work with Claude Code, I'm experimenting with re-assigning the work upstream and augmenting the existing tools that the upstream roles already use.

I'm currently integrating graph models into the flow as part of this research, so Claude updates the test model and GraphWalker can generate tests from it instead of Claude writing tests directly. If the chart proves it can reliably detect ambiguity in the specifications derived from those tests, the next investment is layering a BPMN model on top so the BPMN drives changes to the graph model and the graph model drives Claude. The longer arc, conditional on each step holding up, is to use Claude to connect the developer's tools, the tester's tools, and the business's tools into a single chain — which is, I think, what answering Goldratt's fourth question actually looks like in practice.

---

[1]: deming-building-quality-in
[2]: wheeler-understanding-variation
[4]: /specificationbyprompt/architecture-and-capabilities/testers-driving-the-development
