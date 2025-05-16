# Muda

When I joined the QA team, I had introduced the term shift left. By the time I left the team, I grew to hate the mere mention of it :P. 
My dislike came from all the misinterpretations of it. At the time I traced it all the way back to Dr Deming and it's how I came across his work.

Assuming testers would be defensive and claim their work couldn't be automated, I had taken care to communicate in advance of speaking to them individually that it's not individual efficiency but team efficiency that we're trying to improve. I wanted to communicate to my QA team why we had to focus on optimizing globally and not locally and the importance of quality being built-in since it can't be inspected in. That is, I wanted them to understand how their test data injected earlier in the process makes the developers lives easier and that the cost of delayed feedback such as test automation isn't as effective and creates waste or Muda.

To do so I first showed them how the very process of QA could be improved. The Poppendiecks mapped the lean waste of transport to hand offs so I showed them how time and effort is wasted between the manual tester and test automator and how that waste can be reduced and eventually prevented by working in small batches through Jidoka (autonomation) and Poka-Yoke.

The custom Maven plug-in worked as a sort of automated test suite that would validate their work similar to how their test cases in turn would validate the developers work. When we started using preventative approaches through Xtext, they started to see the value of tests driving the development and how that prevented the need to run a program to test for issues in the work.