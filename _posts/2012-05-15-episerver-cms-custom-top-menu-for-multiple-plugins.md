---
layout: post
title: "EPiServer CMS: Custom top menu for multiple plugins"
date: 2012-05-15 14:09
comments: true
categories: [C#, custom top menu, EPiServer, episerver, mutliple plugins, Web Development]
---
I've been working on this small plugin for EPiServer CMS 6, and I wanted to add it to a custom top menu created by another plugin that I had helped develop. I wanted to append the link for my plugin into the same top menu as that other plugin. The only requirement is that my plugin will still display the same top menu even if the other plugin is not installed.

Unfortunately, after a lot of time Googling and forum crawling, I came to the conclusion that it was just not possible to append an item to a top menu dynamically. I didn't bother looking at solutions that involved me editing the project's web.config file, which may have helped, since the other plugin already had some code which adds a top menu item to the top menu bar. So I figured I should probably try to use that instead.

<!--more-->

This is what I started with;

```csharp
namespace MyPlugin
{
    [MenuProvider]
    public class MenuProvider : IMenuProvider
    {
        private const string RootMenuUri = "/Modules/MyPlugin";

        #region IMenuProvider Members

        public IEnumerable<MenuItem> GetMenuItems()
        {
            string parentPath = MenuPaths.Global + "/mysection";

            var mainMenu = new SectionMenuItem("MySection", parentPath) { IsAvailable = CheckAccess };
            var adminItem = new UrlMenuItem("MyPlugin", parentPath + "/myplugin",
                                            RootMenuUri + "/Admin/Default.aspx")
                                {
                                    IsAvailable = CheckAccess
                                };

            return new List<MenuItem> { mainMenu, adminItem };
        }

        #endregion

        protected bool CheckAccess(RequestContext requestContext)
        {
            if (PrincipalInfo.Current != null)
            {
                return PrincipalInfo.Current.HasPathAccess(RootMenuUri) || PrincipalInfo.HasAdminAccess;
            }

            return false;
        }
    }
}
```

As you could probably already tell, I wanted to have MyPlugin show up as a sub-item of MySection. I figured the best way to do this is to first get a list of menu items and check if MySection already exists, and add it to the list if it doesn't. So I did something like this;

```csharp
IEnumerable<MenuItem> sectionMenuItems = new MenuAssembler().GetMenuItems(parentPath, 1);
if (!sectionMenuItems.Any())
{
    var mainMenu = new SectionMenuItem("MySection", parentPath) { IsAvailable = CheckAccess };
    menuItems.Add(mainMenu);
}
```

Unfortunately, this didn't help. sectionMenuItems returned 0 during my debugging sessions, and I ran quite a few of them to make sure. Apparently, the MenuAssembler class would just create a new instance every time it's called, which was why I failed to get anything. One of the guys at the EPiServer forums told me that it was just not possible to merge menu items, and I have two options for what I'm trying to do;
<ol>
	<li>Use HttpContext or similar so set a static flag that the top menu item has been created.</li>
	<li>Add the top menu item in web.config in your NuGet setup.</li>
</ol>

I went with option one.

So this is what my code ended up looking like;

```csharp
namespace MyPlugin
{
    [MenuProvider]
    public class MenuProvider : IMenuProvider
    {
        private const string RootMenuUri = "/Modules/MyPlugin";

        #region IMenuProvider Members

        public IEnumerable<MenuItem> GetMenuItems()
        {
            var menuItems = new List<MenuItem> {};
            const string parentPath = MenuPaths.Global + "/mysection";
            HttpContext context = HttpContext.Current;

            if (!Convert.ToBoolean(context.Items["MyTopMenuIsSet"]))
            {
                var mainMenu = new SectionMenuItem("MySection", parentPath) { IsAvailable = CheckAccess };
                menuItems.Add(mainMenu);
                context.Items["MyTopMenuIsSet"] = true;
            }

            var adminItem = new UrlMenuItem("MyPlugin",
                                            parentPath + "/myplugin",
                                            RootMenuUri + "/Admin/Default.aspx") {IsAvailable = CheckAccess};
            menuItems.Add(adminItem);

            return menuItems;
        }

        #endregion

        protected bool CheckAccess(RequestContext requestContext)
        {
            if (PrincipalInfo.Current != null)
            {
                return PrincipalInfo.Current.HasPathAccess(RootMenuUri) || PrincipalInfo.HasAdminAccess;
            }

            return false;
        }
    }
}
```

Essentially what this does is simply checking whether the MyTopMenuIsSet variable is set or not, and if it's not it means the top menu has not been added yet, so the code for adding a top menu is executed. Otherwise, it will simply append the link to the plugin to the existing top menu.

I hope this helps anyone else facing this issue. If you've solved this issue using a different method, I'm interested to know how you did it.

<em>Wassalam</em>
