---
layout: post
title: "Tutorials: Changing the maximum upload size for IIS 7.5"
date: 2011-01-21 18:44
comments: true
categories: [IIS 7.5, Internet Information Services (IIS), modify maximum upload size, Tutorials, Windows Server, windows server]
---
I hit a snag when uploading large files using PHP on IIS 7.5. I was getting some weird Error 404, which in most cases means the PHP script that handles the upload process was not found. I checked my server, and sure enough, the PHP script was there. I tried again to be sure, this time with a smaller file. And what do you know, it worked just fine.

This led me to believe that there was a file size restriction imposed somewhere. I've already modified the php.ini file to allow me to upload larger files, but still no go. So the only other thing I can think of was IIS. Apache has something like that, so it's only obvious that the ever-so-cautious IIS would be the same.

After much research, and a couple of Google searches later, I found a simple solution involving some simple config file edits. Damn, after working with IIS for the past 6 months, I got used to not having to deal with config files. Anyway, the file you want to modify is "C:WindowsSystem32inetsrvconfigapplicationHost.config". I don't know if it makes any difference, but I've converted the folders that hold my PHP scripts into Applications through the IIS Manager.

So open that file up using any text editor. It was a newly installed virtual server, so I didn't have anything other than Notepad available.<!--more-->

Add the following lines to the bottom of the file, making sure it's inside the &lt;configuration&gt; area.
<pre>&lt;location path="Default Web Site/alnet"&gt;
   &lt;system.webServer&gt;
      &lt;security&gt;
         &lt;requestFiltering&gt;
            &lt;requestLimits maxAllowedContentLength="2147483648" /&gt;
         &lt;/requestFiltering&gt;
      &lt;/security&gt;
   &lt;/system.webServer&gt;
&lt;/location&gt;</pre>
Where "Default Web Site" is the name of your website, "alnet" is the name of the application, and "2147483648" is the amount you want to set for the upload file limit in bytes. So as you can see, I've set my IIS to accept file sizes up to 2GB.

You can add as many of this as you need. I have 3 applications, so I have three of these in my config XML script.

You can also use the following line at a command prompt. Be sure to navigate to "C:WindowsSystem32inetsrv" first.
<pre>appcmd set config "Default Web Site/alnet" -section:requestFiltering
-requestLimits.maxAllowedContentLength:2147483648-commitpath:apphost</pre>
Where "Default Web Site" is your website and "alnet" is the name of your application.

That's it. Have fun and if you know of a better way to achieve the same result, let me know using the comment form.

Until next time, Wassalam.

EDIT:

An easier way to go about this, as I've recently discovered, is to use the IIS Manager. I know, I know. This is by far the best way to go about this, but I don't really have that much experience with Windows Server and I'm so used to the Linux way of editing configuration files that I didn't think to use the IIS Manager for this. Anyway, here's how to change the file upload limit using the IIS Manager.

Open up the Server Manager and navigate to the IIS Manager. Click on your Application icon and turn your attention to the center window. Scroll down to the bottom and you will find the Configuration Editor under Management. Double-click on this and you will be presented with a bunch of settings.

Open up the drop down menu and navigate through the following; system.webServer &gt; security &gt; requestFiltering. Click on that and it will show you the current requestFiltering settings for your Application. Scroll down and until you find requestLimits. Expand it and there you should see maxAllowedContentLength. You can now change this value to any value you wish to set. Once you're done, click Apply and that's it.

Here's a picture showing you where and what to change. At this moment, I only have the Japanese version of Windows Server available to take screenshots from, but I'm sure you get the idea.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/08/28.png"><img class="size-full wp-image-778 aligncenter" title="28" src="http://subject9.files.wordpress.com/2011/08/28.png" alt="" width="578" height="379" /></a></p>
