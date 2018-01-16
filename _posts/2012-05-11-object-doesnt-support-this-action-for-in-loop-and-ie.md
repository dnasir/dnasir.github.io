---
layout: post
title: "Object doesn't support this action - For In Loop and IE"
date: 2012-05-11 06:38
comments: true
categories: [for in loop, IE, internet explorer, JavaScript, Object doesn't support this action, Web Development]
---
I've recently encountered a problem in Internet Explorer when I was using a for-in loop in one of my scripts. It was a simple loop too.

```javascript
for (item in data) {
    $row.data(item, data[item]);
}
```

This piece of code will attach a data property for each JSON object it receives from an Ajax request. Simple, right? This works in every other browser I've tested it on, EXCEPT Internet Explorer.

Turned out the word `item` is ~~a reserved word or something~~ [an IE method](http://msdn.microsoft.com/en-us/library/ie/ms536460(v=vs.85).aspx){:target="_blank"}, because when I swapped item with something else, like j or _item, the code immediately works.

<!--more-->

So something like this:

```javascript
for (_item in data) {
    $row.data(_item, data[_item]);
}
```

and this:

```javascript
for (j in data) {
    $row.data(j, data[j]);
}
```

will work. But using `item`, without declaring what `item` is, will not.

Just in case someone else runs into this problem ;-)

*Wassalam*
