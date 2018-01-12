---
layout: post
title: "Tutorial: How to enable PHP5's imageantialias on Ubuntu 10.10"
date: 2011-04-23 00:08
comments: true
categories: [imageantialias, Linux, PHP, php, Programming, Tutorials, ubuntu, ubuntu 10.10]
---
I am currently working on a small project that requires me to use Apache on a Linux server. Since I am more accustomed to Ubuntu, I decided to install Ubuntu 10.10 on one of my virtual machines. So I installed everything I needed like Apache, MySQL and PHP5. Unfortunately, when I ran my PHP script, I hit a small problem. I kept getting an error 500 and it turned out that apparently the function imageantialias was not available in the PHP installation on my server.

After a bit of research, it turned out that the GD package used in Ubuntu is different to that used in other Linux distros. I looked around for a viable solution and finally discovered that the best way to overcome this problem was to compile PHP5 from source. Here's how I did it.<!--more-->

Fire up your Ubuntu box, and open up a terminal if you're on a desktop distro.

Sudo yourself, and install some important stuff.
<pre>sudo -i
apt-get install build-essential debhelper fakeroot</pre>
Remove the php5-gd package that is installed on your system, if you haven't already done so.
<pre>apt-get remove php5-gd</pre>
Now, navigate to the source directory.
<pre>cd /usr/src/</pre>
Download the PHP5 source.
<pre>apt-get source php5</pre>
Install all the packages required to build PHP5, and then navigate into the PHP5 directory. The directory name may vary depending on the PHP version you're attempting to build.
<pre>apt-get build-dep php5
cd php5-5.3.3/</pre>
Edit "debian/rules", and replace the following line;
<pre>--with-gd=shared,/usr --enable-gd-native-ttf 
with
--with-gd=shared --enable-gd-native-ttf </pre>
This essentially tells the compiler to use the bundled version of GD and make a shared library. Now, the tutorials I have thus far discovered tells me to now configure the package and build. However, I've encountered a problem during the compilation process where the compiler tries to use the MySQL server to run some tests, and for some reason it keeps timing out trying to start its own process. I kept getting the following error;
<pre># start our own mysql server for the tests
/bin/sh debian/setup-mysql.sh 1025 /usr/src/php5-5.3.3/mysql_db
Timed out waiting for mysql server to be available
kill: 76: No such process

kill: 76: No such process

make: *** [test-results.txt] Error 1
dpkg-buildpackage: error debian/rules build gave error exit status 2</pre>
I looked into the setup-mysql.sh script located in the debian directory, and after a bit of tinkering and a lot of googling, I discovered a simple solution. All I had to do was edit the following line;
<pre># Start the daemon
$mysqld &gt; $datadir/run.log 2&gt;&amp;1 &amp;
to
$mysqld --user=root &gt; $datadir/run.log 2&gt;&amp;1 &amp;</pre>
This tells the script to run as the root user, thus giving it the permission it needs to do stuff. This problem didn't seem to be common as I did not come across anyone who has had this problem. I'm not sure why it occurred on my machine seeing as how I have a perfectly clean install of Ubuntu, but it seems that editing the above line helped, and I could then compile using;
<pre>dpkg-buildpackage -rfakeroot -uc -b</pre>
After the compilation is complete, navigate one directory up, and install the new php5-gd package you just built using;
<pre>cd ..
dpkg -i php5-gd_5.3.3-1ubuntu9.3_i386.deb</pre>
You can check whether the package was properly installed using the following command;
<pre>php -r "var_dump(function_exists('imageantialias'));"
bool(true)</pre>
That's it. I hope this helps anyone facing the same problem. I also wrote this as a reference for myself, in case I ever forget how to fix this problem.

Til next time, wassalam.
