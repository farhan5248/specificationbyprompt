---
title: Early Adopter Automators
---

Have the test automation developers try manually cleaning up the files of early adopter testers and then converting them to test automation through the Maven plug-in. The Maven plug-in makes it easier to parse the Xtext defined DSL language files that can be transformed into test automation code. The goal here is to identify which of these developers can be early adopters and are willing to change their way of working to support the testers. 

Some of them might see testers learning parts of their job as a threat to their job security so not everyone will be thrilled. I like to auto-generate code I find not everybody is. I assume when assembly languages were all the rage, folks balked at the use of C or C++ and so similarly you'll find some developers who are against generating test automation rather than them doing it.

Run the validation everyday to detect any large overheads that need to be reduced. Hopefully folks are using Git and if so get a Jenkins job or whatever to automatically run it. Checks have to be done in seconds, not minutes. The feedback needs to be given to the tester earlier just like running tests and giving developers feedback would happen earlier. This would be the first experience of testers having someone test their work and telling them to rework it. 