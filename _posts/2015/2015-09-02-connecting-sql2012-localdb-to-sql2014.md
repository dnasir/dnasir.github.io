---
layout: post
title: Connecting SQL2012 LocalDB to SQL2014
date: 2015-09-02 21:02

comments: true
categories: [breaking changes, localdb, sql server, Web Development]
---
New OS means fresh install, which is exactly what I did. I installed the latest Windows OS, along with Visual Studio 2015 and SQL Server 2014. Then I tried opening one of my existing projects running on LocalDB, only to be greeted with this error.

![sql-2012-not-installed-error]({{ "/assets/img/2015/09/sql-2012-not-installed-error.png" | absolute_url }})

<!--more-->

So I did what Visual Studio asked me to, but that didn't seem to do much. Instead I got another error:

![sql-instance-not-installed]({{ "/assets/img/2015/09/sql-instance-not-installed.png" | absolute_url }})

I thought that updating the connection string from `v11.0` to `v12.0` would solve my problem, but it didn't.

After a couple of hours Googling, I finally came across this little gem:

According to [Microsoft](https://connect.microsoft.com/SQLServer/feedback/details/845278/sql-server-2014-express-localdb-does-not-create-automatic-instance-v12-0){:target="_blank"}

> In SQL14, we moved away from the numbering/versioning for the automatic instance and named it "MSSQLLocalDB" instead of "v12.0".

And just like that, everything started working again.

This post is more of a note-to-self than anything else, but I do hope someone finds it useful.

*Wassalam*
