---
layout: post
title: Get Nested Property Of An Object
date: 2012-05-02 07:57
comments: true
categories: [JavaScript, Web Development]
---
I was working on a small filtering script for a GMaps project, and I needed to create a filtering method to filter markers based on their properties. The catch is, the filtering method has to be able to filter out markers based on several types of properties. Not all at once though.

So obviously I can write a separate method for all the types of filter material required. Every method will grab all the markers on the map, iterate through them, filter out the irrelevant markers, and return the results. But that would result in code duplication, and that's not how I roll. So instead, I created a single function that handles the filtering, and one method that allows you to get property of nested objects using their property path.

<!--more-->

It essentially allows you to grab the nested property of an object using its property path, eg. `Object.InnerObject.SecondLevelObject.PropertyName`, or `Object['InnerObject'][SecondLevelObject']['PropertyName']`. It returns `false` if the property doesn't not exist or is undefined.

```javascript
function getObjectProperty(obj, propertyPath) {
    var o = obj;

    if (typeof (o) != 'object') return false;

    var p = propertyPath.toString().split('.');
    var c = p[0];

    if (typeof (o) != 'undefined' &amp;&amp; o.hasOwnProperty(c)) {
        if (typeof (o[c]) == 'object') {
            p.shift();
            p = p.join('.');

            var n = o[c];
            var _n = getObjectProperty(n, p);

            if(typeof (_n) != 'undefined') {
                return _n;
            }
            return false;
        } else {
            return o[c];
        }
    }
}
```

I guess some people will look at this and think it's a bit pointless. I could have just used the property path for each object property in my filters. But imagine this - you have about a thousand markers, each with its own properties. Then you have a filter that filters the markers - in my case - by their respective store names, store types, store locations, so and so forth. It would make sense to create a function that can iterate through the markers and find what you're looking for instead of creating a new function to deal with each and every situation.

I hope someone else will find this useful. As usual, there are countless ways to achieve what I was trying to achieve, and this is just one of them.

*Wassalam*
