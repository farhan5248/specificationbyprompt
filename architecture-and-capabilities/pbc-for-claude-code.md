# PBC for Claude Code

Read Beyond the Goal by Goldratt
-New tech can be beneficial if it diminishes a constraint

Reflected on time at Telus Health
-Claude removes a constraint: communicating with the machines through coding
-Why, it's the universal translator
- Claude code shouldn't just be a tool for developers local optimisation or efficiency but made available to everyone in the system of work. The system here is everyone involved in creating and maintaining the software including the help desk agents. Testers shouldn't use it just to write tests to inspect the code but to modify the code base. I imagine a service desk agent contributing to a pull request by interacting with claude.

Did research on SPC and claude code
- I'd be comfortable with non-programmers modifying the code base indirectly if I had a deterministic way to monitor the process that made it possible. Specifically if the system could give the user (tester/BSA) feedback or a reverse prompt if their request wasn't clear.
- couple of weeks ago I was on John's podcast (which as a nerd I was very happy about). At the time I was eyeballing with PDSA to see if a PBC could be used. My hunch was that special cause variation would indicate when a human is needed in the loop.
- since then I've been trying it out myself, just using gh issues and tests and only looking at the code after a days changes. so far it's gone well, I was surprised by the similarity in each run. I have two charts where special cause variation does help me identify useful improvements. They've helped me identify when my tests can be interpreted in more than one way or if they conflict with existing functionality and can't be implemented.
- The detection of ambiguous test expectations was interesting. I see it as a sort of red bead game. For most tests, two separate claude sessions take a similar amount of time. So when they take more than 3 standard deviations of the average difference, that has so far indicated the two work-trees have functional differences. This functional difference can be expressed by claude as a test case that fails in one worktree and passes in the other. I could assume it's the worker (agent or model) but in my case it's the system (the testing harness). The quality of the tests the and order in which I provide them affects the variation. 

Conclusion
- I imagine a platform that makes it easier non-programmers to contribute to the code base earlier, for now I'll start with testers. Therefore instead of augmenting how developers alone work with claude code, I'm experimenting with trying to re-assign the work upstream and augment existing tools to work with claude code.
- I have more questions, I haven't focused much on the common cause variation.
- I've now moved onto integrating graphwalker into the flow and having Claude update the test model alone instead of creating tests. The graphwalker tests will then drive claude's coding. After that I want to try integrating a BPMN model to drive the graph model changes in the MBT tool; basically using claude to connect, developer, tester, business tools.




