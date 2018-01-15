---
layout: post
title: Angular Timepicker Directive
date: 2013-12-06 00:31

comments: true
categories: [AngularJS, angularjs, dropdown, JavaScript, timepicker, Web Development]
---
I was searching for a simple timepicker directive, much like the one used in Google Calendar, for one of my projects. I couldn't find any, so I built my own.

<!--more-->

<a href="https://github.com/dnasir/angular-timepicker" title="Angular Timepicker" target="_blank">Angular Timepicker</a>

<a href="/github/angular-timepicker/demo.html" title="Angular Timepicker Demo" target="_blank">Demo</a>

This directive creates a simple dropdown style timepicker, like the one used in Google Calendar. You can set the minimum and maximum time for the list of selectable time, as well as the format in which time is displayed. The directive expects the model it is bound to to be a JavaScript Date object, and if it isn't it will generate a new one and replace whatever value is currently stored in that model. It will then proceed to update the time property of the Date object when the user interacts and selects a different time.

Currently the directive depends on Angular 1.2.0 and above, and I'm currently evaluating whether I need to update the code to work with older versions of Angular or not. It also depends on Angular UI Bootstrap 0.3.0 and above, solely for the $position service that it comes with. If the Angular library has its own $position service, I'll update my code.

It’s free to use, so feel free to download it, use it, even abuse it, as long as you keep the copyright info intact.

UPDATE 6/12/2013

The project has been moved. <a href="https://github.com/Geta/angular-timepicker" title="Angular Timepicker Directive" target="_blank">Here is the new URL</a>.
