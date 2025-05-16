# Involving Developers

First try to generate and run the automation in small batches without involving the developer. This is to make sure that when the time comes, the developer is only coding or wiring stuff together that is absolutely need it. This ensures that the time they spend executing the test cases is reduced as much as possible. We were trying to make it really easy for them to run these tests. Once a tester is capable of generating automation on their own you can consider involving a developer. Gradually the developer will delivery more virtually bug free code to QA saving everybody time. 

# Dependency Injection

Someone will need to code the transform, this is the test automator initially and gradually the developer themselves (they should use the EMF API to transform the UML model). 

Initially the test automator codes the plug-in changes but also works on the developer git repo. For example, if you're using Java with some DI framework and Cucumber, you have to update the plug-in to generate the step library classes that reference the interfaces. You'd also have to setup the DI to work properly. If there's no DI framework, then you'd use the Java reflection API.

In the case of COBOL or PL/SQL, I'd make a simple Java main program. It would read the UML model XML file and extract whatever data the developers needed in whichever format they wanted, be it CSV files or insert statements for a SQL MX database. 

As more teams adopt this approach and standardise on test frameworks, the transformations could all be moved into the QA plug-in so they could be reused.

# Red Green Refactor

One day, the COBOL dev lead (decades long programmer) told me a story about a feature they had just delivered. The developer was apparently a new-grad and had around a year of COBOL experience and worked on the system as a co-op student. He had worked on a more challenging feature. They had to put a junior person on it because they were short on people. On the QA side, a more senior person was assigned to this feature because of it's complexity. 

Here's what they had thought would happen had they worked without the automated QA tests
1. Being junior, they wouldn't know what all to code and so someone would have to check their work and then redo it
2. Despite the coaching, when the time came to integrate the changes, they might break the work of others
3. When the code came into QA, we'd be running around chasing defects related to newbie related mistakes or misunderstanding.

Yet none of that happened. What did is that the developer knowing that they don't know the system, co-ordinated with the tester. The tester created enough tests for the developer each day to explore the code with the debugger and only change the bare minimum. They basically went through cycles of red-green daily. At the end of the week or so, the senior developer coached them on refactoring etc. By the time the code came to QA, it was defect free. 

The dev lead realised what I had a while ago, with BDD you can move developers around more easily. While it was nice to have developers with deep knowledge of the system, it wasn't necessary for them to work on the scarier features but just pair up to coach.

# Just in Time

In one release, the monolithic build had to go into QA on a fixed data. However one developer was not done. 
Here were her traditional options:
1. If everyone waited for her, all the other features wouldn't be tested in time
2. If she put whatever code she had, it would create bugs within the system.

Instead I told her to just deploy her DDL changes, that is none of the logic, just changes to file layouts, database tables etc and make sure that it wouldn't break anything. 

What she was doing with the tester was red-green-refactor during that project in a lower dev environment. I told her that once she was done in that environment, and after merging her code with the rest of the code base and with help from the tester, they could run any related test suites for any features in that release before deploying to QA. 

The result was that they only put the code in once they knew they wouldn't have any defects for that component. They didn't put the code in QA to test if they had defects. The only testing left was integration tests with Oracle DB and Java code etc.

At this point the COBOL testers realised that they could basically work side by side with developers in the lower environments until they were satisfied. Then one by one, they'd integrate and test and deploy code to QA only as the tester was ready to do any integration tests. They'd also schedule it such that it would be done just before the deploy to the customer facing environments. One tester estimated they'd only be in QA for 2 days (automated regression cycle) per feature instead of the 3 months.

They were slowly getting how Continuous delivery would work.

# Measuring Progress

In one of the big 6 month releases, the devs and testers stated they need 9 months or something to do all the work. They both needed 4-5 months for the planning/coding and then the execution. By that point in time, my entire team of COBOL testers were ready to go work with the developers in their lowest dev environment. I decided that the planning and coding could be done there in parallel and that the developers could take the 4-5 months to code. Most folks were confused as to how that'll work out with the testers having 1-2 months to then run what was estimated to be 4-5 months of test cases. The code came in on time, all defects were found and fixed in a week or two. 

How did it work out? Automated QA test execution earlier in a dev environment prevented defects and all the resulting work that was estimated. Given the defect discussions happen earlier, the code will get deployed to QA later but it'll have fewer defects. The QA execution will start later but because of fewer defects and automated execution, it will also finish earlier as will the regression. 

I currently think I'm doing BDD or shift left testing well if the following are true
1. The overall time to execute test cases in QA goes down.
2. Defect counts go down but planning time goes up because defect discussions happen earlier.
3. QA tests executed automatically in lower environments goes up.
4. Developers spend less time fixing defects because they're just contract or integration defects and not complex logic ones.
5. Developers can take more time to get the code to QA and nobody panics.
6. More developers can pair up to help each other finish the work on time. This is important for monolithic builds.

# Testing Hourglass

I say virtually bug free and not bug free because we were converting E2E tests to component tests (it helps if you visualise the testing pyramid). There wasn't much integration tests with the other development teams that weren't interested in this way of working. As a result the only bugs found as a result of the E2E testing were integration defects with other systems which could also have been prevented.

The other issue is that because of the way the COBOL code was integrated before going into QA, my team was not that comfortable with not running the tests in QA since we still had to sign-off. In the end, hundreds of test cases per feature could run in minutes so there was no downside to just re-running the same tests. Not ideal but it worked.