---
layout: post
title: jQuery Cascading Dropdown Plugin Hits Version 1.2.0
date: 2013-08-06 10:40
comments: true
categories: [cascading dropdown, JavaScript, jquery, jQuery, jquery plugin, Web Development]
---
Just a quick heads up, my jQuery Cascading Dropdown plugin is now updated!

Here's a list of changes.

<ol>
	<li>Added onReady event that gets triggered when all dropdowns are fully initialised, including dynamically loaded dropdowns.</li>
	<li>Added onChange event that gets triggered whenever a dropdown value changes.</li>
	<li>Added source option property for defining dropdown data source. This can be an array, an array of objects, or a function.</li>
	<li>Added setSelected method to set the selected dropdown item from code.</li>
	<li>onChange event for a single dropdown now passes in the event object and the current values of required dropdowns.</li>
	<li>Removed textKey and valueKey. Use the source option to handle Ajax data.</li>
	<li>Methods can now be called using the plugin and passing in the method as string, along with additional parameters.</li>
</ol>

Check it out on the <a href="https://github.com/dnasir/jquery-cascading-dropdown" title="jQuery Cascading Dropdown plugin" target="_blank">project Github page</a>.
