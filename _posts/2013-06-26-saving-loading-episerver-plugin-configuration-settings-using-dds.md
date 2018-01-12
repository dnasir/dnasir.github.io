---
layout: post
title: Saving/loading EPiServer plugin configuration settings using DDS
date: 2013-06-26 16:53
comments: true
categories: [crud, dds, EPiServer, EPiServer 6, episerver plugin, Web Development]
---
I'm currently working on a small EPiServer plugin that requires the site admin to be able to save/load configuration settings from the Admin page.

Fortunately, you can easily do this using EPiServer's Dynamic Data Store (DDS), and it beats having to implement PlugInSettings that uses DataSets, which is just awfully painful to work with.

Firstly, you'll need to decorate the model class that you want to store. For example, if you want to store some plugin settings like feature enable/disable flags, you'll want to create a model class that looks something like this:

[csharp]
using EPiServer.Data;
using EPiServer.Data.Dynamic;

namespace MyPlugin.Models
{
    [EPiServerDataStore(AutomaticallyRemapStore = true, AutomaticallyCreateStore = true)]
    public class SettingsModel
    {
        public Identity Id { get; set; }

        public bool Enabled { get; set; }
    }
}
[/csharp]

The decoration we've applied to this class tells EPiServer to automatically check and update the Type if it doesn't match, which should help when changing property types, and to automatically create the store if it doesn't already exist.

One other thing you'll need to include in the class is an Identity type property. This is required, and it won't work without it.

Next, you need to create a repository that will handle all CRUD operation. Something like this:

[csharp]
using System;
using System.Linq;
using EPiServer.Data.Dynamic;
using MyPlugin.Models;

namespace MyPlugin.Repositories
{
    public class SettingsRepository
    {
        private static SettingsRepository _instance;

        public SettingsRepository()
        {
        }

        public static SettingsRepository Instance
        {
            get { return _instance ?? (_instance = new SettingsRepository()); }
        }

        private static DynamicDataStore Store
        {
            get { return typeof(SettingsModel).GetStore(); }
        }

        public bool SaveSettings(SettingsModel settings)
        {
            try
            {
                var currentSettings = LoadSettings();
                Store.Save(settings, currentSettings.Id);
            }
            catch (Exception)
            {
                return false;
            }
            return true;
        }

        public SettingsModel LoadSettings()
        {
            var currentSettings = Store.Items<SettingsModel>().FirstOrDefault();
            if (currentSettings == null)
            {
                currentSettings = new SettingsModel();
                Store.Save(currentSettings);
            }
            return currentSettings;
        }
    }
}
[/csharp]

I think the code above is pretty much self-explanatory. The repository class has a static property of type DynamicDataStore which has a getter that returns the DynamicDataStore object, that is used in the two methods set up for setting and fetching the plugin settings from the DDS. The LoadSettings() method will try to fetch the plugin settings, and if it doesn't exist, it will create a new entry. The SaveSettings() method will first fetch the settings using the LoadSettings() method, and then overwrites the values using the Save() method.

I'm also using the Singleton design pattern (Thanks <a href="http://www.frederikvig.com/" target="_blank">Frederik Vig</a> for the tip!) to avoid a possible race condition between two threads updating the same object.

On the plugin administration page, you can have a checkbox input control (since we're using a boolean in this example) that will represent the value for the Enabled property and we can use the Checked property for that control to define whether the plugin is to be enabled or disabled. The code-behind for the administration page could look something like this:

[csharp]
using System;
using EPiServer;
using MyPlugin.Repositories;

namespace MyPlugin.Admin
{
    public partial class MyPluginAdminPage : SimplePage
    {
        protected override void OnLoad(EventArgs e)
        {
            base.OnLoad(e);

            var currentSettings = SettingsRepository.Instance.LoadSettings();

            this.PluginEnabled.Checked = currentSettings.Enabled;
        }

        protected void formSubmitBtn_Click(object sender, EventArgs e)
        {
            var currentSettings = SettingsRepository.Instance.LoadSettings();

            currentSettings.Enabled = this.EnableCookieConsent.Checked;

            if (SettingsRepository.Instance.SaveSettings(currentSettings))
            {
                Success.Visible = true;
            }
            else
            {
                Failure.Visible = true;
            }
        }
    }
}
[/csharp]

In the code above, we have an OnLoad event handler that loads up the currently saved settings, and sets them to the relevant controls. In this case, the boolean value is being applied to the checkbox input control.

Then we have a click event handler that gets triggered when the user clicks the Save button. This event handler will fetch the currently saved configuration settings, and updates them according to the values found in our web controls. Finally, it saves the settings and on success it displays the success notification message. If it fails for some reason, it will display the failure notification message. Of course, it's entirely up to you how you want to implement this.

So there you have it. A simple way to save and load configuration settings for EPiServer plugins. The only caveat here is that when the plugin is uninstalled, its entry in the DDS will remain. I'm sure there's a way to clean up, but that's another topic for another day.

I should mention that this method was aimed at EPiServer 6, but it should also work in EPiServer 7, seeing as how the DDS implementation hasn't changed that much from version 6 to 7. I'll update this post when I find the time to verify.

Until next time, <em>wassalam</em>.
