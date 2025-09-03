---
title: Involve the developers
---

First try to generate and run the automation in small batches without involving the developer. 
Once a tester is capable of generating automation on their own you can consider involving a developer. 
This is to make sure that when the time comes, the developer is only coding or wiring stuff together that is absolutely need it. 
This ensures that the time they spend executing the test cases is reduced as much as possible. 
We were trying to make it really easy for them to run these tests. 
Gradually the developer will deliver more virtually defect-free code to QA saving everybody time. 

Someone will need to code the transform, this is the test automator initially and gradually the developer themselves.
Initially the test automator codes the plug-in changes but also works on the developer git repo. 
For example, if you're using Java with some DI framework and Cucumber, you have to update the plug-in to generate the step library classes that reference the interfaces. You'd also have to setup the DI to work properly. If there's no DI framework, then you can use the Java reflection API or the constructor directly.
In the case of COBOL or PL/SQL, I'd make a simple Java main program. It would read the model and extract whatever data the developers needed in whichever format they wanted, be it CSV files or insert statements for a SQL MX database. 

As more teams adopt this approach and standardise on test frameworks, the transformations could all be moved into the QA plug-in or as services in the cloud so they could be reused.