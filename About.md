# What this site is about

## My background

I'm a Java developer and former dev team lead and manager. I've only worked on enterprise systems and spent most of my time on architecture, design and automated deployments.
During that time I found that QA took weeks to months to complete their test execution phase and in most cases didn't think CICD or DevOps applied to them.
Eventually I decided to lead a large QA team to figure out why and help them evolve. 
That one QA team initially had 4 QA teams of almost 50-60 individuals but was reduced to 2 with around 20-30 folks.

## The approach I took

I recently came across the term Deming Driven Testing from [Mike Harris](https://testandanalysis.home.blog/) which I like very much because it describes the approach I took accurately.
I applied the following to the process of inspection carried out by the QA team:

1. Toyota Production System concepts (Jidoka, Kaizen, Poka-Yoke)
2. Dr Deming's System of Profound Knowledge
3. Improvement Kata by Mike Rother
4. Lean Software Development by Tom and Mary Poppendieck

At first the process of inspecting for defects became more efficient. 
The testers were generating test automation and running it progressively earlier.
That is, first the automated tests were made by a test automation developer and run at the end of the first manual cycle of testing but later it was the manual testers generating the automation and running it themselves during that cycle.

Eventually in the case of one team, the testers were running their automated tests alongside developers in lower environments so that quality was built-in. 
The result was that almost all of that QA team's end-to-end tests were run as component tests before the code was delivered to the QA environment and was virtually defect-free.

I should add that the integration process was weak and we wound up implementing the [Test Hourglass anti-pattern](https://testing.googleblog.com/2020/11/fixing-test-hourglass.html) but at least the last phase was done in a week or so, down from months



## How I'm trying to share the knowledge

While helping the team learn, I found that instead of giving cold hard facts and principles, I communicated better with stories.
In this site, I'm attempting to explain some principles that I applied to those teams by recounting some of my experiences.
I'm putting as much of my knowledge that I've accumulated so far using models for coding/testing in these repos as examples that I'll reference in the stories.
Hopefully this makes it easier for someone else who's interested in doing similar things.

I'm actually listening to The Goal by Eliyahu M. Goldratt for inspiration. 
I'm starting with the QA portion of my story but there's also the dev portion.
That is, what made me think of joining a QA team when my own team did TDD/BDD and had a CICD pipeline and fully automated deployments to production.

## Where all the documentation is

This is a work in progress so I'll be updating it daily/weekly. In general the documentation is divided up as so:
1. **Prezi**: To understand the context within which I've used these tools and frameworks, I created this [Prezi presentation](https://prezi.com/view/yNpSiGMbioX8lNp5tS2q/). 
2. **farhan5248.github.io**: The stories of how the team transformed are here. I'm working to move the Prezi content to it and include references to example code in the Git repos.
3. **GitHub Repo README**: This intro and how to build and run the plug-ins.
4. **GitHub Repo Wiki**: Explanations about why I designed things the way I did, roadmap of stuff that's left to implement and notes on how to create/modify the code for first time Xtext or Maven plug-in developers.
5. **GitHub Repo sheep-dog-qa**: This has the usage notes on how the plug-ins work. It's both the test cases and user manual. I wanted it to be an example of living documentation.
