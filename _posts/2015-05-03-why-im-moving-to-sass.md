---
layout: post
title: Why I'm moving to Sass
date: 2015-05-03 21:56

comments: true
categories: [CSS, css preprocessors, less, sass, Web Development]
---
As a front-end developer, I've had my fair share of working with CSS preprocessors. Two of the most prominent preprocessors I've had the pleasure of working with are Sass and Less. They're both great, and I still use both of them, but here's why I'm leaving Less for Sass.

<!--more-->

<h2>Variable declaration</h2>

Less uses `@`, and Sass uses `$`. Technically, it doesn't really matter which symbol is used, but personally I don't agree with the Less approach. Why? Well, the `$` symbol has no special meaning in CSS, but `@` is used for declaring stuff like fonts and media queries.

```less
// Less
@my-variable: 35px;
@font-face {
  font-family: 'My Awesome Font';
  src: url('my-awesome-font.woff');
}
```

```scss
// Sass
$my-variable: 35px;
@font-face {
  font-family: 'My Awesome Font';
  src: url('my-awesome-font.woff');
}
```

I prefer having standard CSS stuff being used for standard CSS stuff, and everything else should use their own stuff.

<h2>Mixin declaration</h2>

In line with my previous point, Less mixins are declared in the same way classes are declared, with the exception of the parameter field where applicable. Again, I'm not too fond of how this works, and the reason can be demonstrated in the code below:

```less
// just a mixin I'm using as a template
.my-icons() {
  display: inline-block;
  width: 16px;
  height: 16px;
}

.my-icons {
  .my-icons;
  // I want to override this
  display: block;
}

.special-icons {
  // I want to use the original mixin
  .my-icons();
}

// Result
.my-icons {
  display: inline-block;
  width: 16px;
  height: 16px;
  display: block;
}
.special-icons {
  display: inline-block;
  width: 16px;
  height: 16px;
  display: block;
}
```

Firstly, you probably shouldn't be doing this. Secondly, this isn't what I wanted. Because Less mixins are declared the same way CSS classes are, it's more susceptible to confusion that may result in unpredictable code.

Now let's try the same thing in Sass.

```scss
// just a mixin I'm using as a template
@mixin my-icons() {
  display: inline-block;
  width: 16px;
  height: 16px;
}

.my-icons {
  @include my-icons;
  // I want to override this
  display: block;
}

.special-icons {
  // I want to use the original mixin
  @include my-icons;
}

// Result
.my-icons {
  display: inline-block;
  width: 16px;
  height: 16px;
  display: block;
}

.special-icons {
  display: inline-block;
  width: 16px;
  height: 16px;
}
```

This is much better. There's a clear distinction between a Sass mixin and regular CSS classes, and this helps avoid the confusion I was talking about earlier.

<h2>Math calculations</h2>

I ran into this issue some time ago, and I had hoped that the Less community had fixed the bug by now. Unfortunately, that hasn't been the case. The issue was, if you're calculating two or more values with different units, Less will assume the first unit is the one you want, and it will ignore all subsequent units. This is weird and not at all how I expected calculations to work.

```less
.navbar {
  height: 200px + 2em + 45pc;
}

// Result
.navbar {
  height: 247px;
}
```

Running the above code in a Sass compiler will simply result in an error, which in my humble opinion is probably the better option.

<h2>Conditionals</h2>

This is an awesome feature in Sass that is not available in Less. Granted, there's a way around this, but it's not as simple and elegant as how it's done in Sass.

```less
.special-button (@size) when (@size &gt; 10px){
  padding: 10px 15px;
  background-color: #f2f2f2;
  display: inline-block;
  vertical-align: middle;
}

.special-button (@size) when (@size &lt;= 10px) {
  padding: 2px 5px;
  display: inline-block;
  vertical-align: middle;
}

.button1 {
  .special-button(5px);
}

.button2 {
  .special-button(15px);
}
```

Let's see how it would look like written in Sass.

```scss
@mixin special-button ($size: 5px) {
  @if($size &gt; 10px) {
    padding: 10px 15px;
    background-color: #f2f2f2;
  }
  @else {
    padding: 2px 5px;
  }

  display: inline-block;
  vertical-align: middle;
}

.button1 {
  @include special-button(5px);
}

.button2 {
  @include special-button(15px);
}
```

```css
/* Result for both Less and Sass */
.button1 {
  padding: 2px 5px;
  display: inline-block;
  vertical-align: middle;
}

.button2 {
  padding: 10px 15px;
  background-color: #f2f2f2;
  display: inline-block;
  vertical-align: middle;
}
```

While both output the same result, I find the Sass approach to be much more elegant compared to how it's done in Less. Then there's the issue of duplicate code in the Less code, which can be solved by doing something like this:

```less
.special-button (@size) {
  display: inline-block;
  vertical-align: middle;
}

.special-button (@size) when (@size &gt; 10px){
  padding: 10px 15px;
  background-color: #f2f2f2;
}

.special-button (@size) when (@size &lt;= 10px) {
  padding: 2px 5px;
}
```

Again, not so elegant.

Sass also comes with a `for` loop function, but I haven't yet found a situation where I'd been needing it. However, when that time comes, I can rest easy knowing Sass has got me covered.

<h2>Bootstrap support</h2>

When I started using CSS preprocessors, I started with Less. The main reason being Bootstrap, the most popular CSS framework at the time, was built using Less. I worked a lot with Bootstrap, which meant I worked a lot with Less.

Now that Bootstrap has an official Sass port, I've decided to use this in all my future projects.

So, there you have it. My reasons for switching to Sass. Did I miss something? Let me know in the comments below.

<em>Wassalam</em>
