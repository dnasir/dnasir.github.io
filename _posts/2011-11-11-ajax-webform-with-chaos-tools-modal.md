---
layout: post
title: AJAX Webform with Chaos Tools Modal in Drupal 7
date: 2011-11-11 17:21
comments: true
categories: [Drupal 7, PHP, Programming, Web Development]
---
A client of mine asked me to create a pop-up contact form for a Drupal page I had recently created for them. Naturally my first thought was to use jQuery, but then I realized that jQuery had been implemented in the Chaos Tool Suite. So why not that advantage of this and use the CTools to create the modals for me. As for the contact form, I decided to use Webform. No particular reason. It was the first one that came up in my search results.

In this tutorial, I will show you how to create a CTools module that opens a modal window and displays a webform that you can submit using AJAX, and have it display the form submission confirmation message in the same modal window.

Prerequisites

Firstly, I'm using Drupal 7.8 for this tutorial. So if you're using an older (or newer) version of Drupal, this tutorial may or may not apply to you. Then there are a couple of modules you're going to need to download.
<ul>
	<li><a title="Chaos tool suite (ctools) | drupal.org" href="http://drupal.org/project/ctools" target="_blank">Chaos Tool Suite</a></li>
	<li><a title="Webform | drupal.org" href="http://drupal.org/project/webform" target="_blank">Webform</a></li>
</ul>
Depending on your server setup, you may or may not require the SMTP Authentication Support module. I did, for some reason.

Once you've acquired the above modules, enable the following;
<ul>
	<li>Chaos tools</li>
	<li>Webform</li>
</ul>
Creating the webform

Since you want to insert a webform into a CTools modal, you'll obviously going to need a webform. Go ahead and create a new webform. I'm not going to cover the instructions on how to get your webform up and running. There are plenty of tutorials on how to do this. However, in order for this to work, you'll need to enable one particular option. In your webform's Form settings, select No redirect under Redirection location. I'll explain why later on in this tutorial.

Creating the modal window

The Chaos Tool Suite comes with a tool that makes it easy to create a modal dialog window. But so far, I found CTools to be the least documented module I have had to use thus far.
