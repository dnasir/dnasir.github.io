---
layout: post
title: Implementing Web API in your NuGet module
date: 2012-08-07 10:30
comments: true
categories: [ASP.NET, asp.net, C#, c#, csharp, nuget, nuget module, web api, Web Development, webactivator]
---
I'm currently working on a small NuGet module for EPiServer CMS, and I thought it'd be cool to implement a RESTful API using the ASP.NET Web API. However, as you probably already know, you need to make certain adjustments to the Global.asax file to set up some routing so that any requests made is redirected to the appropriate controllers.

This is fine if you're implementing Web API into your main project, but not if you're trying to get it working in a 3rd party module. You really don't want anyone who installs your module to have to unnecessarily edit anything on their end. It's just bad design.

<!--more-->

Whenever someone installs your module into their project, it should just work. Granted, there are instances where your users are required to make minor changes to their project files to get certain features to work. But your module should have an out-of-the-box experience that just works without additional set-up.

Similarly, your module shouldn't have to edit your project's Global.asax file to register its own Web API routes. I don't know if that's even possible, but then again I'm still an ASP.NET noob. In any case, we're kinda stuck. We need the module to register its Web API routes, but we don't want to have the users manually edit the Global.asax file.

Enter [WebActivator](http://nuget.org/packages/WebActivator "WebActivator NuGet page"){:target="_blank"}. This module allows you to run start up codes in your web applications. You can even choose to run your start up code before the application starts, or after. Pretty cool, huh?

So on to the code. The following code will allow your NuGet module to register its Web API routes during application start up. Much like how Global.asax works.

```csharp
[assembly: PostApplicationStartMethod(typeof (StartUp), "PostApplicationStart")]
namespace MyModule
{
    public static class StartUp
    {
        public static void PostApplicationStart()
        {
            RouteTable.Routes.Insert(0,
                                     new Route("api/{controller}/{action}/{id}",
                                               new RouteValueDictionary(new {id = RouteParameter.Optional}),
                                               HttpControllerRouteHandler.Instance));
        }
    }
}
```

That's it. That's all it takes to get Web API working in your NuGet module.

It's worth to mention though, that you should set the appropriate path for your route. You could use something like mymodule/api as the root path, so you know that requests made to this path are routed to your controller. It helps to avoid any potential conflicts.

I hope this helps someone wanting to do something similar. It's been an interesting learning experience for an ASP.NET noob such as myself.

<em>Wassalam.</em>
