# Poka Yoke

This was the last improvement type to introduce. 
Folks didn't get it until we automated things and sped things up. 
They then saw the time wasted fixing mistakes which were becoming the bottle neck.

The team had a way to convert Word documents with test cases into setup data to be uploaded into the environment for testing.
The process could also automatically create SoapUI projects using it's Java API. 
However if folks made typos, they'd only realise that by the time they converted everything and ran it and watched the test fail. 
If they copied and pasted that mistake into a dozen other test cases, they'd have to do the tedious and frustrating task of redoing the work. 
Even if they worked in small batches to catch these earlier, running the plug-in that made that work go fast wasn't enough even if it was done in less than a minute.

I told them the story of Toyota and the loom and we introduced the Eclipse Xtext Editor. 
This is a language engineering framework and it controlled what someone typed the way any language editor in an IDE does. 
We basically defined a DSL which was a subset of the english language using a regular expression and it worked much like the Java code editor does in Eclipse. 
This language was eventually the Ubiquitous language in Domain Driven Design. 
The result was that they had fewer runtime errors and work to redo because syntax errors were caught as they typed. Before this it was like coding with a basic text editor, not an IDE. 