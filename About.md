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

If you're wondering which tools were used by the testers, they were:

1. Eclipse
2. Eclipse Modelling Framework (UML)
3. Eclipse Xtext
4. Java Universal Graph Library
5. PlantUML
6. D3.js
7. GraphWalker
8. Cucumber
9. Java
10. SoapUI
11. Groovy
12. Robot Framework
13. Python
14. PyCharm
15. Jenkins
16. Maven

## The direction of challenge?

Keeping the improvement kata in mind, the direction of challenge is to gradually provide feedback earlier by having the QA tests run earlier.
To make it concrete, the QA testers needed to write their test cases in the ubiquitous language described in DDD by Eric Evans using an IDE.

This link to [Martin Fowler's bliki entry on Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html) describes the concept of Ubiquitous Language as a language that is used by both domain experts and developers. 
In my case, I considered the testers as the domain experts as did the developers. 
[In his conversation with Eric Evans, at around minute 5](https://youtube.com/clip/UgkxwDpbV3Wzrdz0mNow9cglz9_KJuxLmj25?si=6Sx67uKN7UoKukVM), Dave Farley explains how he uses the ubiquitous language to write tests. 
This is what my QA team did and what I've done to create the test code for the supporting code. 

Regardless of whether the test was intended to be automated or not in the current process, everything was written in that language.
I won't go into the details of how we arrived at the conclusion that it was the goal over 3 years here, that's covered in the stories.
I will say that I started out with the intention to adopt a keyword driven test automation approach but that had its own issues, namely it devolving into a programming language.

Why the Ubiquitous language, why not just teach them to write the Java/Python code?
Teaching that many people to code would be more challenging (details explained in the stories) but think of the ski slope analogy in Sooner Safer Happier by Jonathan Smart.
Instead, refining a language they already used to communicate with the BSA and developers required less tweaking to how they work.

Why use an IDE? Why not Jira, Word, Google Docs, Excel, Confluence? 
In fact at first they were using Microsoft Word and Excel and then those files were parsed and converted to automation it was like coding with Microsoft Notepad and finding your errors at compile time instead of as you were typing.
In addition to the usual benefits of using a language editor in an IDE the [Xtext framework](https://eclipse.dev/Xtext/) gave us an API to query all the test cases written by everyone in my team.

To be clear this is like the [Java Parser API](https://javaparser.org/) I use to generate Java code automatically.
Through this API, you could extract whichever data you wanted to transform it into whatever you wanted from all of the tests written by all of the testers in the entire team for every component they tested.
It also ensured that the language used to describe the business domain was mapped to the system that was being tested.

## How I'm trying to share the knowledge

While helping the team learn, I found that instead of giving cold hard facts and principles, I communicated better with stories.
In this site, I'm attempting to explain some principles that I applied to those teams by recounting some of my experiences.
I'm putting as much of my knowledge that I've accumulated so far using models for coding/testing in these repos as examples that I'll reference in the stories.
Hopefully this makes it easier for someone else who's interested in doing similar things.

I'm actually listening to The Goal by Eliyahu M. Goldratt for inspiration. 
I'm starting with the QA portion of my story but there's also the dev portion.
That is, what made me think of joining a QA team when my own team did TDD/BDD and had a CICD pipeline and fully automated deployments to production; why wasn't that enough?

## Where all the documentation is

This is a work in progress so I'll be updating it daily/weekly. In general the documentation is divided up as so:
1. **Prezi**: To understand the context within which I've used these tools and frameworks, I created this [Prezi presentation](https://prezi.com/view/yNpSiGMbioX8lNp5tS2q/). 
2. **farhan5248.github.io**: The stories of how the team transformed are here. I'm working to move the Prezi content to it and include references to example code in the Git repos.
3. **GitHub Repo README**: This intro and how to build and run the plug-ins.
4. **GitHub Repo Wiki**: Explanations about why I designed things the way I did, roadmap of stuff that's left to implement and notes on how to create/modify the code for first time Xtext or Maven plug-in developers.
5. **GitHub Repo sheep-dog-qa**: This has the usage notes on how the plug-ins work. It's both the test cases and user manual. I wanted it to be an example of living documentation.
