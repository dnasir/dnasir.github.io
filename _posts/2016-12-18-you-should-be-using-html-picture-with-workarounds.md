---
layout: post
title: You should be using HTML picture - with workarounds
date: 2016-12-18 00:02

comments: true
categories: [HTML, html5, picture, Web Development]
---
One of the biggest culprit to website data traffic, if not the biggest, is images. Due to the increase in the number of devices with high resolution display, content creators have started using high resolution images to improve the look and feel of their websites. This results in large amounts of data needing to be downloaded by everyone, including those not using hi-res displays.

HTML picture is an extension for the image element that allows us to add multiple image sources based on predetermined rules.

<!--more-->

## The syntax

```html
<picture>
  <source media="(min-width: 1024px)" srcset="images/zoom-out.png">
  <source media="(min-width: 768px)" srcset="images/zoom-mid.png">
  <img src="images/zoom-in.png" alt="Night scene">
</picture>
```

When the browser loads this up, it will display ``images/zoom-in.png`` on small displays (less than 768px wide), ``images/zoom-mid.png`` for medium-sized displays (wider than 768px but less than 1024px) and ``images/zoom-out.png`` for large displays (wider than 1024px).

This allows content creators to set different images for each individual display categories - smaller images for mobile displays, larger ones for desktops, and high resolution ones for devices that support them.

The downside is that HTML picture is not supported by some browsers, like Internet Explorer (surprise, surprise), Opera Mini and the stock Android browser. These browsers will simply ignore the picture element and render the image element located inside instead.

## Workarounds

### The ``max-width`` approach

Since non-compliant browsers load the ``img`` tag by default, setting the default image to the smallest sized image (in line with the mobile-first approach) may lead to the wrong image being displayed.

My suggestion is that you set the largest image as the default image, and use ``max-width`` media query rules instead, as shown below:

```html
<picture>
  <source media="(max-width: 767px)" srcset="images/zoom-in.png">
  <source media="(max-width: 1023px)" srcset="images/zoom-mid.png">
  <img src="images/zoom-out.png" alt="Night scene">
</picture>
```

What we're basically doing is essentially reversing the conditions so that the larger image shows up by default, and switch to smaller images for devices that support the picture tag. This way, the browser will pick up the nicer image, and everyone is happy.

Well, sort of. The caveat is that non-compliant browsers will always download the default image, so we're pretty much back to square one when it comes to reducing data traffic.

### The polyfill approach

A more robust workaround involves the use of JavaScript polyfills. There are quite a few of them around, but I suggest using this one: [Picturefill](http://scottjehl.github.io/picturefill/ "Picturefill"){:target="_blank"}

The only caveat is that you'll need to replace the ``src`` attribute with ``srcset`` for the ``img`` tag within the picture element. The reason is explained in the project documentation:

| Trying to use the src attribute in a browser that doesn't support picture natively can result in a double download. To avoid this, don't use the src attribute on the img tag

The upside is that ``srcset`` is a supported attribute for the image element, so everything should be fine on browsers that do support the picture element.

## Conclusion

HTML picture is a great addition to HTML5, and the advantages that comes with it far outweigh the lack of support on some browsers.

If you're not already using the picture element, you should definitely start now.
