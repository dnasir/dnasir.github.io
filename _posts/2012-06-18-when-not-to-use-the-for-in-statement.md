---
layout: post
title: When not to use the for...in statement
date: 2012-06-18 11:00
comments: true
categories: [for-in, for-loop, JavaScript, javascript, Web Development]
---
So today a colleague of mine ran into a small JavaScript problem. One of his functions ran an Ajax request which returns a JSON object which is parsed using jQuery's <strong>parseJSON</strong> method. The resulting array is then ran through another function which does some calculations and displays the results in a list.

Here's where the problem starts. The method that does the calculations runs through the array using the native JavaScript <strong>for...in</strong> loop, in which a calculation method is applied to every item in the array. However, for some reason, the list contained one extra item with the value <strong>NaN</strong>. Upon inspecting the array, I found that there was an extra item called <strong>map</strong>, which was a function. You can probably already guess by now why the calculation didn't work as it was expected to, and rightly so, especially when the value used in the calculation was a function.

To simplify things, take the below code snippet as an example;

[javascript]
var arrayObj = new Array();

arrayObj[0] = 1;
arrayObj[1] = 2;
arrayObj["myfunction"] = function(){ alert('This is my function') };

for(var i in arrayObj){
    document.write(arrayObj[i]) * 5 + "<br />";
}
[/javascript]

This will actually result in JavaScript rendering the below;

[html]
5
10
NaN
[/html]

So where's the problem? Well, firstly the for...in statement is a loop method to be used when iterating through <strong>object properties</strong>. So to use it for iterating through an array is abuse in itself, and if it produces unexpected results, well, that's to be expected.

Now take this example;

[javascript]
var arrayObj = new Array();

arrayObj[0] = 1;
arrayObj[1] = 2;
arrayObj["myfunction"] = function(){ alert('This is my function') };
arrayObj[10] = 10;

for(var i in arrayObj){
    document.write(arrayObj[i]) * 5 + "<br />";
}
[/javascript]

This piece of code will actually produce;

[html]
5
10
NaN
50
[/html]

However, when you check the array length, you'd notice that it shows up as 11. The for-in statement completely ignores the undefined items in the array, unlike the <strong>for</strong> loop. Iterating through the above array using the <strong>for</strong> loop will result in the below;

[html]
5
10
NaN
NaN
NaN
NaN
NaN
NaN
NaN
50
[/html]

So as a general rule of thumb, one should always use the <strong>for</strong> loop when iterating through an array, to avoid having problems with non-array items. The below example will do what we actually want done;

[javascript]
var arrayObj = new Array();

arrayObj[0] = 1;
arrayObj[1] = 2;
arrayObj["myfunction"] = function(){ alert('This is my function') };

for(var j = 0; j < arrayObj.length; j++){
    document.write(arrayObj[j]) * 5 + "<br />";
}
[/javascript]

I hope this helps clear some stuff up.

<em>Wassalam</em>

UPDATE:

A friend of mine pointed out to me that if I were to use the for...in statement, it is important to also use the .hasOwnProperty() method to make sure the object we're testing are our object properties and not the prototype chain. This is important since you only want to iterate through your own object properties and not the prototype chain, and without using the .hasOwnProperty() method, the for...in statement will iterate through the prototype chain. Definitely not what we want.
