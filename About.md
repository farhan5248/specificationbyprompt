# What's this site about

## My background

I'm a developer and former dev team lead and manager. I've only worked on enterprise systems and spent most of my time on architecture, design and automated deployments.
During that time I found that QA took weeks to months to complete their test execution phase and in most cases didn't think CICD or DevOps applied to them.
Eventually I decided to lead a large QA team to figure out why and help them evolve. 
That one QA team had 4 QA teams of almost 50-60 individuals. 

## What I did on the QA team

I upskilled the manual testers in my QA team by applying Toyota Production System concepts, most importantly Kaizen.
I recently came across the term Deming Driven Testing [from Mike Harris](https://testandanalysis.home.blog/) which I like very much because it describes the approach I took accurately.
Keeping the improvement kata in mind, the direction of challenge is to gradually provide feedback earlier by having the QA tests run earlier.
The adoption happens with the support of tools like Eclipse Xtext, UML and EMF frameworks, Cucumber, Java, Graph Models, Maven, PlantUML, D3.js and GraphWalker.

## How I'm trying to share the knowledge

While helping the team learn, I found that instead of giving cold hard facts and principles, I communicated better with stories.
In this site, I'm attempting to explain some principles that I applied to those teams by recounting some of my experiences.
I'm putting as much of my knowledge that I've accumulated so far using models for coding/testing in these repos as examples that I'll reference in the stories.
Hopefully this makes it easier for someone else who's interested in doing similar things.

## Where all the documentation is

This is a work in progress so I'll be updating it daily/weekly. In general the documentation is divided up as so:
1. **Prezi**: To understand the context within which I've used these tools and frameworks, I created this [Prezi presentation](https://prezi.com/view/yNpSiGMbioX8lNp5tS2q/). 
2. **farhan5248.github.io**: I'm working on my github.io blog to move the Prezi content to it and include references to example code in the Git repos.
3. **GitHub Repo README**: This intro and how to build and run the plug-ins.
4. **GitHub Repo Wiki**: Explanations about why I designed things the way I did, roadmap of stuff that's left to implement and notes on how to create/modify the code for first time Xtext or Maven plug-in developers.
5. **GitHub Repo sheep-dog-qa**: This has the usage notes on how the plug-ins work. It's both the test cases and user manual. I wanted it to be an example of living documentation.

## Code Generation from the Ubiquitous Language

As I mentioned on the [home page](https://farhan5248.github.io) the outcome or direction of challenge was to adopt the ubiquitous language from DDD. 
[This link to Martin Fowler's bliki entry on Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html) describes the concept of Ubiquitous Language as a language that is used by both domain experts and developers. 
In my case, I considered the testers as the domain experts as did the developers.
[In his conversation with Eric Evans, at around minute 5](https://youtube.com/clip/UgkxwDpbV3Wzrdz0mNow9cglz9_KJuxLmj25?si=6Sx67uKN7UoKukVM), Dave Farley explains how he uses the ubiquitous language to write tests. 
This is what my QA team did and what I've done to create the test code for the plug-ins. 

The thing is though the tests were written in an IDE; even the manual ones. 
By getting the QA testers to write the tests in this language using an IDE to validate the syntax, it ensured that test automation could be automatically generated from the tests via the API created for the language; this is the key feature of the this project. 
It ensured that the language used to describe the business domain mapped to the system that was being tested.
If I asked them to write tests in the ubiquitous language using a wiki, Google doc or similar, it would be more error prone when generating the test automation. 
In fact at first they were using Microsoft Word and Excel and then those files were parsed and converted to automation it was like coding with Microsoft Notepad and finding your errors at compile time instead of as you were typing.
