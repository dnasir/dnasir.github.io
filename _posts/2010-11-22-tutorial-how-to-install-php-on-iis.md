---
layout: post
title: "Tutorial: How to install PHP on IIS"
date: 2010-11-22 15:39
comments: true
categories: [iis 7, installation, Internet Information Services (IIS), PHP, php, Programming, Tutorials, Windows Server, windows server]
---
Recently I've been assigned to work with servers for a new project that started last month. My supervisor told me to document all the stuff I did so that in the event they need to train a new server administrator in the future, he/she can refer to my documentations and we can get things done quicker. I decided it would be easier for everyone if I made the documentations available on my Wordpress blog, instead of some Word file saved somewhere on the company's network. At least this way, nothing would get lost.

<!--more-->

So here's how to set up PHP on IIS. At the time of writing, the server is running Windows Server 2008 R2 Enterprise which came pre-installed with IIS 7.5.
<ol>
	<li>Make sure you have the Internet Information Service (IIS) installed and configured properly. This means you have to be able to see the IIS Welcome page when you access the server via web browser.</li>
	<li>Make sure you have CGI installed and configured properly. I would just have all the stuff under Application Development install</li>
	<li>Download the PHP installer from <a href="http://windows.php.net/download/">http://windows.php.net/download/</a>. At the time of writing, the latest PHP version available is 5.3.3, so I cannot guarantee this guide will work with newer versions.</li>
	<li>Run the installer.
<ul>
	<li>Set the install path to something easy to remember/use (eg. c:php)</li>
	<li>Select the modules you need (eg. mysql, etc)</li>
	<li>Selecting Script Executable &gt; Register *.php file to open automatically with PHP means you don't have to set this manually.</li>
</ul>
</li>
	<li>Click Next until you're done.</li>
	<li>Open php.ini and unquote/edit the following. I'd recommend using Notepad++ for this just in case Notepad decides to mess up your file;
<ul>
	<li>fastcgi.impersonate=1</li>
	<li>cgi.fix_pathinfo=1</li>
	<li>open_basedir = C:inetpubwwwroot (This depends on where your www root is). This is optional. It will restrict all PHP functions to the root folder and below, so you will not be able to access say, C:some-storage-folder, if this line is enabled. So, if you're working with remote storage locations, you might want to disable this line.</li>
</ul>
</li>
	<li>If you didn't select "Script Executable &gt; Register *.php file to open automatically with PHP" during the installation, here's what you have to do to get IIS to automatically process PHP scripts. Note that if you skip this step, your PHP scripts will not work.;
<ol>
	<li>Open the IIS Manager.</li>
	<li>Open Handler Mapping.</li>
	<li>Click on Add New Module Map.</li>
	<li>Insert the following;
<ul>
	<li>Request Path: *.php</li>
	<li>Module: FastCgiModule</li>
	<li>Executable: C:phpphp-cgi.exe (This depends on where you install PHP)</li>
	<li>Name: PHP via FastCGI (or anything you want)</li>
</ul>
</li>
</ol>
</li>
	<li>Restart IIS.</li>
	<li>Test PHP by putting a PHP file in the wwwroot folder and accessing it via a browser. I just created a file called phpinfo.php and wrote the following lines into the file.</li>
</ol>

```php
<?php
   echo phpinfo();
?>
```

That's it. That's all you have to do to get PHP working on IIS. As much as I am used to working with LAMP servers, I think Windows Server may not be so bad after all. Have fun.

Til next time, wassalam.

### DISCLAIMER ###

I created this tutorial mainly for my personal use. I am not responsible if something happens to your machines. If your machine/network/toaster/etc breaks down because you followed this guide, it is entirely your fault. If you point your finger at me, I will laugh at you.
