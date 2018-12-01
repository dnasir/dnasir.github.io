---
layout: post
title: "RequireJS: Manage caching using ASP.NET assembly version"
date: 2013-01-31 18:48
comments: true
categories: [ASP.NET, asp.net, C#, caching, JavaScript, requirejs, Web Development]
---
RequireJS is a great library for dynamically loading JavaScript modules. It's great in projects where you want to control what gets loaded where, and only load files that are essential to the page currently being displayed. On top of that, it has an awesome dependency control system. All you have to do is define what your module depends on, and it will automatically and dynamically load up all the necessary modules along with their dependencies, provided they're defined.

However, the problem with dynamically loading JavaScript files is that they have a tendency to get cached. While this is good for your visitors, because you don't want them to have to repeatedly download the same JavaScript files, the problem is when you update your files.

<!--more-->

If you have your web server set up so that it tells browsers to cache JavaScript files for a certain amount of time, unless your users know how to clear their caches, they will continue using the cached version of said files.

Fortunately, RequireJS has a parameter called [urlArgs](http://requirejs.org/docs/api.html#config-urlArgs "urlArgs"){:target="_blank"} that you can use to append arguments to your script requests. The RequireJS documentation also states that this parameter can also be used to set some cache-busting values, such as the current time, to every script request. This will force the browser to re-download all required files on every request.

But doesn't that mean your JavaScripts won't ever be cached? Depends. If you use a non-static value, like the time at which the request was being made, then the value will always differ on every request. But if you used a static value instead, then all this becomes pointless, because the browser will detect that nothing has changed and continues to use the cached files instead.

So how can we leverage browser caching, while at the same time force the browser to re-download our JavaScript files when we want it to?

The solution I came up with is pretty simple. Use the project's assembly version number as the urlArgs parameter value. Here's how.

Add the following in your AssemblyInfo.cs file, if it's not already present.

```csharp
[assembly: AssemblyVersion("1.0.*")]
```

Then all you have to do is fetch this value, and use it as your urlArgs parameter value.

```javascript
<script type="text/javascript">
    var require = {
        urlArgs:'build=<%= System.Reflection.Assembly.GetExecutingAssembly().GetName().Version.ToString() %>'
    };
</script>
<script type="text/javascript" src="/scripts/require.js" data-main="app/main"></script>
```

So every time you rebuild your project, it will cause the assembly version number to change. And that's all the browser requires to force it to re-download all the files loaded via RequireJS. Pretty neat, huh.

This solution is ideal for ASP.NET projects mainly because it generates a different assembly version number on every build, but I'm sure there are ways to getting it working in other types of projects as well.

*Wassalam*
