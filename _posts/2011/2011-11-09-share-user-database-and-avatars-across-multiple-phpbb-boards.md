---
layout: post
title: Share User Database And Avatars Across Multiple phpBB Boards
date: 2011-11-09 03:12
comments: true
categories: [multiple forum installations, PHP, phpBB, phpbb, phpbb3, Programming, shared avatar, shared user database, Tutorials, Web Development]
---
I got a request from one of my clients asking me to create a forum for his website. phpBB should work just fine, I thought. But here's the catch. It needs to support three languages, namely Latvian, Russian and English. So I proposed a simple language switcher mod which simply switches the interface language to whichever language the user selects. The main forum page will look the same. But he didn't want all the forums to be on the same page. He wanted the Latvian board to only contain Latvian forums, and the same for Russian and English. His explanation was that if we did what I was proposing we do, the Russian and English forum categories will be pushed to the bottom of the page, and if a Russian or an English-speaking person were to visit the board, he/she would not see the forum in their language, and would immediately leave the site.

So after hours of research, I came across this [topic](http://www.phpbb.com/community/viewtopic.php?f=46&amp;t=562513){:target="_blank"} explaining how to share the user database across multiple phpBB installations. I'm no phpBB guru, so this was a simple enough solution for me. However I did come across a couple of issues while I was working on this, which I will explain in this tutorial.

<!--more-->

Although I'm using multiple boards to accommodate multiple forums for multiple languages, you can use the same steps for multiple boards of any kind. For example, if you own two gaming website forums, you may want your users to be able to access both forums without them having to register twice, because we all know how annoying it is having to register for anything. Especially in some cases where you need to register just to view forum topics. Bloody annoying!

## Prerequisites

In this tutorial I'll be using a modified version of phpBB 3.0.9, specifically the [phpBB SEO Premod V 3.0.9](href="http://downloads.phpbb-seo.com/seo-url-premod-12/phpbb-seo-premod-48.html "phpBB SEO Premod V 3.0.9"){:target="_blank"}, but I assume the same steps will work on other phpBB3 versions as well.

## Installing the first forum

First thing you have to do is install phpBB. This will be your primary installation. Pretty much a straightforward installation, with the only requirement being that you should set a prefix for the phpBB tables. This is because you will be using the same database for all other installations.

## Editing the phpBB database

Next comes the part where you edit the board's database. It would be useful at this point if your host has something like phpMyAdmin installed. Otherwise I'm assuming you know how to make these changes via MySQL (or whatever database system you're using) command line.

Remove the prefix for all of the below tables;

* `banlist`
* `bbcodes`
* `disallow`
* `groups`
* `lang`
* `privmsgs`
* `privmsgs_folder`
* `privmsgs_rules`
* `privmsgs_to`
* `ranks`
* `sessions`
* `sessions_keys`
* `smilies`
* `styles`
* `styles_imageset`
* `styles_imageset_data`
* `styles_template`
* `styles_template_data`
* `styles_theme`
* `users`
* `user_group`
* `warnings`
* `words`

## Pointing phpBB in the right direction, er.. tables

Now we need to tell phpBB to use the tables we have just modified. Fire up your favourite text editor. I'm using Notepad++ for this. Open `includes/constants.php` located inside your phpBB directory, and locate the part where the tables are defined. It should look something like this;

```php
// Table names
define('ACL_GROUPS_TABLE',          $table_prefix . 'acl_groups');
define('ACL_OPTIONS_TABLE',         $table_prefix . 'acl_options');
define('ACL_ROLES_DATA_TABLE',      $table_prefix . 'acl_roles_data');
define('ACL_ROLES_TABLE',           $table_prefix . 'acl_roles');
define('ACL_USERS_TABLE',           $table_prefix . 'acl_users');
define('ATTACHMENTS_TABLE',         $table_prefix . 'attachments');
define('BANLIST_TABLE',             $table_prefix . 'banlist');
define('BBCODES_TABLE',             $table_prefix . 'bbcodes');
define('BOOKMARKS_TABLE',           $table_prefix . 'bookmarks');
define('BOTS_TABLE',                $table_prefix . 'bots');
define('CONFIG_TABLE',              $table_prefix . 'config');
define('CONFIRM_TABLE',             $table_prefix . 'confirm');
define('DISALLOW_TABLE',            $table_prefix . 'disallow');
define('DRAFTS_TABLE',              $table_prefix . 'drafts');
define('EXTENSIONS_TABLE',          $table_prefix . 'extensions');
define('EXTENSION_GROUPS_TABLE',    $table_prefix . 'extension_groups');
define('FORUMS_TABLE',              $table_prefix . 'forums');
define('FORUMS_ACCESS_TABLE',       $table_prefix . 'forums_access');
define('FORUMS_TRACK_TABLE',        $table_prefix . 'forums_track');
define('FORUMS_WATCH_TABLE',        $table_prefix . 'forums_watch');
define('GROUPS_TABLE',              $table_prefix . 'groups');
define('ICONS_TABLE',               $table_prefix . 'icons');
define('LANG_TABLE',                $table_prefix . 'lang');
define('LOG_TABLE',                 $table_prefix . 'log');
define('LOGIN_ATTEMPT_TABLE',       $table_prefix . 'login_attempts');
define('MODERATOR_CACHE_TABLE',     $table_prefix . 'moderator_cache');
define('MODULES_TABLE',             $table_prefix . 'modules');
define('POLL_OPTIONS_TABLE',        $table_prefix . 'poll_options');
define('POLL_VOTES_TABLE',          $table_prefix . 'poll_votes');
define('POSTS_TABLE',               $table_prefix . 'posts');
define('PRIVMSGS_TABLE',            $table_prefix . 'privmsgs');
define('PRIVMSGS_FOLDER_TABLE',     $table_prefix . 'privmsgs_folder');
define('PRIVMSGS_RULES_TABLE',      $table_prefix . 'privmsgs_rules');
define('PRIVMSGS_TO_TABLE',         $table_prefix . 'privmsgs_to');
define('PROFILE_FIELDS_TABLE',      $table_prefix . 'profile_fields');
define('PROFILE_FIELDS_DATA_TABLE', $table_prefix . 'profile_fields_data');
define('PROFILE_FIELDS_LANG_TABLE', $table_prefix . 'profile_fields_lang');
define('PROFILE_LANG_TABLE',        $table_prefix . 'profile_lang');
define('RANKS_TABLE',               $table_prefix . 'ranks');
define('REPORTS_TABLE',             $table_prefix . 'reports');
define('REPORTS_REASONS_TABLE',     $table_prefix . 'reports_reasons');
define('SEARCH_RESULTS_TABLE',      $table_prefix . 'search_results');
define('SEARCH_WORDLIST_TABLE',     $table_prefix . 'search_wordlist');
define('SEARCH_WORDMATCH_TABLE',    $table_prefix . 'search_wordmatch');
define('SESSIONS_TABLE',            $table_prefix . 'sessions');
define('SESSIONS_KEYS_TABLE',       $table_prefix . 'sessions_keys');
define('SITELIST_TABLE',            $table_prefix . 'sitelist');
define('SMILIES_TABLE',             $table_prefix . 'smilies');
define('STYLES_TABLE',              $table_prefix . 'styles');
define('STYLES_TEMPLATE_TABLE',     $table_prefix . 'styles_template');
define('STYLES_TEMPLATE_DATA_TABLE',$table_prefix . 'styles_template_data');
define('STYLES_THEME_TABLE',        $table_prefix . 'styles_theme');
define('STYLES_IMAGESET_TABLE',     $table_prefix . 'styles_imageset');
define('STYLES_IMAGESET_DATA_TABLE',$table_prefix . 'styles_imageset_data');
define('TOPICS_TABLE',              $table_prefix . 'topics');
define('TOPICS_POSTED_TABLE',       $table_prefix . 'topics_posted');
define('TOPICS_TRACK_TABLE',        $table_prefix . 'topics_track');
define('TOPICS_WATCH_TABLE',        $table_prefix . 'topics_watch');
define('USER_GROUP_TABLE',          $table_prefix . 'user_group');
define('USERS_TABLE',               $table_prefix . 'users');
define('WARNINGS_TABLE',            $table_prefix . 'warnings');
define('WORDS_TABLE',               $table_prefix . 'words');
define('ZEBRA_TABLE',               $table_prefix . 'zebra');
```

Now you need to remove the `$table_prefix` variable for the tables we have just modified. Basically you just need to remove the prefix for all the tables that you're going to share. Since each phpBB installation will have its own prefix (provided you include a prefix as per my instructions) it's easier to just remove the prefix for all the shared tables.

```php
// Table names
...
define('BANLIST_TABLE',             'banlist');
define('BBCODES_TABLE',             'bbcodes');
...
define('DISALLOW_TABLE',            'disallow');
...
define('GROUPS_TABLE',              'groups');
...
define('LANG_TABLE',                'lang');
...
define('PRIVMSGS_TABLE',            'privmsgs');
define('PRIVMSGS_FOLDER_TABLE',     'privmsgs_folder');
define('PRIVMSGS_RULES_TABLE',      'privmsgs_rules');
define('PRIVMSGS_TO_TABLE',         'privmsgs_to');
...
define('RANKS_TABLE',               'ranks');
...
define('SESSIONS_TABLE',            'sessions');
define('SESSIONS_KEYS_TABLE',       'sessions_keys');
...
define('SMILIES_TABLE',             'smilies');
define('STYLES_TABLE',              'styles');
define('STYLES_TEMPLATE_TABLE',     'styles_template');
define('STYLES_TEMPLATE_DATA_TABLE','styles_template_data');
define('STYLES_THEME_TABLE',        'styles_theme');
define('STYLES_IMAGESET_TABLE',     'styles_imageset');
define('STYLES_IMAGESET_DATA_TABLE','styles_imageset_data');
...
define('USER_GROUP_TABLE',          'user_group');
define('USERS_TABLE',               'users');
define('WARNINGS_TABLE',            'warnings');
define('WORDS_TABLE',               'words');
```

Save the file and let's move on.

## Installing additional phpBB forums

The steps for installing additional phpBB forums are exactly the same as for the first installation, with one exception, **you do not need to rename the database tables for additional installations**. All you have to do is edit the `includes/constants.php` file to tell your new phpBB installation to use the shared tables.

## The tricky part

Next comes the tricky stuff. phpBB uses a kind of system where it caches the user permission for each board into the users table. I'm not entirely sure how the system works, but suffice it to say that each board needs its own cache. So you will need to add additional columns to the `users` table, specifically the `user_permissions` and the `user_perm_from` fields.

What you need to do is add assign a number for each phpBB installation and then duplicate the above two fields with the addition of the installation number as its suffix. For example, if your primary forum installation is assigned the number 1, the duplicated fields will be called `user_permissions_1` and `user_perm_from_1`. This can be anything you want it to be as long as you know that it refers to this specific installation. Do the same for all other installations.

Now you need to change all reference to these fields for each phpBB installation. Open up `includes/auth.php` for each phpBB installation, and change all references to `user_permissions` and `user_perm_from` to the appropriate tables. For example, the `includes/auth.php` file for your primary phpBB installation will use `user_permissions_1` and `user_perm_from_1`, provided these tables are the ones you've assigned to this specific installation. The same goes for all of your phpBB installations, so in my case I have three forums sharing the same user database, the first one is assigned with the number one so it uses the tables `user_permissions_1` and `user_perm_from_1`. The second forum uses the tables `user_permissions_2` and `user_perm_from_2`, and the third one `user_permissions_3` and `user_perm_from_3`.

Of course, this depends entirely on what you name the tables, but for simplicity's sake, I decided to go with numbers as suggested in the forum post mentioned above.

## Cookie settings

Once you have completed the above instructions, you need to make sure all the forums are using the same cookie settings. Otherwise you'll have to re-authenticate yourself every time you access the different forum installations, thus eliminating the convenience of being able to use one account for all your forums.

By default, phpBB creates a random cookie name during installation for authentication purposes. As such, the cookie name will be different across your phpBB installations. So you need to copy the cookie name from your primary phpBB installation and set it in your other installations.

In case you have no idea how to do this, open up the **Administration Control Panel (ACP)** and you should see Cookie settings under Server configuration in the General tab. Click on that and locate Cookie name. Edit the value to match the one used by your primary phpBB installation, click Submit and you're done.

![Image of the cookies settings]({{ "/assets/img/2011/11/cookie-settings-2011-11-08-15-14-32.png" | absolute_url }})

After that you should be able to work seamlessly across all your forums. No more multiple registrations and authentications. Win-win.

## Field `user_permissions_1` doesn't have a default value [1364]

This is one small issue during registration where the above error is displayed when a new user submits the registration form. I couldn't find any solution to this problem, but suffice it to say that it's a pain in the arse if people can't register on your board.

I found a solution after taking the time to dissect the phpBB codes, and giving up halfway through.

Open up phpMyAdmin or any database manager, and locate the shared `users` table. Select all the `user_permissions` and `user_perm_from` tables and click Edit.

For the `user_permissions` fields, set the Default field to **As defined**, and leave the input box **blank**.

For the `user_perm_from` fields, set the Default field to **As defined**, and enter **0 (zero)** in the input box.

Save it and test. It should now allow new users to register without any problems. If it doesn't work for you, well I guess the problem lies elsewhere. This worked for me, so I stopped searching for a different solution there and then.

## Shared avatar directory

Now that you have a shared user database for all your forums, it makes sense to also have a shared avatar directory. Otherwise your users will have to upload their avatar to every forum, and if you're already sharing the user database, why not also share the avatar directory, right? Of course, this step is completely optional. I just thought that maybe I should mention how I achieved this part, especially since I couldn't find any tutorials on this.

First and foremost, you will need to have the ability to create symlinks on your hosting site. So if you can't do that, and your webhost refuses to assist you in this matter, you're out of luck.

Next, you need to decide where the avatar directory will be located. I read somewhere that in previous versions of phpBB, you can set the avatar directory to any directory you want. However, this is no longer the case with phpBB 3. You can only select directories under the phpBB root directory for your avatar directory. Oh, and relative paths don't work either.

So let's say you've decided that the first phpBB installation is going to hold the avatar directory for all your phpBB installations. Create a symlink in the `images` directory for your secondary installation to point to the `images/avatars` directory in your primary phpBB installation directory. You may have to remove, or rename if you're the cautious type, the existing avatars directory.

Next, configure all your phpBB installations to use `images/avatars/upload` for uploading avatars and `images/avatars/gallery` for the avatar gallery using the ACP. These paths are set by default on a fresh phpBB installation, so if you haven't been messing around with them, you shouldn't have to modify them.

![Image of avatar settings]({{ "/assets/img/2011/11/avatar-settings-2011-11-09-01-42-50.png" | absolute_url }})

Repeat for all other phpBB installations.

Now comes the most important part for sharing your avatar directory.

phpBB's avatar upload system generates a hash kinda name for all uploaded avatar files, and this name is stored in the database, which is accessed whenever a request is made for any particular avatar file. I assume this is to prevent problems with non-Latin filenames. One thing I didn't account for was the fact that phpBB also adds a randomly generated string to the beginning of the filename. This "salt" was probably generated during installation and is most likely different across all your phpBB installations. So even if you've created a symlink for the avatars directory, your secondary phpBB installation won't be able to find the avatar files you've uploaded on the primary installation since the filenames don't match.

So what you need to do now is fire up phpMyAdmin once again, and open the `config` table for your primary phpBB installation, and find the `config_name` called `avatar_salt`. Copy this value.

![Image of the config table]({{ "/assets/img/2011/11/dzul-ubuntu-localhost-dermatologs_phpbb3-phpbben_config-phpmyadmin-3-3-10deb1-2011-11-09-01-49-52.png" | absolute_url }})

Next, open up the config table for your secondary phpBB installation and locate the same entry called `avatar_salt`, and update it with the value you copied from the primary phpBB table.

Repeat for all other phpBB installations, and you're done.

## Final words

This has been a rather long tutorial, and I do hope it can help anyone else facing the same issue. I'm sure there are other ways around this, but considering my lack of expertise in dealing with phpBB, I figured that this was the simplest solution. Besides, I'm pretty sure this method has its merits, such as separate designs and configurations for each forums.

You could have numerous types of boards, such as an automotive themed one as well as a gardening themed one, and your users can all use the same login info for all of them. Each board will look and behave differently, but they all share the same user database.

You can also check out the phpBB board I created for my client on which this tutorial is based on [here](http://www.dermatologs.com/forum/){:target="_blank"}. Click on the language switcher icons located at the top right corner to switch between phpBB installations. You'll notice the language change as well as the different forums on each board.

Let me know what you think and if you know of a better (simpler) way to achieve similar results in the comments below.

*Wassalam*
