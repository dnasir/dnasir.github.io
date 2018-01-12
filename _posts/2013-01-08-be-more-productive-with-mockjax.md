---
layout: post
title: Be more productive with Mockjax
date: 2013-01-08 18:05
comments: true
categories: [JavaScript, jquery, jQuery, mockjax, Web Development, web service simulation]
---
It's common knowledge that when working in a group we always break the tasks down into smaller portions so as not to choke anyone with too much work. It's also common for most people working in groups to try and get things done in parallel.

I work mostly with front-end stuff, while my team-mates work on the back-end stuff. Once we get our assigned tasks done, we get together and spend some time getting everyone's code to play nice with each other.

But sometimes, due to unforeseen problems, the tasks don't get done in time, and one side will need to wait for the other side to get their code up and running. I have, on occasions, had to wait for the back-end code to be up and running before I can test anything, and obviously there are times when it's the opposite.

I work with a lot of Ajax stuff, and this means sending data to web services, getting a response, and reacting based on said response. Pretty standard stuff.

I don't usually work on the back-end code for the web services that my Ajax modules are dependent upon. The other people in my team are more suited for the task. But even so, there are times when we just have to spend that extra hour or two getting a web service ready for testing, and I get stuck with one less task. In some cases, the absence of a particular web service becomes a major show stopper.

So it makes sense then, to find a way to be able to keep working even if the web services are not yet ready.

Enter <a href="https://github.com/appendto/jquery-mockjax">Mockjax</a>, a jQuery plugin that allows you to simulate an Ajax response without the need of external web services.

You can set up a client-side "web service" to generate the response you're expecting from the yet-to-be-completed web service, while you continue working on your Ajax module. You can set it up to return strings, HTML, XML, JSON, etc - pretty much anything you can return via a real web service, you can do it here. You can also set how long it takes before the responses arrive, simulating fast/slow internet connections. You can even set it up to return a request time-out, if you need to test your code for that.

The best part is, once the real web service is ready, you only need to remove the Mockjax code to start using it. That's right. You can already start using the URL pointing to the currently incomplete web service and Mockjax will intercept all requests made to this URL and return your predefined responses.

This is one of those plugins that I wish I had found sooner.

<em>Wassalam</em>
