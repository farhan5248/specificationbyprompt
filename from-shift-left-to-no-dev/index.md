---
title: From Shift Left to No Dev
---

Gene Kim was on Dave Farley's podcast, and he said that [if you want to move really fast, you need a feedback and a control system to make sure you don't go off the rails][1]. AI coding agents move fast. The faster they go, the more variation in their output and the greater the need for controls. This is why I think Deming still matters and especially more so with AI — quality has to be built into the process, not inspected in afterward.

On the QA team, we used SPC to study test execution rates and discovered a signal: testers would go from executing plenty of tests per day to zero — stuck redoing their test cases. That special cause variation pointed us to the metric that mattered most: **hypothesis confirmation time**, how long it takes a tester to confirm whether their test case is correct by running it against the code. The solution we built to reduce that time — from months to hours — is the same one I now use with Claude to reduce it further to minutes.

# Before AI

The journey to reduce hypothesis confirmation time was evolutionary — each improvement revealed the next bottleneck.

First, we automated the creation of test automation from the test cases ([jidoka][2]), eliminating the time spent on manual test execution. Then we mistake-proofed how test cases were written ([poka yoke][3]) to reduce rework. This led to the development of a DSL editor and Maven plugins — a platform where tests were written so clearly that a computer program could generate the test automation code. The test automation developers had effectively automated themselves out of the job of coding. Instead of a tester asking a test automation developer "can you automate this for me?", that role was replaced by a set of services. If a tester couldn't create their test automation within 15-20 minutes, they'd escalate and a test automation developer would fix the platform — not write the automation for them.

With the platform in place, we could [run the QA tests earlier][4] — verification tests during development, with validation tests still run later. One developer worked side by side with a tester in her dev environment. Every couple of hours, the tester would prepare the next small batch of tests and the developer would prepare the next chunk of code. They'd run it together in seconds and make a plan for the next batch. The developer delivered her code to QA later than the other developers, but she had virtually no bug fixes and test execution was completed earlier. The hypothesis confirmation time went from months — waiting until QA to discover if the test cases and code were correct — to hours.

Why focus on this metric? Because the [variation we studied][5] showed that the biggest disruption was when testers went from executing plenty of tests per day to zero — stuck redoing their test cases. The platform didn't just speed up execution; it shortened the feedback loop so that hypotheses were confirmed before errors could compound.

# After AI

Before we had AI, we already had proof that a developer with the right tests and a debugger didn't need much else.

On the QA team, a complex new feature was given to a junior developer with about a year of COBOL experience because the team was under-resourced. Everyone expected disaster: broken builds, defects in everyone else's work, senior developers pulled away to review and fix things. None of it happened. He [let the tests drive his code changes][6] — running them first to identify which parts of the code were affected, then only changing enough to make each test pass. Senior developers reviewed and refactored his work afterward but didn't have to write the code for him. He needed a debugger and the tests. That was it.

This inspired me to have Claude Code go through the same process. The "Before AI" section described **no-test-dev** — testers generating their own test automation without a test automation developer. This section is about **no-dev** — Claude generating the main code without a developer.

[Darmok][7] puts the junior developer's process into a loop. It iterates through a sequence of test cases going through the red-green-refactor cycle for each one:
- **Red**: Test automation is generated from the DSL using traditional code generation — no AI needed
- **Green**: Claude is given the failing test along with similar passing tests and told to fix it, only modifying main code
- **Refactor**: Claude removes duplication, guided by the patterns in the UML files

Just like the junior developer, Claude works best when given **one failing test at a time** with similar successful tests as reference. If given too many tests at once, it bounces between failures without fixing anything — the same way any junior developer would struggle if handed an entire project at once instead of debugging one at a time.

The order and size of the test cases matter too. Tests sequenced so that each is slightly different from the previous one result in smaller code changes, less variation, and faster cycles. When I've experimented with random ordering or large jumps between test cases, it can take almost 15 minutes per test compared to the average 4 minutes. I suspect there's [common cause and special cause variation][8] to study here — some short tests take longer than long ones, and understanding why could feed back into improving the test sequence or the examples Claude uses.

# Why Deming Still Matters

Why invest in your QA team? They're the key supporting characters in this story. Most of the work they do around inspection is non-value added and easier to automate. Automating that work creates space for them to learn and pay it forward. Even if your devs can write the tests themselves like I can, you probably don't want your technical resources doing non-technical work. I'd rather a tester work with the business analysts to write the specifications while I focus on reducing the variation — basically being an industrial engineer like Ohno. The faster the system moves, the more you need that [feedback and control system][1] Gene Kim described. Deming's approach gives you one.

[1]: https://open.spotify.com/episode/2oJPAPHKGJ9LlfMiBvBobq
[2]: /demingdriventesting/migrating-from-defect-inspection-to-prevention/jidoka
[3]: /demingdriventesting/migrating-from-defect-inspection-to-prevention/poka-yoke
[4]: /demingdriventesting/why-run-the-qa-tests-earlier
[5]: /demingdriventesting/migrating-from-defect-inspection-to-prevention/mura
[6]: /demingdriventesting/why-run-the-qa-tests-earlier/does-it-matter-if-the-tests-actually-drive-the-development
[7]: /specificationbyprompt/architecture-and-capabilities/code-generation
[8]: /specificationbyprompt/architecture-and-capabilities/code-generation#statistical-process-control