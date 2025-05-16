# Jidoka

In my experience, whether doing deploys or automating test execution or anything else like test case creation using MBT etc folks always aim for full automation; I don't. 
My approach is to automate a small part and then each time I do the same task again, I automate more, stopping at the point of diminishing returns.
This is why I like the definition of automation with a human touch for Jidoka. I don't think we should try to completely automate someone out of a job.

When I'd ask testers what about their job could we automate, most folks said nothing. 
I assume this was out of fear of me automating them out of their jobs so I focused on admin stuff like test case reporting.
The team used HP ALM to store test cases so we automated stuff around that using its web-services. 
When we moved to Azure DevOps/Team Foundation Server, we used its web-services. 
When we left behind HP QTP to go to Jenkins, we used the Jenkins web-services to automate managing it and to create custom reports from runs. 

Any automation I made had to be coded in 1 hour or half a day or one day. 
If it needed more time, we'd break up the improvement into smaller steps. 
The goal was to make something quickly and put it in front of the tester (customer) and get their feedback. 
An example is a tool the COBOL testers were using. 
It was tedious to click through a whole bunch of buttons to input 2-3 parameters so I made the custom Maven plug-in by-pass the GUI. 
Now they just double-clicked one script.