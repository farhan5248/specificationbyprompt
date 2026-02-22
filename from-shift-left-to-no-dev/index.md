---
title: From Shift Left to No Dev
---

Proposition

I'm a manual tester advocate, instead of making them better at inspection, they should be used to do prevention.
Dr Deming quality built in with AI coding driven by acceptance tests

Conclusion

 why you should start with your manual testers to create that space and capacity for others to improve
 even if your devs can write the tests themselves like I can, you probably don't want your technical resources doing non-technical work.

Before AI - How did we get to the DSL editor and no-test-dev with maven

 The goal was to try and reduce variation in the process of inspection. This would involve identifying it, then the overburdened individual and then the non-value wasteful activities that we'd automate away. The most useful or eye opening metric was the time to confirm a tester's hypothesis. review variation/mura page, explain the xbarR chart for scientist time to confirm hypothesis

 Explain what variation was studied to point me in this direction, specifically the time it took to confirm a hypothesis if you treat your testers are scientests. The DSL editor was a tool used to reduce toil/overburden which was reduced by eliminating waste. Part of reducing overburden meant writing tests that are so clear that eventually a computer program could take over the test automation coding. With defects being systematically prevented through earlier verification testing, there was less of a need for inspection and we focused on validation testing. Spreadsheet driven testing was at least twice as fast as what I was doing with Cucumber or Robot Framework. But the speed of mass inspection at the end as a regression wasn't the goal. I was trying to reduce the dependence on inspection in the first place. 


 update roser podcast review about heijunka. Instead of 4 door and 2 door car, it was drug app vs mb app work. First DA tried automating DA tests but there was no point. Then they spent time trying to automate MB tests but couldn't understand them and slowed down the MB SME. Then they helped execute MB tests in pairs and the MB testers wrote tests with less assumed knowledge.

 Drug-engine shift left, this is about maven creating test automation automatically. This is about poka-yoke, jidoka.

 end with how was qa preventing defects, the story of the kid. basic debugging skills armed with tests provided in the right sequence. Also point out the no-test-dev, that the test automation coding was a service of sorts even though it wasn't really deployed to a server.

After AI -explain why shift left after AI is no dev and how the manual testers and dsl editor gets you to no dev

 Describe how the no-test-dev is used in red to support green cycle in RGR and this ensures quality built-in.

No Test dev in the red cycle
With my QA team, they base the test automation developers made a platform, and it was almost like as if it was no dev, but no Test Dev, right.
And it was test coding as a service, whether it was Eclipse and a jar file for an API. The idea and today could be vs code and a bunch of services in the cloud.

The goal was that the test automation developers automated themselves out of the job of coding. Instead of the tester talking to the test automation developer and saying, hey, can you automate this for me? That job disappeared. And what was there instead was a set of services.

Some code was implemented, right, such that the tester themselves could create whatever code they wanted. And if you know, let's say, this unspoken SLA, if you can't create a test automation in 15 minutes or 20 minutes or the process takes a long time. Sometimes, every performance issues fine, you escalate, and you call a test automation developer, and they will figure out how to fix that as quickly as possible, so that from the service of being able to create test automation.

No Dev in the green cycle, the story of the kid revisited
And that's what I done with my QA team. And so, when I do the research now, with Claude, this is just taking it further. And so, no test Dev, but it's just no Dev at all right. Combine this sort of story of running tests in small batch sizes, where the developer being the bottleneck or the story of the junior developer who just used a test to debug? 

To me, claudes are just Junior developers in some ways. But this is why I think when you go down the path of removing the person from the picture. And letting the manual tester do the work themselves. This is basically no test Dev in the red cycle followed by no Dev in the green cycle

 Explain where SPC can be used to study the code variation. Self correcting process using spc to identify which parts of darmok plugin to improve. Either break up the tests or add more examples to uml-interaction.


E-mail

To be respectful of your time, and at the risk of sounding like the Deming version of a Jehovah's witness, I've prepared this pamphlet page which explains why I think you should bring Deming into your AI enabled process. 

I want to convey that there is a set of things you can do starting very small and making progress just as I did.

My ulterior motive is to have a deming success story. I would like to see someone say, hey, look. We apply Dr Demming's SoPK, and this is how amazing it became. We did all these things, and now we have no Dev and sure they can talk about the CI CD and the TD, but they must call out explicitly that this is a deming way

I think then once we have the success story, more people will want to copy this deming buzzword to get on the Deming bandwagon. But this is why I reached out to you because I just don't see anyone calling this stuff out in an amazing success story that just makes people want this, right?
So, I think we can use this AI. Bubble. To get some sort of publicity for Dr Deming's day working in software development.

1. [Extrinsic vs Intrinsic Motivation][1]

[1]: https://mosy.tech/books/learn-microservices/
