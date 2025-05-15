# The Awesome Machine

What initially took 5 business days and 10+ developers to deploy to production was eventually done automatically in a few hours, 2 hours ahead of schedule the first time. When I was hired to join the team, every single senior developer with 10+ years of experience said it couldn't be done but I chipped away at it and eventually with support from my manager, willing developers and the architecture team we got there. The only person who could understand how I felt was Andy Dufresne at the end of The Shawshank Redemption. After reading Team of Teams, I would call it my "awesome machine" but I soon came to realise that it wasn't quite so.

To give you an idea of the scale of the CICD pipeline, there were these environments
1. 3 stand alone development environments
2. 3 integrated ones
3. 3 QA ones
4. 2 performance testing ones
5. 4 Virtual test environments 
6. 2 customer facing self-test environments
7. 3 prod sized environments

How many VM/servers were there in a prod-sized environment? I remember the list having 200+ entries, these are some of them
1. The front end had 15 VM
2. The API gateway had 4 VM
3. The application tier had 20 integration servers, 10 MQs. Each IS had a MemCached server, there were 12 or more app servers
4. There were 6 sets of 3 nodes of Oracle databases, so 18 of those
5. 12 Oracle IDM related servers
6. Then there were the Splunk servers and their predecessors.