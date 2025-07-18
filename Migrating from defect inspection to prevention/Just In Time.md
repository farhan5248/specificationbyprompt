---
title: Just in Time
---

My entire QA team worked in a waterfall project, all the code was written before any tests were executed. 
In some cases, you'd have 3 months of test case planning followed by 3 months of test case execution.
When I felt we had made it easy enough for developers to run the QA tests earlier, I explained what just-in-time manufacturing was and the waste of inventory. 
It was now time to work with our upstream parts suppliers, AKA the COBOL dev team.

One of our goals was to not have huge inventories of
1. Test cases that weren't executed yet. One team mainly worked with drug benefit health insurance and some features needed hundreds of new test cases, all with their own head hurting math equations. I used to see the test execution phase as the test re-planning phase as testers went through re-doing all the math, it was just a waste of time.
2. Executed test cases and their code not deployed to production just waiting in QA. The obvious problem with this situation was when the dreaded high severity high impact bug that puts the whole release on hold. The idea was that if we sign-off on something, we should be able to promote it.

To avoid this we'd work towards the following
1. Only write enough test cases to drive the development in the developer's local dev environment. If the tester was ahead, then work on improvements to make the next cycle quicker or go help someone else. The intention here was to have a level flow as opposed to famines and feasts of free time.
2. The developer should never write code that waits too long to have tests run against it. This is to prevent testing in medium batches after coding and actually work towards red green refactor. We don't want an inventory of untested code building because then we'd be waiting for the developer to redo the work and we'd find ourselves ahead again.
3. The code should only be deployed to QA just in time for a regression to finish before the deploy into the customer facing environment. This is to avoid dumping a whole bunch of code into QA and then face the pressure of having to sign-off on everything before it can leave the environment. There were times where the project managers and development managers would downgrade defect severities just to get the sign-off and promote buggy code into production.
