---
layout: post
title: A sneaky way of styling file input form fields
date: 2012-02-24 20:31
comments: true
categories: [CSS, HTML, JavaScript, javascript, jquery, jQuery, styling file input, Web Development]
---
There may come a time when you need to style a file input form field on your page, but here's the catch; there are currently no known way of styling the file input fields.

There are however, ways around this. Call it a hack, if you will. In this tutorial, I will demonstrate one of the methods you can use to "style" your file input fields. Note that this method does not work in Internet Explorer, and I've only tested it on the latest versions of Firefox (10.0.2), Chrome (17.0.963.56), Safari (5.1.2) and Opera (11.61).

<!--more-->

Say we start with the below HTML code:

```html
<input type="file" />
```

I've removed the other properties (method, action, etc) for the sake of readability. We then add the button right above the file input field. Actually it really doesn't matter where you place the link, but since I like to keep everything organized, it made sense to keep them close to one another.

```html
<a id="file-upload-button" class="input-file-button" href="#">Select file</a>
<input id="file-input-field" type="file" />
```

Next we add a bit of CSS.

```css
#file-upload-button {
    width: 100px;
    height: 22px;
    padding: 10px;
    background: #f5f5f5;
    display: inline-block;
    margin: 3px 0;
    line-height: 22px;
    color: #555;
}

#file-input-field {
    height: 1px;
    width: 1px;
    overflow: hidden;
}
```

Essentially what we're doing is hiding the file input field, but you can't use `visibility: hidden` or `display: none` as this will cause some issues in some browsers. I can't remember which ones, but I think one of them was Firefox.

Now we add a bit of jQuery magic, like so:

```javascript
$('#file-upload-button').on('click', function (e) {
    $('#file-input-field').click();
    e.preventDefault();
});
```

And we're done. So when the user clicks on the link above, it will trigger a click event for the file input field, where the user can then select a file for upload. Of course, you can also attach an onchange event that displays the name of the file you intend to upload, but that's outside the scope of this tutorial.

As I've mentioned earlier, this method does not work in Internet Explorer due to the way IE security works. You can open the file select box, but IE won't let you submit the file, and you'll most likely get an "Access denied" error. With that in mind, you should let your script fall back gracefully to the default file input field when it detects the Internet Explorer browser.

My suggestion is when IE is detected, just hide the button link, set overflow to visible for the file input field, to and attach a :visible selector to the jQuery bind element. You can achieve this using conditional comments and some extra IE-specific CSS rules. That way users on non-IE browsers will see the pretty "Select file" button, and IE users will still be able to upload files using the default file input form field.

Have a go, and let me know what you think. If you have any suggestions, please leave a comment.

*Wassalam*
