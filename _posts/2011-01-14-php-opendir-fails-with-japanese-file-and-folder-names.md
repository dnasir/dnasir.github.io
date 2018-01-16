---
layout: post
title: PHP opendir fails with Japanese file and folder names
date: 2011-01-14 13:05
comments: true
categories: [encoding problem, iis 7, Internet Information Services (IIS), opendir, PHP, php, Windows Server, windows server]
---
I've been working with PHP to handle the server-side programming for a C++ application I am currently working on at work. Being a Japanese firm, it's obvious our clients are also Japanese, thus working with Japanese file names also being obvious.

However, lately I've been having problems with PHP not being able to create a handler for folders with Japanese names. PHP's DirectoryIterator gave me an error, and opendir just fails completely. This only happens with certain Japanese characters, like the Katakana for the letter 'ソ' and the kanji '表'. Upon further research, I've discovered that there is a possibility that PHP's opendir is not handling multibyte characters properly, resulting in them being misread as individual single byte characters. Shift-JIS character table reveals that the letter 'ソ' has a value of 0x835C. So assuming PHP is misinterpreting the multibyte character as a single byte character, it would show up as 83 and 5C.

This is where the problem lies, because according to the ISO-8859-1 character set, 5C is the character code for the backward slash `\` symbol. This becomes a problem due to the fact that Windows accepts both forward slash and backward slash as the director separator symbol. So `C:\Windows` can also be `C:/Windows/`.

Because of this, the word "ソフトバンク" can easily be misinterpreted as " フトバンク" and in Windows, this refers to a folder called "フトバンク" which just doesn't exist on my server.

<!--more-->

To prove that the Windows file system encoding is to blame, I booted up my Linux server box and uploaded all the related PHP files. This is a headless machine, so I'm basically stuck with the command line. I created several Japanese-named folders and files using Filezilla FTP client, and connect to the server using my Win32 test application.

The result? I was able to traverse through all the directories, as well as create new folders using Japanese characters. Creating folders using Japanese characters worked fine on IIS7, but once that folder is created, the application cannot open that new folder.

I informed my supervisor about my findings, and I gave him a couple of options.

* Keep using Windows Server 2008 (IIS7)  and switch to something other than PHP, like Perl or CGI. Because so far, there is currently no workaround for this problem. At least I haven't found any yet.
* Stay with PHP and use a database to index the files and folders. This way we can use ISO-8859-1 certified characters for file/folder names, but the caveat is that we will have to access the database every time we need to access the file system. This can work, but since we're expecting multiple connections at any given time, it may create unnecessary load on the server. Additionally, in the event we need to manually access the file system, through Remote Desktop for example, it would just be impossible to tell which file/folder is what.
* Ditch Windows Server and use a Linux-based server like Ubuntu Server. It has everything we need for the application to work. But Linux is not exactly known for its simplicity and there's no point and click set up like in Windows Server. Setting up things like Web Server and FTP requires you to edit configuration files and such. Not the kind of thing the Japanese are known to love. The term "mendokusai", which basically means "pain in the arse", comes to mind.

Currently I'm exploring the first option, just because I'm sure the company doesn't want to switch to Linux. Converting PHP codes into C++ will take a while, but then again C++ does have certain advantages over PHP especially in the file system management department.
