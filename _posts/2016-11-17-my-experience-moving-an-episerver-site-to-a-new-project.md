---
layout: post
title: My experience moving an EPiServer site to a new project
date: 2016-11-17 02:27

comments: true
categories: [ASP.NET, C#, EPiServer, episerver, episerver cms, site extraction, Web Development]
---
Every now and again we get sites that have grown so huge that it becomes necessary to extract certain parts of the site and convert them into separate sites. What was once a simple landing page had turned into a huge member-based system that involves stuff like integration with external service providers. True story.

Typically the simplest way to solve this little scenario is to simply move the section to the Root node of the EPiServer site, and assign it its own URL in the website configuration tool. But what if you wanted to extract that section out of the project altogether and recreate it as an entirely new project?

<!--more-->

One of our clients asked us to do exactly that. They wanted us to extract a whole section of their existing EPiServer website and recreate it as a new EPiServer CMS project, but at the same time keeping all existing content.

This post is a simple guide on how we achieved this.

<h2>Export data</h2>

Since we want to migrate content from the existing site to the new one, we start off by exporting all the data we need. This is easily done via the built-in export tool that comes pre-installed with all EPiServer sites located at <code>CMS &gt; Admin &gt; Tools &gt; Export data</code>.

<h2>Create a new database</h2>

Depending on how you set your new project up, you can either use the database that comes pre-installed by the EPiServer CMS extension, or you can manually create a new one.

We use SQL Server for all our sites, and the following steps are for this set-up. You should modify them to fit your needs.

<ol>
<li>Fire up SQL Management Studio, and log into the local, or remote, SQL Server instance.</li>
<li>Create a new empty database.</li>
<li>Assign a user and password. This step is optional, as you may also use Windows authentication.</li>
<li>Update the <code>connectionStrings.config</code> to point to this new database, and set the relevant credentials.</li>
<li>Open the NuGet Package Manager Console in Visual Studio, and type the following command: <code>Initialize-EpiDatabase</code></li>
<li>We now have a new EPiServer database.</li>
</ol>

<strong>Note</strong> When deploying the site to production, it's highly likely that you're not going to have access to a Visual Studio installation on the production machine. This means that you won't be able to run that cmdlet to initialize a new EPiServer database. To solve this, simply follow steps 1 through 4, and instead of running the cmdlet, add <code>createDatabaseSchema="true"</code> to the <code>&lt;episerver.framework&gt;</code> element of the project <code>episerverFramework.config</code> file. This will tell EPiServer to check whether the correct database schema exists, and to create it if it doesn't.

Sample episerver.framework configuration

<pre><code class="xml">&lt;episerver.framework createDatabaseSchema="true"&gt;
  ...
&lt;/episerver.framework&gt;
</code></pre>

<h2>Create new EPiServer CMS project</h2>

Next, create a new EPiServer CMS project using the EPiServer CMS Visual Studio Extension. This is the new project we'll be migrating to.

<h2>Move code to the new project</h2>

Now, we move all the code we need from the old project to this new one. This includes everything from classes, to controllers, to views, as well as other code like extension and helper classes. In short, we want to move everything we need to make the new site work.

In our project, we had everything we wanted to move located in an MVC Area, so it wasn't that difficult to move them to the new project.

<h2>Refactor everything - first run</h2>

Depending on the size of your project, this may or may not be the most tedious and time-consuming task in this list, as it involves going through the whole project and fixing things like namespaces, path references and so on.

<strong>IMPORTANT: Make sure to leave the content type GUID and class names exactly as it was in the original project</strong>, because this is what EPiServer will look for when it tries to map the data from the exported file to the database.

I made the mistake of renaming everything at this stage, and when I tried to import the data I've previously extracted from the old site, I ran into some import errors which pretty much broke everything. Once I reverted the class names and reset the database, I re-ran the import and only then was I able to successfully restore the data to the new site.

<h2>Build the project</h2>

Build the project and make sure everything is OK.

<h2>Import data</h2>

Once you get everything working, and you're able to access the CMS UI, you can import the data from your old site to the new one.

Navigate to <code>CMS &gt; Admin</code>, and in the <code>Admin</code> tab, under <code>Tools</code>, select <code>Import data</code>, and upload the file you got when you exported the data from the old site.

<h2>Refactor everything - second run</h2>

This part is entirely optional. In our project all the campaign pages had a <code>Campaign</code> prefix to the content type class names, and we wanted to clean them up by removing them in the new project.

Once you have successfully imported the data from the old site, you're free to rename your content types, provided you retain the content type GUID.

This guide, like every other guide I've posted, is mostly a note-to-self, but if it helped you, do let me know.

<em>Wassalam</em>
