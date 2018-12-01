---
layout: post
title: "Tutorial: How to set up MySQL on IIS"
date: 2010-11-25 09:57
comments: true
categories: [iis 7, installation, Internet Information Services (IIS), MySQL, mysql, Windows Server, windows server]
---
This is the next tutorial in my line of IIS Tutorials for work. Unfortunately, there's a large possibility the company might not use MySQL, and use MS SQL Server 2008 instead. Well, I've already installed MySQL on one of the virtual IIS servers, so here's how I did it just in case they decide to use MySQL for other projects.

<!--more-->

1. Download MySQL installer from [http://dev.mysql.com/downloads/mysql/](http://dev.mysql.com/downloads/mysql/){:target="_blank"}
2. Run the installer.
	* Select Custom Install and set the install path to something easy to remember/use (eg. c:mysql). This really does not matter because it's not that difficult to find where you installed MySQL anyway.
	* Select the modules you require. This depends on what you need. Personally, I would just install everything.
3. Click Next until the installation is done. Leave Configure the MySQL Server now checked and go through the Detailed Configuration.
4. Select Server Machine as server type, and Multifunctional Database usage. You can also set this according to your needs.
5. Leave everything else on default until Windows Option, then check Include BIN directory in Windows Path.
6. Set the root password, and click Finish.
7. To set PHP5 to access MySQL server,
	1. Open the php.ini
	2. Uncomment the following;
		* `extension=php_mysqli.dll`
		* `extension=php_mbstring.dll`
		* `extension=php_mcrypt.dll`
8. Finally, Restart the IIS service to apply changes.

That's it. It's actually easier than the Linux people say it is, me included.

Til next time, wassalam.

### DISCLAIMER ###

I created this tutorial mainly for my personal use. I am not responsible if something happens to your machines. If your machine/network/toaster/etc breaks down because you followed this guide, it is entirely your fault. If you point your finger at me, I will laugh at you.
