---
layout: post
title: "Tutorial: How to install PEAR on IIS7"
date: 2010-12-02 15:44
comments: true
categories: [iis, installation, Internet Information Services (IIS), pear, PHP, php, Programming, Syntax error, Tutorials, "unexpected '(' in Unknown", Windows Server, windows server]
---
I was looking around for some libraries to use in my server-side scripts and it led me to reading up on PEAR. So I decided to have a look, and install it on my Windows Server 2008 R2 server.

I am assuming that you have properly installed PHP, and that you are using the latest version of PHP. At the time of writing, the latest PHP version is 5.3.3. You can read up on the whole installation process [here](/2010/11/22/tutorial-how-to-install-php-on-iis/){:target="_blank"}.

<!--more-->

Once  you have installed PHP, navigate to the folder in which PHP is installed, and look for go-pear.bat. Run this batch program in shell, taking care in selecting the options for your installation. Depending on your needs, you may or may not have to change any of the default values. In my case, there was no need to change any of the default values. Several Enters later you would have completed the PEAR installation.

![Image of PEAR installation]({{ "/assets/img/2011/08/pear-install.png" | absolute_url }})

I ran into some problems while trying to get PEAR working. Firstly, I kept getting an error telling me that there was a syntax error.

![Image of PEAR runtime error]({{ "/assets/img/2011/08/pear-run-error2.png" | absolute_url }} "PHP Error")

```shell
Syntax error, unexpected '(' in Unknown on line 14.
```

This usually means it's a script error, and it's usually a simple "misunderstanding" where PHP "incorrectly" processes a line.

Fortunately, I found a simple solution to this problem. Go to the directory in which PHP is installed and look for a file called pear.bat. Open it up in any text editor, I'm using Notepad++, and look for the following line at the very bottom of the batch script;

```shell
"%PHP_PEAR_PHP_BIN%" -C -d output_buffering=1 -d safe_mode=0 -d open_basedir="" -d auto_prepend_file="" -d auto_append_file="" -d variables_order=EGPCS -d register_argc_argv="On" -d include_path="%PHP_PEAR_INSTALL_DIR%" -f "%PHP_PEAR_INSTALL_DIR%pearcmd.php" -- %1 %2 %3 %4 %5 %6 %7 %8 %9
```

Now, scroll all the way to the end of that line and look for this;

```
include_path="%PHP_PEAR_INSTALL_DIR%"
```

Now, edit this line so that it looks like this;

```
"include_path='%PHP_PEAR_INSTALL_DIR%'"
```

Notice the single and double quotes. The double quotes keeps shell happy, while the single quotes are for PHP to use. This simple trick helped me fix one problem.

The next problem was that whenever I try running pear in shell, I was getting this error telling me that OCI.dll is missing.

![Image of PEAR runtime error]({{ "/assets/img/2011/08/pear-run-error1.png" | absolute_url }} "php.exe - System Error")

```
The program can't start because OCI.dll is missing from your computer. Try reinstalling the program to fix this problem.
```

I figured it's a simple problem with PHP trying to call an extension that doesn't exist. So what I did was open up php.ini in Notepad++ and disabled the following extensions;

```
extension=php_oci8.dll
extension=php_oci8_11g.dll
extension=php_pdo_oci.dll
```

That solved another problem. I don't know what OCI is, but I hope it's not needed for any of the work I need PHP to do.

Finally, the last problem is that shell was spewing out a bunch of errors telling me that PHP couldn't find several modules, as you can see in the image below.

![Image of PEAR runtime error]({{ "/assets/img/2011/08/pear-run-error3.png" | absolute_url }} "Missing modules error")

It turned out that quoting the following line helped solve this problem.

```
extension=php_snmp.dll
```

Again, PHP was attempting to call out modules that don't exist. I don't have SNMP installed on my IIS server because I don't think I need the SNMP feature for any of my current projects. So it should be OK to disable it. Besides, I haven't had any problems so far. I believe this also applies to OCI. Maybe I should just disable all the modules I don't need, instead of leaving them enabled. But due to my limited knowledge of PHP, I don't really know which modules actually matter.

Anyway, I hope this tutorial has helped setting up PEAR on your IIS server. If you have any suggestions and comments, please leave a message. It's nice to learn something new every day.

Til next time, wassalam.

References:
[http://pear.php.net/](http://pear.php.net){:target="_blank"}
[http://www.pear-forum.org](http://www.pear-forum.org){:target="_blank"}

### DISCLAIMER ###

I created this tutorial mainly for my personal use. I am not responsible if something happens to your machines. If your machine/network/toaster/etc breaks down because you followed this guide, it is entirely your fault. If you point your finger at me, I will laugh at you.
