---
title: Install the Tools
---

It's better to have as few tools as possible when doing such a transformation to reduce the cognitive overload and it's less intimidating to folks who've never worked with an IDE before. It's important to identify the early adopters before doing this. You don't want to take everyone to the top of the ski slope at once.

# Maven

The first tool to install was Maven. I'd use it to implement the little utilities on the testers laptops. Before installing it, make a hello world custom plug-in and run it on their laptops. Make sure there are no JDK/JRE issues or firewalls blocking Maven's access to the central repo etc. 

# Eclipse or VSCode

The other tool to install was Eclipse. It's the IDE I was most familiar with so it was a natural choice in the beginning but later continued with it because of the Xtext framework. Also make sure they can install the custom Xtext plug-in. Nowadays you can run the [Xtext plug-in as a VSCode extension][1].

# Git

The most important tool is Git. I told my QA team that they're there to provide a product; quality data. I explained to them how containers transformed the shipping industry and how Docker transformed deployments. Our solution then needed to have a way to continuously deliver the data from the testers workspace to that of our customer, the developer. This is where Git comes in, developers could simply clone our repo to get the data. Later I preferred using a RESTful API which got around the need for a developer to know Java just to use the API to browse the test data.

Each person will go through 3 git repos; just a local one until they're confident, then a sandbox one where they can practice pushing and pulling changes and working with others. Finally the 3rd main one when they can resolve merge conflicts on their own. Which files to practice with? Test cases, even if it's a word doc. What if there are no files? Use Maven to pull down the test cases from HPQC for eg and convert to AsciiDoctor, with the validation turned off.

I thought my testers 4 things to do daily with Git
1. Commit their work, early and often.
2. Pull any remote changes on the branch and handle any conflicts
3. Merge in the release branch. The release branch had the baseline for the current waterfall release.
4. Push the changes

# Team Tools

If you're wondering which tools were used by my QA team, they were:

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

[1]: https://github.com/cdietrich/xtext-languageserver-example
