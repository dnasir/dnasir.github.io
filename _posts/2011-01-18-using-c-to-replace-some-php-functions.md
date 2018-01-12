---
layout: post
title: Using C++ to replace some PHP functions
date: 2011-01-18 19:28
comments: true
categories: [C++, c++, custom functions, Internet Information Services (IIS), PHP, php, Programming, Windows Server, windows server]
---
So my boss told me that we won't be changing server OSes anytime soon, which means I'll have to bite my tongue and refresh my C++ skills. I decided to go with C++ to replace certain PHP functions, especially the ones that deals directly with the Windows file system.

Since I have never used the Windows C++ API and it has been about 5 years since I last programmed in C++, it took me about 3 days to get used to Visual Studio 2008. But I found it to be rather comfortable and easy to use, albeit me constantly making mistakes with defining variables.

I made my own version of PHP's DirectoryIterator using WINAPI's FindFirstFile and tested it on the problem directories, and what do you know, it worked. Thank God.

So I guess I'll be replacing some other PHP functions with C++ ones. My only concern is the overhead this method will produce, seeing as how PHP is still used to call the C++ program and process the return values. So, to make myself and my server happy, I decided to have my PHP scripts run everything in PHP first, and only use the C++ functions when it fails using PHP function. If the C++ function also fails, then just return false, or some kind of error.

If you know of a simpler and more effective method for achieving this, I'll all ears.. or eyes.

Until next time, Wassalam.
