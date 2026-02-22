---
title: From Shift Left to No Dev
---

# E-mail

To be respectful of your time, and at the risk of sounding like the Deming version of a Jehovah's witness, I've prepared this pamphlet page which explains why I think you should bring Deming into your AI coding process. 

I want to convey that there is a set of things you can do starting very small and making progress just as I did.

My motivation is to have a deming success story. I would like to see someone say, hey, look. We apply Dr Demming's SoPK, and this is how amazing it became. We did all these things, and now we have no Dev and sure they can talk about the CI CD and the TD, but they must call out explicitly that this is a deming way

I think then once we have the success story, more people will want to copy this deming buzzword to get on the Deming bandwagon. But this is why I reached out to you because I just don't see anyone calling this stuff out in an amazing success story that just makes people want this, right?
So, I think we can use this AI. Bubble. To get some sort of publicity for Dr Deming's day working in software development.

# Proposition

Gene Kim was on Dave Farley's podcast, and he said that [if you want to move really fast, you need a feedback and a control system to make sure you don't go off the rails][2]. I think that for an AI coding agent, controlling the variation in its output such that it produces code of a consistent level, is achieved by taking a test driven approach. So understanding factors that affect variation and having quality built in are the reasons why I think Deming still matters and especially more so with AI.

What's the aim of the system of work or the direction of challenge? Reduce hypothesis confirmation time. While you don't need a tester to leverage code-generation (I don't while I do my research); if you do have them, you should treat them like scientists. The metric that you're trying to reduce is the time it takes for them to confirm their hypothesis (passing test case). While on the QA team, we reduced that time from months to hours. Now with Claude I can reduce that further to minutes.

Why start with your manual testers? Most of the work they do around inspection is non-value added and easier to automate. This will create space for them to learn and then they can pay that forward. Even if your devs can write the tests themselves like I can, you probably don't want your technical resources doing non-technical work. It's enjoyable for me to work this way because I'm effectively designining my own product. However I'd rather a tester work with the business analysts etc to do this if I were back at a large enterprise. I can then focus on reducing the variation and basically being an industrial engineer like Ohno.

# Before AI - Mona

How did we get to the DSL editor and no-test-dev with maven
 Drug-engine shift left. This is how we went from months of waiting to confirm the hypothesis to hours with Mona using no-test dev platform.

 With my QA team, they base the test automation developers made a platform, and it was almost like as if it was no dev, but no Test Dev, right.
 And it was test coding as a service, whether it was Eclipse and a jar file for an API. The idea and today could be vs code and a bunch of services in the cloud.

 The goal was that the test automation developers automated themselves out of the job of coding. Instead of the tester talking to the test automation developer and saying, hey, can you automate this for me? That job disappeared. And what was there instead was a set of services.

 Some code was implemented, right, such that the tester themselves could create whatever code they wanted. And if you know, let's say, this unspoken SLA, if you can't create a test automation in 15 minutes or 20 minutes or the process takes a long time. Sometimes, every performance issues fine, you escalate, and you call a test automation developer, and they will figure out how to fix that as quickly as possible, so that from the service of being able to create test automation.

Links 
- can you run the all the tests earlier. verification ones, you'll still need validation
- Why the hypothesis metric? explain why I focused on this and why other ones don't need no-dev. Refer to mura
- solving heijunka with dsl editor

# After AI - the kid

 the story of the kid, junior developers making defect free code. basic debugging skills armed with tests provided in the right sequence. 

Describe how the no-test-dev is used in red to support green cycle in RGR and this ensures quality built-in.
No Dev in the green cycle, the story of the kid revisited
And that's what I done with my QA team. And so, when I do the research now, with Claude, this is just taking it further. And so, no test Dev, but it's just no Dev at all right. Combine this sort of story of running tests in small batch sizes, where the developer being the bottleneck or the story of the junior developer who just used a test to debug? 

To me, claudes are just Junior developers in some ways. But this is why I think when you go down the path of removing the person from the picture. And letting the manual tester do the work themselves. This is basically no test Dev in the red cycle followed by no Dev in the green cycle

 Explain where SPC can be used to study the code variation. Self correcting process using spc to identify which parts of darmok plugin to improve. Either break up the tests or add more examples to uml-interaction.

 Links
 - the story of the kid
 - initial research

# References

## UPDATE MURA

 The goal was to try and reduce variation in the process of inspection. This would involve identifying it, then the overburdened individual and then the non-value wasteful activities that we'd automate away. The most useful or eye opening metric was the time to confirm a tester's hypothesis. review variation/mura page, explain the xbarR chart for scientist time to confirm hypothesis

 Explain what variation was studied to point me in this direction, specifically the time it took to confirm a hypothesis if you treat your testers are scientests. The DSL editor was a tool used to reduce toil/overburden which was reduced by eliminating waste. Part of reducing overburden meant writing tests that are so clear that eventually a computer program could take over the test automation coding. With defects being systematically prevented through earlier verification testing, there was less of a need for inspection and we focused on validation testing. Spreadsheet driven testing was at least twice as fast as what I was doing with Cucumber or Robot Framework. But the speed of mass inspection at the end as a regression wasn't the goal. I was trying to reduce the dependence on inspection in the first place. 

This metric of the time it takes to confirm a hypothesis. And know if it is good or bad comes about from using SPC. I did not know what I was doing. I collected a lot of data. And basically. Um. It's not so much the number of test cases someone was executing per day, whether the test passed or failed because you could execute two to three tests per day and then go up to 20 or 30.
In the beginning. The two to three simply means you're starting a new feature. You proceed a little slowly. Everything is going as expected. There's nothing you know that is unexpected that you need to deal with. Well, it becomes interesting is a sharp drop for a long time. It's okay for you to be executing 20 deaths per day and tomorrow, going back to two and then three because you switch from one feature to another.
But what I found interesting was. When you go from? Plenty tests per day to zero for the next couple of days. And that's because you're just re redoing your test cases. And that's how I discovered that. Um, that's how this metric became of interest to me to study. How do I make sure that the testers can write a good test case, right?
And Sure, you could have used a model based testing tool and that will help them write the test cases really fast. Right, but that doesn't prevent them from confirming the hypothesis that they wrote the test case is really fast, because all it has done is written. A great many test cases that are wrong really quickly.
It's only when they're able to run the test against the code can they confirm the hypothesis.

## UPDATE HEIJUNKA

 update roser podcast review about heijunka. Instead of 4 door and 2 door car, it was drug app vs mb app work. First DA tried automating DA tests but there was no point. Then they spent time trying to automate MB tests but couldn't understand them and slowed down the MB SME. Then they helped execute MB tests in pairs and the MB testers wrote tests with less assumed knowledge.

## TRANSCRIPT


1. [Extrinsic vs Intrinsic Motivation][1]

[1]: https://mosy.tech/books/learn-microservices/
[2]: Gene clip on engineering room