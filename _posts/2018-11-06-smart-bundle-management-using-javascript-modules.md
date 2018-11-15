---
layout: post
title: "Smart Bundle Management Using JavaScript Modules"
date: 2018-11-06 00:11
comments: true
tags: [html, "javascript modules"]
---

I recently read an [article on SmashingMagazine][1] on managing bundles for modern
and legacy browsers, in which the author had discovered that bundles built for legacy browsers
are, on average, 25% larger than those created for modern browsers.

This is mainly due to the fact that native support for ES6 has resulted in the possibility to achieve
the same results with less code. Unfortunately, legacy browsers such as Internet Explorer lack support
for most, if not all, of ES6, forcing developers to include workarounds and polyfills just to get things
working, resulting in larger bundle files.

In the article, the author suggested creating separate bundles for each platform, and employing server-side browser 
sniffing to serve the appropriate bundle to users.

While this method works, there are [numerous reasons why browser sniffing is bad][3].

<!--more-->

I think a better approach would be to use JavaScript modules, which was introduced in HTML 5.2,
and letting the browsers decide which bundle it should load.

```html
<script type="module" src="bundle.modern.js"></script>
<script nomodule src="bundle.legacy.js"></script>
```

Browsers that support JavaScript modules are programmed to load the first script, and ignore
the second one, while legacy browsers will simply ignore the first line because it doesn't
understand what it is.

The main advantage of this strategy is that you don't need to do anything other than include
one script tag for modern browsers, and another one for legacy, and let the browser handle
the rest.

The drawback is that JavaScript modules support was included a few versions after ES6 support
was added on most major browsers. So anyone who hasn't kept their browsers up-to-date will not
be able to benefit from this cool feature, even if their browser does support ES6.

These browsers will simply fall back to using legacy code that, if built correctly,
should have no negative impact other than having to load additional bloat that resulted from 
the polyfills that these browsers don't actually need.

Regardless, I think this is still the way to go, as we currently have no idea when Microsoft
will drop support for Internet Explorer, and we still have to support our corporate visitors,
many of whom are stuck on this archaic monstrosity due to silly corporate policies.

**Update**

Apparently, as it turns out, IE still downloads both files, but it would only parse the legacy code,
as demonstrated in the screenshot below.

[![alt text][figure 1]][4]

So JS modules will not help you reduce the overall download size, at least not on IE. In which case,
the original suggestion to use browser sniffing to serve the correct bundle makes more sense.

Alternatively, you can always just transpile your code to ES5, and provide polyfills in a separate
bundle for legacy browsers using the `nomodule` attribute. Just make sure that the polyfills come first,
to avoid any potential issues during parsing.

```html
<script nomodule src="polyfills.js"></script>
<script src="bundle.js"></script>
```


[1]: https://www.smashingmagazine.com/2018/10/smart-bundling-legacy-code-browsers/
[2]: https://caniuse.com/#feat=es6-module
[3]: https://css-tricks.com/browser-detection-is-bad/
[4]: /assets/img/2018/11/ie-perf-tab-captioned.png
[figure 1]: /assets/img/2018/11/ie-perf-tab-captioned.png "IE 11 still downloads JS modules"