---
layout: post
title: "Twitter Bootstrap in IE8 - 'eval' is null or not and object"
date: 2012-03-16 20:29
comments: true
categories: ["'eval' is null or not and object", CSS, css, LESS, less, Web Development]
---
I've recently decided to move onto LESS for my current and future projects, after seeing how simple CSS can be using LESS. So I downloaded Twitter Bootstrap 2.0.2 and Less.js 1.3.0 and included them in Visual Studio. Mind you, you might need to install Mindscape Web Workbench to be able to add LESS files via the Add New Item window. You can get it via Visual Studio's Extension Manager.

Anyway, after "installing" LESS into my project, I ran some tests. I opened up Rockmelt, my default browser, and lo and behold everything worked just like it did using standard CSS. Then I opened the same project using Internet Explorer 8 (I seem to be the only one at the office who still uses IE8) and I was greeted with this error.

<a href="http://www.dnasir.com/wp-content/uploads/2012/04/bootstrap-less-error.png"><img class="alignnone size-full wp-image-1774" title="bootstrap-less-error" src="http://www.dnasir.com/wp-content/uploads/2012/04/bootstrap-less-error.png" alt="" width="810" height="140" /></a>

<!--more-->

Hmm. I was perplexed. Line NULL?? I wasn't even sure where I'd start debugging. Fortunately, a quick search on Google led me to this forum topic: <a href="https://github.com/duncansmart/less.js-windows/issues/12">https://github.com/duncansmart/less.js-windows/issues/12</a>

The forum folks suggested that LESS 1.3.0 was failing to properly parse the mixin definitions, and is the root cause for this ordeal. A user suggested a quick fix, which doesn't seem to make sense at first (and still doesn't), where you add a random property, or even a comment line, to the `.ie7-inline-block()` property in the `mixins.less` file.

So your `.ie7-inline-block()` should look like this when you're done:

```css
.ie7-inline-block() {
    *display: inline; /* IE7 inline-block hack */
    *zoom: 1;
}
```

The final comment line made the LESS version of Twitter Bootstrap work in IE8, and IE7, albeit a couple of missing features like gradient background colour and rounded border edges. Otherwise it works fine now.

I hope this helps someone else facing the same issue.

<em>Wassalam.</em>
