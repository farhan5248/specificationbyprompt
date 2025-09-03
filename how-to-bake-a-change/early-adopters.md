---
title: Identify early adopters
---

Run the Maven plug-in to do offline validation of the test cases by converting them to AsciiDoc or Markdown. 
The goal is to identify early adopters and innovators before asking them to change. 
To be clear, don't install maven on their laptops and run it there, pull the data using web-services from HPQC, TFS, Sharepoint or wherever the tests are currently stored.
Some testers are very detailed oriented and meticulous, some are not. 
We want the former to be volunteers first since it's easier for them to generate automation; they don't need to add more detail. 
Introduce the regex to validate their test steps gradually. 
Invite the testers to volunteer to see how valid their test cases are. 

Though I was the manager, I was also the developer doing most of the coding around automating how we worked.
If you're not able to code, then you need to find someone who is and needs little convincing :).
Once the early adopter testers are using the DSL, you can now move onto the test automation folks.
Have the test automation developers volunteer to try manually cleaning up the files of testers and then converting them to test automation through the Maven plug-in. 
The goal here is to identify which of these developers can be early adopters and are willing to change their way of working to support the testers. 

Some of them might see testers learning parts of their job as a threat to their job security so not everyone will be thrilled. 
I like to auto-generate code but I find not everybody is. 
I assume when assembly languages were all the rage, folks balked at the use of C or C++ and so similarly you'll find some developers who are against generating test automation rather than them doing it.
Nowadays with AI coding tools, it's interesting to see how that's playing out. 

Run the validation everyday to detect any large overheads that need to be reduced. 
Hopefully folks are using Git and if so get a Jenkins job or whatever to automatically run it. 
Checks have to be done in seconds, not minutes. 
The feedback needs to be given to the tester earlier just like running tests and giving developers feedback would happen earlier. 
This would be the first experience of testers having someone test their work and telling them to rework it. 