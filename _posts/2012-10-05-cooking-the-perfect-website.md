---
layout: post
title: Cooking the perfect website
date: 2012-10-05 17:28
comments: true
categories: [CSS, css3, HTML, html5, JavaScript, jquery, js frameworks, js libraries, jscompress, modernizr, tips, Web Development, website optimization, yui compressor]
---
Throughout my career as a web developer, I've come to realise that building a website is a lot like cooking. Everything needs to be just right, and that there is a certain balance that needs to be kept. Get it right, and you'll be serving a Gordon Ramsay dinner - rich, elegant and enjoyed by all. Get it wrong, and you'll be serving a fast food dinner - greasy, bloated and no good to anyone.

It's important to think about what you actually need as it's very easy to get lost and end up with a website that actually have more features than you actually need.

I have compiled a list of guidelines that I personally use when starting a new project.

<strong>1. Start small.</strong>

It's always better to add features to your website rather than trying to slim it down later on. There are plenty of these pre-built templates such as the popular "HTML5 Boilerplate" that can help you quickly build a HTML5 website, but you should ask yourself one thing - Do you actually need all the features that come with the template?

It's easy to lose sight of what you have, and what you actually need. HTML5 templates provide a platform for developers to quickly build and deploy websites, but without careful consideration on what your website actually needs, you end up including features your website will never use.

For example, let's say you're building a HTML5 website. You start using all the HTML5 tags, and all is well in the kingdom of modern browsers. You start testing your website in older browsers, and lo and behold, it breaks in Internet Explorer 8. That's right, IE8 doesn't support HTMl5 tags. So you do what many developers do, and you include Modernizr in your project.

Think about it. All you needed was for IE8 to understand what HTML5 tags are. You don't need the whole Modernizr library to get that working. I personally use the <a title="HTML5Shiv" href="https://code.google.com/p/html5shiv/" target="_blank">HTML5Shiv</a> for cases like this. The full minified version of the Modernizr script is 15KB, where else the minified HTML5Shiv script is just 4KB.

It really is a matter of less is more.

<strong>2. Work backwards.</strong>

Build for the latest browsers, using the latest standards, and work your way backwards in providing support for older browsers. Technology is progressing so quickly that at the current rate, older browsers such as Internet Explorer 8 will quickly be killed off and support dropped.

If you do decide to support older browsers, and I recommend that you do, determine how far back you're willing to go. IE8? IE7? Or, God forbid, IE6? How about older Mozilla browsers?

In any case, you can find solace in knowing that there are polyfills out there that can help you with this. A combination of conditional comment added CSS and polyfills can make a difference.

I personally use the <a title="HTML5Please" href="http://html5please.com/" target="_blank">HTML5Please</a> website whenever I need to find a polyfill for a HTML5 feature I plan to support on older browsers. You can find a list of polyfills for HTML5 features such as HTML5 form validation and HTML5 elements. It really is a handy little tool, and all developers should have it bookmarked somewhere.

<strong>3. Optimize your JavaScript libraries.</strong>

I cannot stress this one enough. One of the worst culprits to website performance is JavaScript libraries. I have one too many times come across developers who include a whole JavaScript library in their projects just so they can use one or two of its features. Unfortunately, I have also made the same mistake during my early development years.

The more popular libraries such as jQuery UI, and Modernizr, provide the option for developers to create their own custom build of their libraries. So if you for example, only need the autocomplete plugin, you don't need to include the whole jQuery UI library in your project. I seriously recommend doing this, because it really does make a difference.

Also, minify and combine your scripts. You can seriously reduce the number of requests your websites make, and also save a lot of bandwidth in the process. There are plenty of tools out there that can help you do this like the <a title="YUI Compressor" href="http://developer.yahoo.com/yui/compressor/" target="_blank">YUI Compressor</a> and <a title="JSCompress" href="http://jscompress.com/" target="_blank">JSCompress</a> if you prefer an online solution.

<strong>4. Use Content Delivery Networks (CDN).</strong>

Modern browsers tend to cache static files such as JavaScript files and CSS stylesheets. So if your users are regular web surfing people, like most web users nowadays, it's highly likely they already have a copy of jQuery or Modernizr cached somewhere on their machine.

When a browser starts downloading your website, it will come across the CDN link to, for example, jQuery, and if it finds that the file already exists in its cache, it will skip downloading and use the cached version. This will greatly reduce the amount of requests your users make when visiting your website.

Additionally, by using popular CDNs like the <a title="Google CDN Guide" href="https://developers.google.com/speed/libraries/devguide" target="_blank">Google CDN</a> and <a title="CDNJS" href="http://cdnjs.com/" target="_blank">CDNJS</a>, your users will be able to download files from the CDN server that is geographically closest to them, thus improving latency and reducing download times, and because they're downloading from an external server, you get to keep your bandwidth.

So use CDNs whenever possible and remember, for every redundant request your website makes, a baby unicorn dies.

As a general rule of thumb, keep things simple and don't try to solve problems you don't yet have. Build for modern browsers, and if your company policy includes support for older browsers, add support for those browsers as you go, keeping in mind to only add what you need and nothing more. You don't want to ruin a perfectly good dish by over-garnishing it.

<em>Wassalam</em>
