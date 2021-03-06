---
layout: post
title: MVC in Telerik Mobile Appbuilder
date: 2014-08-31 23:25

comments: true
categories: [Mobile App Development, mvc, requirejs, telerik appbuilder]
---
Recently I started playing around with the [Telerik Platform Mobile Appbuilder](http://www.telerik.com/appbuilder "Link to Telerik Platform Mobile Appbuilder"){:target="_blank"} and tried my hand at building mobile apps. It's an awesome tool, and despite its flaws, most of which are negligible, it does provide developers a viable avenue to quickly build mobile apps. The best part is, I get to code in HTML5 and JavaScript.

For the most parts, I was working with [Kendo UI](http://www.telerik.com/kendo-ui "Link to Kendo UI"){:target="blank"}, Telerik's own front-end UI framework. It took some time to work out what's what, but since it was all HTML and JavaScript, I was up and running in no time.

I started experimenting, and tried to implement an ASP.NET MVC approach to building my project.

<!--more-->

Firstly, I structured my project similar to that found in a standard ASP.NET MVC project. So I have the models, views and controllers folders, which contains the appropriate modules.

![Appbuilder project structure]({{ "/assets/img/2014/08/project-structure.png" | absolute_url }})

I'm using [RequireJS](http://requirejs.org "Link to RequireJS"){:target="blank"} in my project to dynamically add HTML and JavaScript code as required. It also allows me to define dependencies for my modules, so that when I'm loading the main view, I don't have to load the code for the Settings page, for instance.

Now, let's have a look at how I set up my models. I define my models in individual files, just like I would in ASP.NET MVC, and using RequireJS, I can set it up as a dependency for any modules that uses it.

```javascript
// User model
define([], function() {
    return kendo.data.Model.define({
        id: 'id',
        fields: {
            'name': {
                type: 'string'
            },
            'age': {
                type: 'number'
            }
        }
    });
})
```

As you can see, all this module does is returning a Kendo model. That's it.

At this point, you might be wondering, what about nested models? At least I was. Here's how I did it.

```javascript
// Comment model
define([
    'models/user'
], function(User) {
    return kendo.data.Model.define({
        id: 'id',
        fields: {
            'comment': {
                type: 'string'
            },
            'posted': {
                type: 'string'
            },
            'author': User
        }
    });
});
```

Next, let's have a look at how I'm using these models that I've created. In my demo project, I'm using my models as schemas in my service calls. It will ensure that the data I'm fetching is mapped according to the structure and property types defined in the model.

```javascript
// Main controller
define([
    'uiHelper',
    'text!views/main.html',
    'models/comment'
], function(uiHelper, template, Comment) {
    var model = {
        comments: new kendo.data.DataSource({
            transport: {
                read: {
                    url: 'api/comments',
                    dataType: 'json'
                }
            },
            pageSize: 10,
            serverPaging: true,
            schema: {
                model: Comment
            }
        })
    };

    uiHelper.InitModule('main', template, model);
});
```

If you remember how I had set up the Comment model, it has a property that is of type User. Kendo should automatically map the author values to the appropriate property fields. To demonstrate this, let's have a look at the view.

```html
<div data-role="view" id="main" data-init="APP.main.init">
    <ul data-role="listview" data-source="APP.main.model.comments" data-template="commentTmpl"></ul>
</div>

<script type="text/x-kendo-tmpl" id="commentTmpl">
    <div>
        ${comment}
        Posted: ${posted} by ${author.name} (${author.age})
    </div>
</script>
```

It's all pretty self-explanatory. I have a ListView that I want filled with comments from my datasource, and rendered using the provided template.

Running the project in the simulator produced this:

![Appbuilder simulator]({{ "/assets/img/2014/08/project-simulator.png" | absolute_url }})

As you can see, everything is working as expected. However, I'd like to point out that even though I've defined what the property type should be, it seems like Kendo is completely ignoring this during mapping. I was expecting it to show some sort of error in the console, but it didn't. Maybe I'm doing something wrong.

As always, there are other ways to set up an Appbuilder project. This is just my take on it. I like how ASP.NET MVC projects are structured, and I didn't see why it couldn't be implemented in other languages.

You can find my demo app [here](https://bitbucket.org/dzul1983/telerik-mobile-appbuilder-app-base "Link to demo app repo"){:target="_blank"}.  I'll be improving it as I continue my journey into mobile app building using the Telerik Mobile Appbuilder.

*Wassalam*
