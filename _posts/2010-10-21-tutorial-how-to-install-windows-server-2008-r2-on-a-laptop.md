---
layout: post
title: "Tutorial: How to install Windows Server 2008 R2 on a laptop"
date: 2010-10-21 17:30
comments: true
categories: [installation, laptop, Tutorials, Windows Server, windows server 2008 r2]
---
OK, so installing Windows Server 2008 on a laptop isn't such a big feat, but getting all the drivers working apparently is. I have a new Dell Latitude E6510 laptop and I wanted to install Windows Server 2008 R2 64-bit on it as the second operating system, mainly for work. The laptop originally came with Windows 7 Professional 64-bit and I figured the driver CD should provide all the required drivers for Windows Server 2008 as well. Unfortunately, that is not the case. Every time I try to install a driver, more often that not, I get an error telling me that either the said device isn't detected, or that the Windows version is wrong.

After spending some time googling for a solution, I came across a small guide that helped me solve the problem. However, this guide requires you to have a copy of Windows already installed on a separate partition. Since my Dell laptop already came with Windows 7 Professional, and I already installed Windows Server 2008 R2 on a separate partition, my laptop fitted the profile. So here's how to do it;

1. Open the Device Manager. You should see a bunch of devices with the yellow exclamation mark next to it. This tells us that the device is not working properly, most probably because the correct drivers are not yet installed.
2. Now right-click on a device and click "Update Driver Software..". Select "Browse my computer for driver software" and point the folder location to X:WindowsSystem32DriverStoreFileRepository while replacing the X with the drive letter of the partition on which your Windows 7 installation is located.
3. Repeat for the remaining devices.

You should be able to install all the drivers for your devices using this method. The only device that failed to install was my "Dell Wireless 375 Bluetooth Module with AMP" but other than that, everything else installed just fine, including my Smartcard reader.

[Reference](http://blogs.technet.com/b/roblarson/archive/2009/05/02/installing-windows-server-2008-r2-on-a-notebook-and-getting-all-those-required-drivers.aspx){:target="_blank"}

### DISCLAIMER ###

I created this tutorial mainly for my personal use. I am not responsible if something happens to your machines. If your machine/network/toaster/etc breaks down because you followed this guide, it is entirely your fault. If you point your finger at me, I will laugh at you.
