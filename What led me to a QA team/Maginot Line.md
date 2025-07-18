---
title: Maginot Line
---

I realised that the pipeline was built for a purpose that was no longer required just like the Maginot line. 
It was designed to make deploys into existing environments fast and mistake-proof given its size and complexity because those were the most time consuming activities in the past. 
I think what happened was that because it was so scary to do deployments, they were done infrequently and they became a bottleneck.
However once that fear was removed and the deployment bottleneck was removed along with it, the delay in feedback became the new bottleneck I guess.

What the deployment automation should have been designed for is to make it easier to stand-up individual components so that we could get feedback earlier. 
Then you could have just one or more components on your laptop or in the cloud or all of them in an environment. 
Docker makes it easier to solve this problem but I don't think you always need containers to solve the problem as I later proved with COBOL. 
Later on when working with my testers and COBOL developers, I could see that if you ran the QA tests in advance, you didn't need that many deploys, so even if you do it manually as they did you'd be OK. 

Eventually my manager asked me to work with the developer and take all the QA tests and automate them using SOAPUI.
We ran them in advance and deployed the code one last time but sadly we still had to wait because the QA team was doing everything manually. 
To them, automation was something that could only be done at the end after all tests had been run manually; I didn't understand why. 
What I did get at this point is that getting that feedback earlier through automated tests was a smaller price to pay than the cost of the re-work.