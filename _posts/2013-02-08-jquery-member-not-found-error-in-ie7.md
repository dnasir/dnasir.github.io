---
layout: post
title: "jQuery: 'Member not found' error in IE7"
date: 2013-02-08 18:00
comments: true
categories: [attr, ie7, JavaScript, javascript, jquery, jQuery, member not found, Web Development]
---
Recently I've been working with a jQuery dropdown plugin called <a href="http://ddslickremablized.remabledesigns.com/" target="_blank">ddSlick Remablized</a>, which is a plugin that allows you to have customised dropdowns on your page. However, after implementing it in one of my projects, I've discovered a bug in Internet Explorer 7, which looks something like this:

<img class="size-full wp-image-1986 alignnone" title="Member not found" alt="member-not-found-ie7-error" src="http://dnasir.com/wp-content/uploads/2013/02/member-not-found-ie7-error.png" width="438" height="288" />

It turns out that this bug has been reported, and you can see the details here: <a href="http://bugs.jquery.com/ticket/12577" target="_blank">http://bugs.jquery.com/ticket/12577</a>. However, it seems like this bug is yet to be fixed. So a suitable workaround is in order.

After an hour of debugging using the IE Developer Tool, and let's be honest - it's the most useless developer tool ever, I've traced the problem down to a line in the code where jQuery's .attr() method is invoked to set a number of properties to a dynamically generated element.

It seems to me that jQuery is having trouble setting the attributes for dynamically generated elements. I'm not sure why, and I currently don't have time to dig deeper.

So I decided to use the native JavaScript implementation for setting element attributes, .setAttribute(). According to <a href="http://www.quirksmode.org/dom/w3c_core.html#attributes" target="_blank">quirksmode.org</a>, IE7 has an incomplete support for the .setAttribute() method. But the description they mentioned that this only affects the styles attribute, which is not what we're doing, because you should always use CSS for your styles.

Lo and behold, it worked. The plugin started to work, and no errors were displayed. Awesome!

I'm sure there are countless other reasons for this bug, but my solution fixed my problem, and at the moment, that's all I need. If you know something more about this bug, or you managed to fix it using a different method, I'd love to hear from you, so please leave a comment.

<em>Wassalam</em>
