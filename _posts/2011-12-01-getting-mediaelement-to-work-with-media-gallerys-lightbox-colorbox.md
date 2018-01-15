---
layout: post
title: "Getting MediaElement to work with Media Gallery's lightbox (Colorbox)"
date: 2011-12-01 23:35
comments: true
categories: [colorbox, Drupal 7, drupal 7, JavaScript, lightbox, media gallery, mediaelement, player not loading, Tutorials, Web Development]
---
OK, here's a quick one for you. Say you're setting up a media gallery in Drupal using the Media Gallery module. Say you want to include videos along with your images in said gallery. Say you want to use Media Gallery's lightbox feature to allow your visitors to view your media in a nice pop-up JavaScript box. Say you want to allow your visitors to play your videos in said box using the MediaElement module.

Well, if you're reading this, that means you're facing the same problem as I did a few hours ago. The player just fails to load, and you're left with something that looks like this;

<a href="http://www.dnasir.com/wp-content/uploads/2011/12/mediaelement-player-load-fail.png"><img class="size-full wp-image-1628 alignnone" title="MediaElement Player Load Fail" src="http://www.dnasir.com/wp-content/uploads/2011/12/mediaelement-player-load-fail.png" alt="" width="590" height="417" /></a>

<!--more-->

This is because Media Gallery's lightbox (which is really Colorbox) loads the video via Ajax, and only when the user requests it. So it's a dynamically loaded element, which is not picked up by MediaElement's jQuery module as it only scans the page during page load.

Solution? Customized JavaScript, of course.

Create a new JavaScript file called colorbox-behaviour.js. Naturally, the file name can be anything you want. Now you need to add the following code in your new script.

```javascript
(function ($) {
    Drupal.behaviors.initColorboxDefaultStyle = {
        attach: function (context, settings) {
            $(document).bind('cbox_complete', function () {
                // Enables MediaElement player inside Colorbox popup.
                // Required because video element is added dynamically and original MediaElement JS only scans the page during page load.
                $('video,audio', context).once('mediaelement', function() {
                    var player_container = $(this);
                    player_container.mediaelementplayer();
                    // fix for IE so whole video screen shows up even if video fails to load
                    $.colorbox.resize({
                        innerWidth: player_container.attr('width'),
                        innerHeight: player_container.attr('height')
                    });
                });
            });
        }
    };
})(jQuery);
```

Now you need to include this script in your theme's `template.php` file under the `theme_preprocess_page` function, like so.

```php
function isl_preprocess_page(&$variables, $hook) {
    /* Add custom Colorbox script */
    drupal_add_js(drupal_get_path('theme', 'THEMENAME') . '/js/colorbox-behaviour.js', array('group' =&gt; 'JS_THEME', 'type' =&gt; 'file'));
}
```

Remember to replace `THEMENAME` with your theme's name.

That should be it. Your MediaElement player should now load in your Media Gallery's lightbox.

<a href="http://www.dnasir.com/wp-content/uploads/2011/12/mediaelement-player-load-success.png"><img class="alignnone size-full wp-image-1629" title="MediaElement Player Load Success" src="http://www.dnasir.com/wp-content/uploads/2011/12/mediaelement-player-load-success.png" alt="" width="590" height="402" /></a>

Happy coding, and <em>wassalam.</em>
