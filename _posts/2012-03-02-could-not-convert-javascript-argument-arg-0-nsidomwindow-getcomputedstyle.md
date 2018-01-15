---
layout: post
title: "Could not convert JavaScript argument arg 0 [nsIDOMWindow.getComputedStyle]"
date: 2012-03-02 20:24
comments: true
categories: [beforeremove, "Could not convert JavaScript argument arg 0 [nsIDOMWindow.getComputedStyle]", JavaScript, javascript, jquery, jQuery, KnockoutJS, knockoutjs, Web Development]
---
I'm working on a small project at work using [KnockoutJS](http://knockoutjs.com/ "KnockoutJS"){:target="_blank"} (which is awesome by the way) and I ran into a problem which seems to only occur on Firefox. I'm using the `beforeRemove` method for my template that is supposed to use jQuery's `fadeOut` method to fade the element out before removing it from the DOM. While this works on Chrome, and even Internet Explorer (!!!), I am greeted with the following error on Firefox (Firebug).

```shell
Could not convert JavaScript argument arg 0 [nsIDOMWindow.getComputedStyle]
```

A quick search on ol' Google and I came to the conclusion that jQuery is attempting to style an element that no longer exists. This is weird, since the `beforeRemove` method was supposed to run before the element is removed from my observable array. So I tried replacing `fadeOut` with a simple `hide` method, and sure enough, it worked. So it means that it does run before the element is removed. But why doesn't it work with `fadeOut`?

So I'm guessing the `remove` method is somehow called while the `fadeOut` method has yet to complete its run, resulting in jQuery to lose its DOM element to fade out.

My workaround is simple, run the `remove` method AFTER the `fadeOut` method is complete. In the below example, I have an Ajax call function called serviceProxy that contains a method called removeStory. Upon success, it will run the callback code which is supposed to fade out the deleted DOM element, and remove it from my observable array.

```javascript
serviceProxy.removeStory(story.id(), function () {
	$('#post-' + story.id()).animate({ height: 0, opacity: 0 }, 'normal', function () {
		ctx.newsFeeds.remove(story);
	});
});
```

So I've decided to avoid using the `beforeRemove` method, at least until the KnockoutJS people come up with a solution and do this instead. It may not be the most elegant solution, but it works. You could also use jQuery's `then` method for this, if you're planning to do something else.

I'd like to hear from you guys about other solutions you guys came up with. Preferably something that would allow me to use the `beforeRemove` method.

<em>Wassalam</em>
