---
title: Remove admin toil
---

Before approaching someone to change their work, start removing non-threatening stuff like admin stuff. 
Some people think knowledge gives them job security so sharing that is scary. 
Admin stuff is boring, automating it gives you an excuse to work with them and get to know about them. 
Even if you start with the early adopters, eventually you need to do the same with folks who aren't as comfortable with change so that's why you should start with boring tasks.

Another reason to automate admin work is related to implementing Continuous Delivery. My QA team had to satisfy an audit and so there was a fair bit of reporting to do. It's one thing to work on the technical issues of CICD like BDD but what about all the paper work? Knowing that I couldn't implement Continuous Delivery instantly, I wanted to know if we could have a fully automated system that initially co-existed with the manual testing practices. Working towards a proper CICD pipeline we'd then chip away at that testing cycle. We'd gradually automate not just test execution and planning through Kaizen but also any documention and administration work.

Such automation of manual admin or documentation tasks can be done with Jira or HP ALM or web-services. In the beginning you can make a CICD pipeline like I did and deploy the code into QA. Then every day you can have a nightly job check via web-services if all the work is done and then move onto the next stage. For example, I used web-services to test if all the work for a feature was done or if a tag existed and then trigger a Jenkins job via its web-services. A lot of how we used TFS was designed around reducing human intervention. 