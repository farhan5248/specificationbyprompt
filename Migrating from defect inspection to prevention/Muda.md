---
title: Muda
---

When I joined the QA team, I had introduced the term shift left. By the time I left the team, I grew to hate the mere mention of it :P. 
My dislike came from all the misinterpretations of it. 
At the time I traced it all the way back to Dr Deming and it's how I came across his work.
Specifically it was the quote about burning and scraping toast.

Given the circumstances of why I joined the QA team, I assumed testers would be defensive and claim their work couldn't be automated.
I had taken care to communicate in advance of speaking to them individually that it's not individual efficiency but team efficiency that we're trying to improve. 
I used a Netflix documentary on coaches to help with this, the coach said he coaches teams, not individual players.
I wanted to communicate to my QA team why we had to focus on optimizing globally and not locally and the importance of quality being built-in since it can't be inspected in. 
That is, I wanted them to understand how their test data injected earlier in the process makes the developers lives easier and that the cost of delayed feedback such as test automation isn't as effective and creates waste or Muda.

Initially, the global system was the QA team only and later I included a dev team.
I did this because motivating them to make their lives harder for another team's life to be easier might have been a harder sell.
To do so I first showed them how the very process of QA could be improved. 
The Poppendiecks mapped the lean waste of transport to hand-offs so I showed them how time and effort is wasted between the manual tester and test automator when making Selenium tests.
Then I demonstrated how that waste can be reduced and eventually prevented by working in small batches through Jidoka and Poka-Yoke using an IDE or Maven.

The custom Maven plug-in worked as a sort of automated test suite that would validate their work similar to how their test cases in turn would validate the developers work. 
The more frequently they ran the plug-in, the earlier they would catch their mistakes and save more time and therefore reduce wasted efforts.
When we started using preventative approaches through Xtext, they started to see the value of tests driving the development.
Instead of typing and then checking for mistakes, the IDA would give them feedback as they typed and that prevented the need to run a program to test for issues in the work.

The more waste was reduced, the more free time people had to try new things or help others.
This meant more kaizens and even more free-time for an individual or free-time for the team.
