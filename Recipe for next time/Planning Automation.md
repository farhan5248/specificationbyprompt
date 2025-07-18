---
title: Planning Automation
---

This is about making it easier for someone to write a test case. Specifically it was generalist testers in the Stream aligned teams. Just as the junior COBOL developer was able to use the tests to walk through the code, we want to make it easier for a junior or new tester to do the same but with test cases. The MBT model can fill in gaps between steps or create new scenarios as a basis for new test cases. The code completion features of Xtext combined with a model can help suggest next steps or values based on existing test cases.

# Model Based Testing

I started socialising the concept of model based testing (MBT) to my team. The team estimated that it would cut down 75% of the front-end test planning time and 90% of the back-end test planning time. The tool we selected in the end was the Curiosity Test Modeller which I liked best. I like this tool because everything you can do with the GUI, you can do via web-service. An example of this can be seen at Everfi. My intention was to have this tool used by the complex subsystem team since there is a bit of a learning curve to it. They'd use it to refactor tests or review the test cases. If you know who Simon Brown is, I figured it would be like a testing version of that as well. 

However in the beginning people have a hard time visualising a model. I tried using PlantUML diagrams and D3.js to show them how their test cases can be used to create models automatically. I think the biggest barrier to entry is the ability to quickly create a model. Even if you have existing feature files, if they don't have sufficient detail, they're not very useful.

By the time you're automatically generating test automation for the testers themselves, the test cases are detailed enough. At that point I'd introduce MBT with D3.js visualisations or PlantUML to get people comfortable understanding what a model is. Then they can think of expressions that replace literal values.  This should make it easier to migrate to an actual MBT tool.

# Product not Project

For whatever reason, whether it was in Sharepoint, Google Drive, HP ALM, testers stored test cases by projects. Then if you wanted to find the current set of test cases that specified the behaviour of the system, you'd have to either go through every project or simply know which projects touched that functionality. Eventually you wind up with individuals that become bottlenecks because they alone know how a feature works. It might sound great thinking you have job security but in reality it was very stressful and folks couldn't go on vacations.

One of the goals was to move away storing test cases in folders representing projects to one representing products and functionality. If MBT wouldn't be adopted, having a product structure would make it easier for generalist full-stack testers in the stream aligned teams to find things. This is part of the culture of easy ownership. We wanted to make it easier for a tester to help another tester write the test cases just as a junior and senior developer could pair up to write code.

# Test Plan Document

The last thing to cut down on test planning was the test plan document. The team started to question what purpose it had when we were sharing test cases on a daily and hourly basis with the developers. In the past it was needed for reasons that increasingly no longer applied. For example in the past the test plan communicated a summary of which functionality would be tested. 

When developers were running tests daily, this summary wasn't needed by them. It was still needed though by the Business Analysts who had to sign-off on the test coverage as part of the audit. This was largely a pointless process and I'd be surprised if most of them even opened the document because they believed the testers knew better what the coverage should be. 

The team's suggestion was to use Cucumber tags and descriptions creatively such that a summary could be derived automatically. That combined with AsciiDoctor fragments for boiler plate contents, would be automatically turned into a PDF for the purpose of the audit.