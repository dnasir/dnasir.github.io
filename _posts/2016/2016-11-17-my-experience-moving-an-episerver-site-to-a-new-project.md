---
layout: post
title: My experience moving an EPiServer site to a new project
date: 2016-11-17 02:27
comments: true
categories: [ASP.NET, C#, EPiServer, episerver, episerver cms, site extraction, Web Development]
tags: [episerver, episerver cms, c#, asp.net]
---
Every now and again we get sites that have grown so huge that it becomes necessary to extract certain parts of the site and convert them into separate sites. What was once a simple landing page had turned into a huge member-based system that involves stuff like integration with external service providers. True story.

Typically the simplest way to solve this little scenario is to simply move the section to the Root node of the EPiServer site, and assign it its own URL in the website configuration tool. But what if you wanted to extract that section out of the project altogether and recreate it as an entirely new project?

<!--more-->

One of our clients asked us to do exactly that. They wanted us to extract a whole section of their existing EPiServer website and recreate it as a new EPiServer CMS project, but at the same time keeping all existing content.

This post is a simple guide on how we achieved this.

## Export data

Since we want to migrate content from the existing site to the new one, we start off by exporting all the data we need. This is easily done via the built-in export tool that comes pre-installed with all EPiServer sites located at `CMS > Admin > Tools > Export data`.

## Create a new database

Depending on how you set your new project up, you can either use the database that comes pre-installed by the EPiServer CMS extension, or you can manually create a new one.

We use SQL Server for all our sites, and the following steps are for this set-up. You should modify them to fit your needs.

1. Fire up SQL Management Studio, and log into the local, or remote, SQL Server instance.
2. Create a new empty database.
3. Assign a user and password. This step is optional, as you may also use Windows authentication.
4. Update the `connectionStrings.config` to point to this new database, and set the relevant credentials.
5. Open the NuGet Package Manager Console in Visual Studio, and type the following command: `Initialize-EpiDatabase`.
6. We now have a new EPiServer database.

**Note** When deploying the site to production, it's highly likely that you're not going to have access to a Visual Studio installation on the production machine. This means that you won't be able to run that cmdlet to initialize a new EPiServer database. To solve this, simply follow steps 1 through 4, and instead of running the cmdlet, add `createDatabaseSchema="true"` to the `<episerver.framework>` element of the project `episerverFramework.config` file. This will tell EPiServer to check whether the correct database schema exists, and to create it if it doesn't.

Sample episerver.framework configuration

```xml
<episerver.framework createDatabaseSchema="true">
  ...
</episerver.framework>
```

## Create new EPiServer CMS project

Next, create a new EPiServer CMS project using the EPiServer CMS Visual Studio Extension. This is the new project we'll be migrating to.

## Move code to the new project

Now, we move all the code we need from the old project to this new one. This includes everything from classes, to controllers, to views, as well as other code like extension and helper classes. In short, we want to move everything we need to make the new site work.

In our project, we had everything we wanted to move located in an MVC Area, so it wasn't that difficult to move them to the new project.

## Refactor everything - first run

Depending on the size of your project, this may or may not be the most tedious and time-consuming task in this list, as it involves going through the whole project and fixing things like namespaces, path references and so on.

**IMPORTANT: Make sure to leave the content type GUID and class names exactly as it was in the original project**, because this is what EPiServer will look for when it tries to map the data from the exported file to the database.

I made the mistake of renaming everything at this stage, and when I tried to import the data I've previously extracted from the old site, I ran into some import errors which pretty much broke everything. Once I reverted the class names and reset the database, I re-ran the import and only then was I able to successfully restore the data to the new site.

## Build the project

Build the project and make sure everything is OK.

## Import data

Once you get everything working, and you're able to access the CMS UI, you can import the data from your old site to the new one.

Navigate to `CMS > Admin`, and in the `Admin` tab, under `Tools`, select `Import data`, and upload the file you got when you exported the data from the old site.

## Refactor everything - second run

This part is entirely optional. In our project all the campaign pages had a `Campaign` prefix to the content type class names, and we wanted to clean them up by removing them in the new project.

Once you have successfully imported the data from the old site, you're free to rename your content types, provided you retain the content type GUID.

This guide, like every other guide I've posted, is mostly a note-to-self, but if it helped you, do let me know.

*Wassalam*
