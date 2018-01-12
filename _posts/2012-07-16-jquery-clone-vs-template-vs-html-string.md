---
layout: post
title: jQuery Clone vs Template vs HTML String
date: 2012-07-16 18:17
comments: true
categories: [HTML, JavaScript, javascript, javascript performance, jquery, jQuery, Web Development]
---
Every now and again I get into this situation where I have a list, and some JS function will retrieve more stuff via Ajax to insert into that list. This is normally fine if you stick to normal lists, but once you start using DIVs (eg. grid) and whatnot things start to get a bit more complicated. For one, you will have to make sure the item you're appending to the list uses the same template as the existing items, for consistency.

So the question I normally get asked is, "What's the best way to display the new list items in this kind of situation?".

There are a couple of ways to do this, and in most cases they all output the same stuff. The only difference is the speed in which the job gets done. 

I'm not going to go into detail on how each method works. I'm sure there are plenty of resources you can find on the subject.

The first method involves the use of jQuery's .clone() method. It makes sense. The list item already exists, why not just clone it and replace the values with the new ones, right? You'll need to supply a candidate for cloning though.

I like the .clone() method mainly because it's very simple to use, keeps my code clean and doesn't require any additional code to work. And because it returns a jQuery object, you can immediately use it in your jQuery method chain.

The second method uses the jQuery Template plugin that involves us setting up a template and inserting the values during the iteration. You can set up multiple templates in your markup and define them in your JS code. You can then fetch these templates later in your code and just outfit them with the necessary values.

I personally never liked this method that much, mainly because it involves additional code. It is obviously very useful, just not very pretty. The project never got past the Beta stage, and has now been discontinued. But you can still find it on the jQuery website, and it's still floating around on various CDN providers.

Finally, the third method involves string manipulation. It really just involves appending HTML strings that contains all the DIVs, classes and values. It's the least beautiful of the lot, if I'm honest, as it involves mixing JS code with HTML strings. However, if you don't like that, you could always have the HTML code you wish to duplicate somewhere in your markup and use jQuery's .html() method to retrieve the HTML code to be used in your loop.

I've set up a simple test as proof of concept on JSFiddle.net. You can see it here: <a href="http://jsfiddle.net/dzul1983/vB8Fn/2/" target="_blank">http://jsfiddle.net/dzul1983/vB8Fn/2/</a>. A small note, you'll need a browser that supports the console feature.

Based on the results, string manipulation is the fastest way to duplicate stuff, with jQuery's .clone() method being second, and .template()/.tmpl() method being the slowest of the lot. It goes to show that touching the DOM too often will slow things down. So it's important to keep DOM manipulation to a minimum.

That said, there's really little chance you will ever need to display 500+ items in any single page. It's just bad UI design, and I would definitely recommend implementing some kind of paging system for this sort of situation. In which case, the speed difference between all three method is reduced to such a small number that it really doesn't matter which method you use. I mean, who can really tell the difference between 16ms and 1ms?

<em>Wassalam</em>
