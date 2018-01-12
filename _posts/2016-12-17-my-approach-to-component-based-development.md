---
layout: post
title: My approach to component-based development
date: 2016-12-17 22:46

comments: true
categories: [cbd, component-based development, CSS, Front-end Development, HTML, JavaScript, Web Development]
---
I've been experimenting with a few approaches to

I started looking at web projects as simply a set of features and components, and with this in mind, I started to

Everything my component needs should exist inside its own domain. This will make it easier later down the line, when I need to make changes, or fix a bug, and I don't need to go through several folders to find what I need.

For example, I need to update how my carousel works, as well as fix a few visual bugs. In a traditional set-up, I would be looking for the JS file inside a folder where all my JS files are located, and the same goes for the CSS file, as well as my template files.

In a feature-driven development scenario, all the code for my component is located in one place, and I don't have to traverse through several folders to find what I'm looking for.

Ideally, the components should be isolated enough that they can be added and removed without breaking unrelated code, or at least with minimal fuss. That was my goal when I started walking down this path.

<h2>Project file structure</h2>

This is a simplified version of what my project structure looks like:

<pre><code>MyProject
|- components
|  |- carousel
|     |- scss
|     |- ts
|     |- views
|     gulpfile.js
|
gulpfile.js
package.json
tsconfig.json
</code></pre>

As you have probably noticed, my component folder has its own <code>gulpfile.js</code>. The reason for this is really to spread out the gulp task definitions. At one point I had over a dozen separate gulp tasks in a single <code>gulpfile.js</code> and the file just got a bit too large for my liking.

Spreading things out also allows me to set up specific build configurations for my components. Granted, this can often result in code duplication, but it gives me full control over how my components are built. I can also run component-specific tasks individually without running the entire project build.

<h2>Setting up the task runner</h2>

The root <code>gulpfile.js</code> is what I run whenever I need to rebuild the entire project. Otherwise I simply run component-specific gulp tasks.

The easiest method to run the default gulp task for all my components is to use <a href="https://www.npmjs.com/package/gulp-chug">gulp-chug</a>. This plugin allows me to pass in a glob containing the path to the <code>gulpfile.js</code> for all my components and run them from my main <code>gulpfile.js</code>.

<ul>
<li>Use gulp-chug to run nested gulpfile</li>
<li>Root gulpfile contains task declaration of nested gulp tasks (optional)</li>
</ul>

<h2>JS</h2>

<ul>
<li>Feature-specific code</li>
<li>Use common code when necessary</li>
<li>Code should not depend on another feature's code, unless absolutely necessary</li>
<li>Use mediator pattern for building modules - modules communicate through mediator via events</li>
<li>mediator.js</li>
</ul>

<h2>CSS</h2>

<ul>
<li>Set up a base style</li>
<li>Set up feature-specific style that does not depend on base style</li>
<li>Feature-specific style may override base style</li>
<li>Use base style when necessary</li>
<li>BEM methodology is useful in this situation</li>
</ul>

<h2>Views/Templates</h2>

<ul>
<li>Use a common markup structure</li>
<li>Use base style classes where applicable</li>
<li>Use feature-specific style classes to override base styles</li>
<li>May result in longer class names</li>
</ul>
