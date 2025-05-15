# Repositories and Projects

There are 4 repositories
1. **sheep-dog-ops**: Contains deployment automation code for sheep-dog-* projects.
   - **sheep-dog-mgmt-maven-plugin**: The Maven release plug-in doesn't work for the xtext projects so I made this to mimic the behavior.

2. **sheep-dog-qa**: Contains the tests written in Asciidoc from which test automation is derived for the sheep-dog-local and sheep-dog-cloud projects. 
I think the tests should be in the Git repo as the code that is being tested so that I can run it easily. 
However if you have an end-to-end test which applies to several components, where do you keep them? For my QA team we kept the tests in one central repo. 
Then as is demonstrated with the code in these repos, tests applicable to a component can be queried via the API and transformed into test automation. 
That test automation is kept in the same repo as the code being tested.
   - **sheep-dog-specs**: Living documentation about the plug-ins. They are converted into test automation but also serve as documentation about what the plug-ins do.

3. **sheep-dog-local**: Eclipse and Maven plug-ins to help manual testers support developers adopting Deming driven testing. All the code generation is done locally. 
This is what I had implemented for my team. That is, all the transformations were done locally. 
I actually preferred to have them on a server but this approach allowed me to move without dependencies on external teams that controlled the infrastructure. 
I think this approach is best in the beginning so testers can get started using the tools and only consider moving the code to the cloud when scaling up.
   - **sheep-dog-test**: Contains semantic validation rules. Used in the **sheepdogxtextplugin.parent** to demonstrate added a dependency.
   - **sheepdogxtextplugin.parent**: Eclipse DSL plug-in that the testers use to write their test cases in the ubiquitous language.
   - **sheepdogxtextcukeplugin.parent**: Example of using Xtext to generate an API for a language like Cucumber feature files.
   - **sheep-dog-dev**: Converts the test cases into UML models and converts those into Cucumber and Java.
   - **sheep-dog-maven-plugin**: Maven plug-in that's just a wrapper around **sheep-dog-dev**. 

4. **sheep-dog-cloud**: Eclipse and Maven plug-ins to help manual testers support developers adopting Deming driven testing. All the code generation is done remotely in k8s.
This project serves 2 purposes. One is to demonstrate how the code would be implemented as services. 
The second is to demonstrate how a monolithic code base is split into micro-services following an event-driven architecture. 
This was the original problem I was trying to solve, break up a large monolithic platform and have the QA tests executed earlier.
   - **sheep-dog-dev-svc**: Currently wraps **sheep-dog-dev** as a Spring Boot service. This is going to be changed over the next few weeks to be several services and include **sheep-dog-test**.
   - **sheep-dog-dev-svc-maven-plugin**: Maven plug-in for **sheep-dog-dev-svc**.
