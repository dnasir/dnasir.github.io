---
layout: post
title: "Migrating from WebpackDevMiddleware to SpaServices.Extensions"
description: "A quick guide on migrating the front-end build process when upgrading to .Net Core 3.x."
comments: true
tags:
  - webpackdevmiddleware
  - spaservices
  - netcore
  - vue
---

Upgrading to .Net Core 3.x resulted in some breaking changes for my front-end development process, in particular the Hot Module Reloading (HMR) feature that I use in all my projects.

If you use the `WebpackDevMiddleware`, it's likely you would have seen this message pop up after upgrading to .Net Core 3.

![Sad day]({{ "/assets/img/2020/webpackdevmiddleware-obsolete.png" | absolute_url }}){: .center-image }

So, what do we do now?

<!--more-->

The good news is, Microsoft has implemented support for Angular and React, in the form of `AngularCliMiddleware` and `ReactDevelopmentServerMiddleware` middlewares, in their `SpaServices.Extensions` package. The bad news is, Vue developers like me are left out in the cold.

Fortunately, it's not the end of the world for us, and a quick look at the [source code for the `ReactDevelopmentServerMiddleware` middleware][0] shows that it's basically just a wrapper that starts an NPM task via `NodeScriptRunner`, attaches it to the middleware pipeline, and creates a proxy that allows HMR to work.

Once we understand this, we can repurpose the code to work with the Vue CLI.

I came across a [Gist][1] created by a GitHub user by the name of Alexey Zimarev that does exactly this, as I was writing my own solution. It goes to show that you should definitely Google before you start writing your own code ;)

Once you have added the code to your project, you can proceed to use it in your `Startup.cs`.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // all your other startup code

        app.UseSpa(spaBuilder =>
        {
            if (_env.IsDevelopment())
            {
                spaBuilder.Options.SourcePath = Path.Combine(_env.ContentRootPath, "src");
                spaBuilder.UseVueDevelopmentServer("dev");
            }
        });
    }
}
```

Run the app, and HMR should be back in business.

I hope this helps anyone else running into this problem.

[0]: https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/SpaServices.Extensions/src/ReactDevelopmentServer/ReactDevelopmentServerMiddleware.cs
[1]: https://gist.github.com/alexeyzimarev/f0262426aa38e2c1ed2913252ceb5e7a