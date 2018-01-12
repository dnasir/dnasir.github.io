---
layout: post
title: "Unresolved external 'LresultFromObject' error in RAD Studio 2010"
date: 2011-02-17 13:21
comments: true
categories: [C++, error, Internet Information Services (IIS), Programming, rad studio 2010, unresolved external lresultfromobject, Windows Server]
---
Recently, I've been working on a small service application that monitors the IIS7 server for calls to have the server restart or shutdown. The reasoning behind using a service instead of having PHP restart or shutdown the server was that PHP does not run as a user with restart/shutdown privileges. So, it became a necessity to have a service that monitors for any shutdown calls from PHP.

I created a small service application using Embarcadero's RAD Studio 2010 that runs as a service and checks for shutdown calls, as well as other processes. However, coming from Codegear, there were a few issues when importing an old application to be used as a base for the new application. Even after correcting all the paths that needed correction, I was getting this one error that prevented me from compiling the application.
<pre>[ILINK32 Error] Error: Unresolved external 'LresultFromObject' referenced from C:PROGRAM FILES (X86)EMBARCADERORAD STUDIO7.0
LIBRELEASEVCLACTNBAND.LIB|ActnMenus
[ILINK32 Error] Error: Unable to perform link</pre>
After spending some time searching for a solution, I came upon a post on Embarcadero's own forum, that became the solution to my compilation problems.

A forum user wrote that all I had to do was add a pragma link to "oleacc.lib" at the top of my code and that should allow RAD Studio to compile without any further problems.

Instead of adding a pragma link, I just added the library link using "Project&gt;Add to Project" and pointed towards "oleacc.lib" located at "C:Program Files (x86)EmbarcaderoRAD Studio7.0libpsdkoleacc.lib" on my computer.

That's it. I was able to compile after I added "oleacc.lib" to my project.Thank God.

The path to "oleacc.lib" could be different on your machine, so make sure you point RAD Studio to the correct location.

Til next time, wassalam.
