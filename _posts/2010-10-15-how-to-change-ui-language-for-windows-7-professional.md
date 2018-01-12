---
layout: post
title: "Tutorial: How to change UI language for Windows 7 Professional"
date: 2010-10-15 16:01
comments: true
categories: [change ui language, Tutorials, Windows 7, windows 7, windows 7 professional]
---
By default, the ability to change the UI language is only found in the Ultimate version of Windows 7. Those with the misfortune of buying a PC preinstalled with Windows 7 Professional, me included, did not have this ability. I decided that I had to replace the Japanese UI with an English one, and the only way was to hack it. Here's how I did it;

Download the language pack that you want to install from [here](http://www.vista123.net/content/download-windows-7-mui-language-packs-official-32-bit-and-64-bit-direct-download-links){:target="_blank"}

Run CMD as administrator and type:
```
DISM /Online /Add-Package /PackagePath:[path to lp.cab];
```

eg. `C:\DISM /Online /Add-Package /PackagePath:C:DocumentsDownloadslp.cab`

then:
```
bcdedit /set {current} locale (your new locale)
```
eg. `C:\bcdedit /set {current} locale en-US`

and:
```
bcdboot %WinDir% /l (your new locale)
```
eg. `C:\bcdboot %WinDir% /l en-US`

Then in registry:

`HKEY_LOCAL_MACHINESYSTEMCurrentControlSetControlMUIUILanguages`

remove the key of the current language (in my case, jp-JP)

Reboot and you should boot up in the language of your choice.

[Reference](http://www.winmatrix.com/forums/index.php?/topic/25539-how-to-enable-mui-language-pack-in-windows-7-professional/){:target="_blank"}

### DISCLAIMER ###

I created this tutorial mainly for my personal use. I am not responsible if something happens to your machines. If your machine/network/toaster/etc breaks down because you followed this guide, it is entirely your fault. If you point your finger at me, I will laugh at you.
