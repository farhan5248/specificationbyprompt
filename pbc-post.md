# LinkedIn Post

I recently finished listening to Goldratt's *Beyond the Goal*. I was hooked as soon as I heard his opening statement: a technology only delivers benefits if it diminishes a real limitation, and capturing those benefits means changing the rules you adopted to live with the limitation in the first place. 

That made me think of Claude Code. The power is natural-language-to-code translation in both directions, like the universal translator in Star Trek :). The limitation it diminishes is the rule that has organized software work: if you don't speak code, you don't change the code. Reflecting on my time at Telus Health, we had all these processes to capture information and narrow down the scope of work to the most important that was then assigned to developers. 

Goldratt's fourth question is the hard one: what new rules should we use now? If we treat Claude as a developer productivity tool, we keep all the old rules and capture maybe a fraction of the benefit. If we treat it as a system-of-work lever, the developer's job shifts toward a platform engineer, building guardrails and the people closest to the domain start contributing directly through the ubiquitous language they already use.

That said, I wouldn't be comfortable with letting a non-programmer modify a code base (even indirectly) unless I had a way to monitor it deterministically. For the past few weeks I've been running some Process Behavior Charts over Claude Code to see whether variation in the process could be used as a signal to ask the tester clarifying questions. A pattern is showing up that I'm watching and continuing to validate.

Pairs of independent runs against the same input mostly take similar amounts of time when the test is clear. When the run-to-run range for a given test is much wider than the rest, the cases I've inspected so far have looked like *ambiguous* tests — specifications that admit two valid implementations. Studying these runs made me think of the red bead game. When a pair of agents produces different implementations for a passing test, it points at a problem in my harness, not the agent.

If the chart proves it can reliably detect ambiguity in the specifications derived from those tests, the next investment is layering a BPMN model on top, where BPMN drives changes to the GraphWalker model. The longer arc, conditional on each step holding up, is to use Claude to connect the developer's tools, the tester's tools, and the business's tools into a single chain. As more time goes by I think the companies that get the most out of this new translator are the ones that enable more people to speak through it.

I have more research to do; but for now I've captured what I have here: [link to page]

---