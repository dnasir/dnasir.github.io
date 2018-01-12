---
layout: post
title: Angular Date Parser
date: 2013-12-25 16:08

comments: true
categories: [AngularJS, angularjs, dateparser, directive, JavaScript, Web Development]
---
Lately I've been working a lot with date and time pickers, and it got me thinking, "Why doesn't Angular come with some form of date and time parser that converts strings into a JavaScript Date object." I mean, you could already convert Date objects into formatted strings using the Angular dateFilter, so why not the other way around? Wouldn't it be cool to be able to convert strings into Date objects using a particular format, and in many cases for us in Europe, locale.

The JavaScript Date constructor accept strings and it tries its best to parse it, but it usually fails when given a format other than the RFC2822 or ISO 8601 date formats. So if you're in Norway, for instance, entering '25.12.2013' is going to generate an Invalid Date object.

So my parser will allow you to enter a string, provide a format, and output a valid Date object. You can use all of the date format strings provided by Angular on the dateFilter documentation page.

<a href="https://github.com/dnasir/angular-dateParser" title="Angular Date Parser" target="_blank">Project Page</a>

<a href="http://dnasir.github.io/angular-dateParser/demo.html" title="Angular Date Parser Demo" target="_blank">Demo</a>

It’s free to use, so feel free to download it, use it, even abuse it, as long as you keep the copyright info intact.

The parser algorithm was originally created by <a href="http://www.mattkruse.com" target="_blank">Matt Kruse</a>, so kudos to him for his awesome work. You can view his code <a href="http://www.mattkruse.com/javascript/date" target="_blank">here</a>.

<em>Wassalam</em>
