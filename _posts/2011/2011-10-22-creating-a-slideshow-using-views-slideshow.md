---
layout: post
title: "Creating A Slideshow Using Views Slideshow In Drupal 7"
date: 2011-10-22 15:50
comments: true
categories: [drupal, Drupal 7, drupal 7, tutorial, Web Development]
---
Recently I've been trying to rebuild a website I previously built for a Malaysian company using Drupal. When I first made that website, I created a small content management system from scratch using PHP. It was part of a technical exercise for me, and it was only after that that I realised how faster and easier it would have been if I had built it using ready-made content management systems (CMS) like Drupal or Joomla.

For this particular website, I needed to recreate a slideshow which is displayed on the front page. I googled for tutorials and found that the easiest way to achieve this is through the use of the Views module, in particular the Slideshow sub-module. Unfortunately, the tutorials and how-tos I came across for setting up a simple slideshow were poorly written and very difficult to understand. After I finally got my slideshow up and running, I decided that it was best for me to write this small tutorial for others who want to do the same, and also for myself as a reference note for future use in case I ever need it.

<!--more-->

## Prerequisites

In this tutorial, I'm using Drupal 7.8, although this should work with all versions of Drupal 7. If you're running an older version of Drupal, this tutorial is most likely not for you.

Then you need to install the following modules;

* Views
* Views Slideshow
* Chaos Tools
* Libraries

You will also need the following plugin;

jQuery Cycle Plugin

## Installing and Enabling required Modules

Once you have downloaded the required files, copy the module files to your sites/all/modules directory. Next, select Modules from the Admin toolbar and enable the modules you have just installed. You will need to enable the following in particular;

Image (Core)
Chaos Tools (Chaos Tools Suite)
Libraries (Other)
Views (Views)
Views Slideshow (Views)
Views Slideshow: Cycle (Views)
Views UI (Views)

Next you will need to install the jQuery plugin that is required by the slideshow module, or otherwise the slideshow won't work. Navigate to sites/all and create a directory called libraries, if it doesn't already exist. Now enter that directory and create a new directory called `jquery.cycle`, and then copy the jQuery Cycle Plugin file you've downloaded in the previous step. You need to rename the file to `jquery.cycle.all.min.js` or the Views Slideshow module will fail to find it.

## Creating a new Content Type

This step is not entirely necessary if you're good at using the Views filter, but I recommend it as it will make life easier later.

Select Structure from the Admin toolbar and then click on Content Types. Next click on Add Content Type, and enter the following information;

```
Name: Slideshow (can be anything you want)
Description: (Optional so you can fill in anything you want)
Submission Form Settings: (Leave default values)
Publishing Options: Published
Display Settings: (Uncheck all)
Comment Settings: Hidden (Leave default values for the rest)
Menu Settings: (Uncheck all)
```

After you've configured the above settings, click on **Save And Add Fields**. You have to add a new field for the images to be used in your slideshow. What you name this field is entirely up to you, but I would recommend using an easy to remember name, such as slides. You can also set where the images are saved, and the number of images you want to allow in any one post. You can set this up as you like, and I usually prefer to store images according to the node number and content type. So in this case, I would store them in a directory called slides within another directory named after the node number. Depending on how many images you wish to include in one slideshow, you can set the number of images allowed in any one post. I usually just set it to Unlimited.

After you've saved all that, you might want to get rid of the content body field, seeing as you won't be using it in your slideshows anyway. You may also want to set the Label as hidden in the Manage Display settings so you don't end up having a label above or below your slideshow named Slideshow, or whatever name you decided to call your new content type.

So basically you have just created a new content type that doesn't do anything else other than hold the images you want to include in your slideshow.

Also, at this point, you may want to create a new post of this new content type you've just created. You can fill it up with all the images you want to appear in your slideshow. You can also arrange the images according to how you want them to appear in your slideshow, with the first ones at the top and the last ones at the bottom.

## Creating a new Image Style

Now that you have created a container for your slides, you now need to define your slide sizes. This will also define the size of your slideshow window, and all your slides will fit exactly in this window. To do this, navigate to Configuration from the Admin toolbar and scroll down until you see Media, and select Image Styles.

Once the page loads, you should be able to see the preset styles - thumbnail, medium and large. Click on Add Style to create a new template for your slides.

You can give it any name you want, as long as it's easy to remember since you'll be using this when setting up the slideshow View later. Click the Create New Style button and your new image style is now created.

Now you can set the size for your slides, and you can do this by first selecting an effect type. I'd personally use the Resize and Crop effect to ensure all my slides are the same size. I mean, resizing is OK and all, but there may come a time when the images I upload are not of a typical aspect ratio and I'd end up with blank spaces on both sides on the image either vertically or horizontally.

Once you're done setting up your image style, click the Update Style button to save the changes you've made.

## Creating a new View

Now we come to the most important part of this tutorial, creating a View for your slideshow.

Select Structure from your Admin toolbar, and you should see Views in the list of Structure items. Click on it and it should take you to a list of Views you have available. Click on Add New View to start creating your new View.

Give your View a name. It can be anything you want, as long as you know which one it is. Now uncheck Create A Page, and check Create A Block. This is because you only want to create a slideshow block that you can place anywhere on your page. I won't cover how to make a slideshow page, but I'm sure you can figure it out based on the technique used to make a slideshow block.

Now give your block a title, and again, this can be anything you want. Now for the Display Format, select Slideshow for the first drop-down box, and Fields for the second. As for Items Per Page, you can set this to anything you want. This will basically limit how many items are allowed in one slide "page" and if the number of images exceed this number, your slideshow will be paginated. So if you want all your slides to appear in one large slideshow, give this value a large enough number, like 100 or so.

Click the Continue &amp; Edit button to continue creating your slideshow block.
