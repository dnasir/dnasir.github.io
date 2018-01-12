---
layout: post
title: Creating a Multilingual Drupal site
date: 2011-10-04 00:41
comments: true
categories: [drupal, Drupal 7, howto, i18n, internationalization, multilingual, tutorial, Tutorials, Web Development]
---
<h4>Introduction</h4>
Recently I've been working a lot with MODX, and in case you don't know what MODX is, it's a great content management system designed with PHP developers, such as myself, in mind. You can check it out here.

The projects I have been working on using MODX are mostly multilingual sites. MODX Evolution has a great multilingual module called YAMS (Yet Another Multilingual Solution) and it allows me to create a page with multiple resources in multiple languages. Did that sound right? Well, basically you can have a page in multiple languages, and you can switch between languages by modifying the URL - usually by adding a two-letter combination of the language you want (eg. en for English, fr for French, ru for Russian). This is great, because all the pages can use the same template so they all look the same, but by adding a language tag to the URL visitors can view the same page in different languages.

Then I wanted to make the same thing work in Drupal. I got to know that there's also a multilingual solution for Drupal, namely the Internationalization module. I went through a few tutorials that explained how to set this up, which was fine, but it was until I found <a title="Drupal 7: Cracking the multilingual front page nut" href="http://bengoodyear.com/article/drupal-7-cracking-the-multilingual-front-page-nut" target="_blank">this</a> tutorial that I managed to get a multilingual front page up and running.

So in this tutorial, I'll be showing you how to set up a multilingual site with a fully functioning multilingual front page and nice clean URL paths to boot.<!--more-->
<h4>Prerequisites</h4>
In this tutorial, I'll be using Drupal 7.8, but this should work with pretty much all Drupal 7 versions.

Here's a list of modules you'll need to make this work;
<ol>
	<li><a title="Internationalization | drupal.org" href="http://drupal.org/project/i18n" target="_blank">Internationalization</a></li>
	<li><a title="Variable | drupal.org" href="http://drupal.org/project/variable" target="_blank">Variable</a></li>
	<li><a title="Pathauto | drupal.org" href="drupal.org/project/pathauto" target="_blank">Pathauto</a></li>
	<li><a title="Token | drupal.org" href="drupal.org/project/token" target="_blank">Token</a></li>
</ol>
Install these modules and then enable the following. Drupal should let you know whether you need to enable other modules as well, so do so when asked.
<ol>
	<li>Internationalization</li>
	<li>Multilingual content</li>
	<li>Variable translation</li>
	<li>Token</li>
	<li>Pathauto</li>
	<li>Variable</li>
</ol>
<h4>Configuring languages</h4>
Now that you have everything installed and enabled, you'll now need to set it up to get everything working. Let's start by installing the languages you need for your Drupal site. Go to Administration » Configuration » Languages, where all the enabled languages are listed. You'll most likely see just one language and in most cases it will be English. Now click Add Language, and select the language you want from the list, and click the Add Language button to enable the selected language for your site.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/add-language.png"><img class="size-medium wp-image-1475 aligncenter" title="add-language" src="http://subject9.files.wordpress.com/2011/10/add-language.png?w=300" alt="Add Language" width="300" height="179" /></a></p>
You should now see the newly enabled language in the list, under the default language. Oh, and before I forget, the order in which the languages are shown displays the level of priority given to each language, with the default language being at the top. This is also reflected in the order in which the languages are shown in the language switcher, more of which I will explain later in this tutorial.

Next, click on the Detection and Selection tab, and we'll set up the way your visitors access each translation of your content. In this tutorial, I'll only be using the URL detection method with the Path prefix option. This will add a two-letter abbreviation of a language to the URL, so you can have for example http://www.example/fr/content/home which will display the French translation to the home content. To do this, check the URL detection method, click Save settings, and although Path prefix should have been selected by default, click on Configure to make sure.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/enable-url-detection.png"><img class="size-medium wp-image-1484 aligncenter" title="enable-url-detection" src="http://subject9.files.wordpress.com/2011/10/enable-url-detection.png?w=300" alt="Enable URL Detection Method" width="300" height="179" /></a></p>
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/select-url-prefix.png"><img class="size-medium wp-image-1492 aligncenter" title="select-url-prefix" src="http://subject9.files.wordpress.com/2011/10/select-url-prefix.png?w=300" alt="Set URL Prefix" width="300" height="176" /></a></p>

<h4>Updating content types</h4>
Next, we need to enable multilingual support for content types. To do this, go to Administration » Structure » Content types and select the content type you want to enable multilingual support in, for example Basic page and click on Edit for that content type.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/basic-page-edit.png"><img class="size-medium wp-image-1478 aligncenter" title="basic-page-edit" src="http://subject9.files.wordpress.com/2011/10/basic-page-edit.png?w=300" alt="Edit Basic Page Properties" width="300" height="179" /></a></p>
Scroll down until you see Publishing option, click on that and under Multilingual support, select Enabled, with translation. Click Save content type, and go back to the same page to Edit another option, this time Multilingual settings. The reason for this is that unless Multilingual support is enabled in the Publishing option, the options within the Multilingual settings tab will be disabled. So now that you've enabled Multilingual support, you can now edit the Multilingual settings for the Basic page content type.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/basic-page-multilingual-settings.png"><img class="size-medium wp-image-1497 aligncenter" title="basic-page-multilingual-settings" src="http://subject9.files.wordpress.com/2011/10/basic-page-multilingual-settings.png?w=300" alt="Multilingual settings for Basic page" width="300" height="179" /></a></p>
In the Multilingual settings, you will see several options, but the ones we're interested in are Set current language as default for new content, and Require language (Do not allow Language Neutral). This is entirely optional, but I prefer to have everything defined, so I would enable these two options. Click Save content type and we can move on.
<h4>Adding translations</h4>
So now when you create a new Basic page, it will now have a language selection drop-down menu so you can select the language you wish to use for that post. And when you publish that post, you will be greeted with a Translate tab and links to the translated version of the same post at the foot of the content.

<a href="http://subject9.files.wordpress.com/2011/10/add-translation.png"><img class="aligncenter size-medium wp-image-1476" title="add-translation" src="http://subject9.files.wordpress.com/2011/10/add-translation.png?w=300" alt="Adding Translation" width="300" height="211" /></a>

Click the Translate tab, and you will be redirected to the Translations page for the selected content. Let's say I have a blog post (or article) in English, and I have Latvian and Russian set up as my additional languages. When I access the Translations page, I will see that the English version, being my default language, is already translated, but there are no translations for Latvian and Russian. That's because I haven't translated it yet. So to translate a content, simply click on the add translation link and you will be redirected to a Create article page, only this time you can't select the language from the drop-down menu. Finish your translation and click the Save button to save the translation.
<p style="text-align:center;"><a href="http://subject9.files.wordpress.com/2011/10/language-drop-down.png"><img class="aligncenter" title="language-drop-down" src="http://subject9.files.wordpress.com/2011/10/language-drop-down.png?w=300" alt="Drop-down Language Selector" width="300" height="179" /></a></p>
Now you should be redirected to the translated page (let's say Latvian) and you should see a link to the English translation at the foot of the content. You should also notice the URL has changed, and should have the two-letter language abbreviation after the domain name, for example http://baseweb.cave/lv/blog/dienasgrāmatas-ieraksts. You should also notice that on most modern browsers (not IE) the URL may contain special characters. Pretty cool huh? Especially when you use languages like Russian and Japanese. But I'm not sure whether this will have adverse effects on search engines and I prefer to keep my URLs free from non-Latin characters anyway.

<a href="http://subject9.files.wordpress.com/2011/10/english-translation-link.png"><img class="aligncenter size-medium wp-image-1485" title="english-translation-link" src="http://subject9.files.wordpress.com/2011/10/english-translation-link.png?w=300" alt="Latvian translation and English translation link" width="300" height="174" /></a>

Oh and in case you're wondering how to get rid of that pesky links at the foot of your content, navigate to Administration » Configuration » Regional and language » Multilingual settings and select the Node options tab. Now check Hide content translation link, click Save configuration and you're done. Now you visitors will have to manually add the two-letter language abbreviation to the URL to view your content in the desired language. Thankfully, however, you can use the Language switcher block to help alleviate this problem. More on this later.

<a href="http://subject9.files.wordpress.com/2011/10/hide-content-translation-link.png"><img class="aligncenter size-medium wp-image-1486" title="hide-content-translation-link" src="http://subject9.files.wordpress.com/2011/10/hide-content-translation-link.png?w=300" alt="Hiding content translation links" width="300" height="176" /></a>
<h4>Multilingual front page</h4>
OK, now that you got everything up and running, it's time to create a multilingual front page. Navigate to Administration » Configuration » Regional and language » Multilingual settings and select the Variable tab. Locate Default front page from the list of variables to be translated, and check it. This will allow you to add translations to the front page.

<a href="http://subject9.files.wordpress.com/2011/10/variable-default-front-page.png"><img class="aligncenter size-medium wp-image-1496" title="variable-default-front-page" src="http://subject9.files.wordpress.com/2011/10/variable-default-front-page.png?w=300" alt="Enable default front page" width="300" height="179" /></a>

Next, create a Basic page that will be your front page, and add a translation for every language you have enabled. Now in the URL path settings, disable Automatic alias and give every page the same alias, for example, home. Save the page.

<a href="http://subject9.files.wordpress.com/2011/10/disable-auto-alias.png"><img class="aligncenter size-medium wp-image-1479" title="disable-auto-alias" src="http://subject9.files.wordpress.com/2011/10/disable-auto-alias.png?w=300" alt="Disable automatic alias" width="300" height="179" /></a>

Now navigate to Administration » Configuration » System and you should now see a multilingual box at the top. Forget that for now, and scroll down until you find Default front page under Front page. Set the alias to the front page you've previously created, in this case, home.

<a href="http://subject9.files.wordpress.com/2011/10/set-front-page-alias.png"><img class="aligncenter size-medium wp-image-1493" title="set-front-page-alias" src="http://subject9.files.wordpress.com/2011/10/set-front-page-alias.png?w=300" alt="Set default front page" width="300" height="179" /></a>

Next is the tricky bit. Remember the language selector at the top of the page? Well, in a perfect world clicking another language from the list means you can edit the site parameters for that language, in this case set the default front page for the selected language. But the world isn't perfect, and so selecting a different language from the list and changing the options will do nothing. Instead, change the URL to include the two-letter language abbreviation. For example, if the Site information URL for the default language is www.example.com/admin/config/system/site-information, the URL for the Russian language is www.example.com/ru/admin/config/system/site-information.

Once this page loads up, edit it like you did for the default language and save. Check whether the predefined URL for the default front page has the two-letter language abbreviation or not.

<a href="http://subject9.files.wordpress.com/2011/10/set-front-page-alias-lv.png"><img class="aligncenter size-medium wp-image-1498" title="set-front-page-alias-lv" src="http://subject9.files.wordpress.com/2011/10/set-front-page-alias-lv.png?w=300" alt="Set default front page for Latvian" width="300" height="179" /></a>

Next access your front page in the set language and hey presto, you should see the translated version of your front page. You have now successfully set up a multilingual front page.
<h4>Language selector</h4>
If you have disabled the translation links for your contents, chances are you'll want a way for your visitors to see your contents in the desired languages without them having to modify the URL. You're in luck, because the Internationalization module comes with a language switcher block that you can place anywhere on your page.

<a href="http://subject9.files.wordpress.com/2011/10/language-switcher-block.png"><img class="aligncenter size-medium wp-image-1488" title="language-switcher-block" src="http://subject9.files.wordpress.com/2011/10/language-switcher-block.png?w=300" alt="Language switcher block" width="300" height="179" /></a>

To enable this, navigate to Administration » Structure » Blocks and locate the Language switcher block. Select the region in which this block will appear, click Save blocks, and you're done. You can now switch between languages by selecting the desired language from the language switcher.

<a href="http://subject9.files.wordpress.com/2011/10/language-switcher-links.png"><img class="aligncenter size-medium wp-image-1489" title="language-switcher-links" src="http://subject9.files.wordpress.com/2011/10/language-switcher-links.png?w=300" alt="Language switcher links" width="300" height="174" /></a>
<h4>Final words</h4>
This has been a long tutorial, but I think I got most things covered. The Internationalization module is awesome, and incredibly useful if you need a multilingual website, and not to mention easy to use. You can apply multilingual capabilities to pretty much any type of content, and switch between languages using the language switcher.

As usual, this tutorial is mainly for my future reference, but I hope it will also help anyone attempting to create a multilingual Drupal site. Please let me know if I've missed anything, as well as errors, in the comments.

<em>Wassalam</em>
