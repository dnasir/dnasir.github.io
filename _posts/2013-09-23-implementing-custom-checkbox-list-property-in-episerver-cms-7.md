---
layout: post
title: Implementing Custom Checkbox List Property in EPiServer CMS 7
date: 2013-09-23 12:47

comments: true
categories: [ASP.NET, C#, EPiServer 7, Web Development]
---
<img src="http://www.dnasir.com/wp-content/uploads/2013/09/logo-EPiServer-Ptr.png" alt="EPiServer Logo" width="666" height="178" class="alignnone size-full wp-image-2116" />

In one of my recent projects, I've had to implement a property that displays a list of checkboxes so the administrator could select an item to enable from the list of available options. I googled around for a bit and came across this <a href="http://world.episerver.com/Blogs/Linus-Ekstrom/Dates/2012/9/EPiServer-7-Configuring-editors-for-your-properties/" target="_blank">article by Linus Ekström.</a>

This is my implementation.

[csharp]
[EditorDescriptorRegistration(TargetType = typeof(string), UIHint = "TransportTypes")]
public class TransportTypeEditorDescriptor : EditorDescriptor
{
    public override void ModifyMetadata(ExtendedMetadata metadata, IEnumerable<Attribute> attributes)
    {
        SelectionFactoryType = typeof(TransportTypesFactory);
        ClientEditingClass = "epi-cms.contentediting.editors.CheckBoxListEditor";
        base.ModifyMetadata(metadata, attributes);
    }
}
public class TransportTypesFactory : ISelectionFactory
{
    public IEnumerable<ISelectItem> GetSelections(ExtendedMetadata metadata)
    {
        var languages = new List<SelectItem>
            {
                    new SelectItem { Value = "train", Text = "Train" },
                    new SelectItem { Value = "bus", Text = "Bus" },
                    new SelectItem { Value = "ferry", Text = "Ferry" }
            };
        return languages;
    }
}
[/csharp]

You can then implement this in your class like so:

[csharp]
[Display(GroupName = SystemTabNames.Content, Order = 20)]
[UIHint("TransportTypes")]
public virtual string TransportTypes { get; set; }
[/csharp]

And you'll end up with something like this:

<a href="http://www.dnasir.com/wp-content/uploads/2013/09/custom-checkboxlist-property.png" rel="lightbox"><img src="http://www.dnasir.com/wp-content/uploads/2013/09/custom-checkboxlist-property.png" alt="Custom checkboxlist property" width="800" height="202" class="alignnone size-full wp-image-2114" /></a>

<strong>Note to CMS 7.1 developers!</strong>

As pointed out by Linus in <a href="http://world.episerver.com/Forum/Developer-forum/EPiServer-7-CMS/Thread-Container/2013/9/How-to-use-PropertyCheckBoxList/" target="_blank">this forum post</a>, you'll need to replace this line:

[csharp]
ClientEditingClass = "epi-cms.contentediting.editors.CheckBoxListEditor";
[/csharp]

with this:

[csharp]
ClientEditingClass = "epi.cms.contentediting.editors.CheckBoxListEditor";
[/csharp]

This doesn't affect CMS 7.2, however. But it obviously begs the question, WHY?
