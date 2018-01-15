---
layout: post
title: Adding Or Preserving Hash Values When Using Response.Redirect() For IE
date: 2013-08-15 14:23
comments: true
categories: [302 redirect, ASP.NET, asp.net, C#, IE, response.redirect, Web Development]
---
I recently ran into a small problem in one of my projects. I have some code that would open up the user login UI when the URL contains a few specific parameters, which are defined as hash parameters.

One of the tasks assigned to me needed me to redirect the user to the same page, but with the login UI activated, when they want to do something that required them to be authenticated. Naturally, I went ahead and used the Response.Redirect() method and appended the hash parameters to the end of the current URL. Then I ran my usual tests.

<!--more-->

Chrome? Check.

Firefox? Check.

Opera? Check.

Safari? Check.

Then came the moment of truth. IE? The hash values were missing!

A quick search on the Almighty Google brought me to this page on StackOverflow: [http://stackoverflow.com/questions/2286402/url-fragment-and-302-redirects](http://stackoverflow.com/questions/2286402/url-fragment-and-302-redirects){:target="_blank"}

Regular browsers, such as Chrome and Firefox have no problems preserving URI fragments. IE, on the other hand, discards them on 302 HTTP Redirect requests. This explains why the hash values in my URL were missing in IE.

While the selected answer on StackOverflow suggests that I use the Location header to set my URL fragments, the project I was working on is rather old and large, with lots of code that does all kinds of stuff to the URL. So playing around with the HTTP headers was not an option, at least not in my case.

A far simpler solution for me was to <strong>replace the hash, or pound, symbol with its URL-encoded counterpart</strong>, which is '%23'. And guess what? That fixed it. It's now working on all the browsers I've tested it on, including IE.

```csharp
// Before
Response.Redirect(Request.RawUrl + "#login");

// After
Response.Redirect(Request.RawUrl + "%23login");
```

So if you want to avoid messing around with the HTTP headers, just encode the URL.

<em>Wassalam</em>
