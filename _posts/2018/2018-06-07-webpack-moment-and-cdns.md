---
layout: post
title: "Webpack, Moment.js and CDNs"
date: 2018-06-07 19:20
comments: true
tags: [webpack, "moment.js", cdn]
---

In one of my projects, I needed to implement MomentJS with localisation. I thought, "Alright, this should be simple enough."

Here's the funny thing about "simple" tasks. There's always a catch. It just so happens the catch with this one is that I had to 
make the site load MomentJS from a CDN server, and fallback to a local copy in the event CDN is down.

Still, it's not really a big deal - or so I thought.

Once I've set everything up, I ran Webpack. I immediately noticed that the file size had increased by over 500 kB.

Five hundred kilobytes! What on earth..

Turns out the moment I imported a MomentJS locale file into one of my files, for some bizzare reason webpack decided to include the entire
MomentJS library, **AND ALL THE LOCALE FILES.**

<!--more-->

Upon inspection, I've discovered that every single locale file starts with the following mantra:

```js
;(function (global, factory) {
   typeof exports === 'object' && typeof module !== 'undefined'
       && typeof require === 'function' ? factory(require('../moment')) :
   typeof define === 'function' && define.amd ? define(['../moment'], factory) :
   factory(global.moment)
}(this, (function (moment) { 'use strict';
...
```

Yea. That `require('../moment')` statement was throwing Webpack off into including the entire MomentJS library, and since the entry point
for MomentJS forces Webpack to include all locale files, we end up with half a megabyte of redundant data.

I played around with `ProvidePlugin`, `ContextReplacementPlugin` and the likes in my desperate attempt at resolving this issue, 
before discovering that simply including that very path (`../moment`) to Webpack's `externals` property was the only way I could get the locale file I needed included in my build without all the additional junk.

```js
externals: {
    'moment': 'window.moment',      // for the standard moment imports
    '../moment': 'window.moment'    // for moment imports by locale files
}
```

That's it. I have now imported a MomentJS locale, without having imported the entire MomentJS library and all the locales, thus saving
500 kB from my output file. Needless to say, this simple solution made me feel like an idiot, but whatever.

I hope the MomentJS devs will take heed, and start updating their code to the latest ES standards, where all crap this could have been avoided.
But until then, we're stuck with workarounds like this one.

*Wassalam*
