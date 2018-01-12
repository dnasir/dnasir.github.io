---
layout: post
title: Using RequireJS in an ASP.NET project
date: 2012-08-23 16:35
comments: true
categories: [ASP.NET, asp.net, JavaScript, requirejs, Web Development]
---
<em>Salam,</em>

As the number of JavaScript plugins in my projects grow, I began thinking about how I was going to manage dependencies, especially since we're going with the whole "JS files should go at the bottom of the page" approach.

Unfortunately, it hasn't been that simple. Some of the projects I'm currently working on uses partial views that inject parameter values into the JS function that runs in those partial views. So if I were to include for instance, jQuery, at the bottom of the page, some of these functions may not work because at the moment the browser processes the function, jQuery is not yet defined. So the script crashes. This will be true for all functions that rely on jQuery to be defined on page load.

Enter <a title="RequireJS" href="http://requirejs.org/" target="_blank">RequireJS</a>.
