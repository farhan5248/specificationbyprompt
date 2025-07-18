---
title: Poka Yoke
---

Initially didn't understand how they could change the way they worked such that developer defects could be prevented.
Fortunately one of the teams had an existing way of working that could be used to demonstrate defect prevention.

The team had a way to convert Word documents with test cases into setup data to be uploaded into the environment for testing.
The process could also automatically create SoapUI projects using it's Java API. 
However if folks made typos, they'd only realise that by the time they converted everything and ran it and watched the test fail. 
If they copied and pasted that mistake into a dozen other test cases, they'd have to do the tedious and frustrating task of redoing the work. 
Even if they worked in small batches to catch these earlier, running the plug-in that made that work go fast wasn't enough even if it was done in seconds.
Once we automated things and sped things up, they saw the time wasted fixing mistakes which were becoming the bottle neck.

I told them the story of Toyota and the automatic loom and we introduced the Eclipse Xtext Editor. 
Xtext is a language engineering framework and it controlled what someone typed the way any language editor in an IDE does. 
We basically defined a DSL which was a subset of the english language using a regular expression and it worked much like the Java code editor does in Eclipse. 
This language was eventually the Ubiquitous language in Domain Driven Design. 
The result was that they had fewer runtime errors and work to redo because syntax errors were caught as they typed. Before this it was like coding with a basic text editor, not an IDE. 
