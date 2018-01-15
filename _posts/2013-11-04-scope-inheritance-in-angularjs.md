---
layout: post
title: Scope Inheritance in AngularJS
date: 2013-11-04 19:43

comments: true
categories: [AngularJS, angularjs, JavaScript, javascript, scope inheritance, Uncategorized, Web Development]
---
As I continue my journey into the Angular world, I come across many new and awesome things. One very interesting thing Angular has got going for it is the use of scopes.

Scope inheritance in Angular works very much like JavaScript's prototypal inheritance. Child scopes have access to their respective parent scopes, changes in the parent scope can trigger event listeners in the child scope, so on and so forth. It's pretty straight forward, up until the moment you need to do some "two-way binding" from within the child scope.

<!--more-->

Let's look at an example. Say you have code that looks something like this:

```html
<section ng-controller="MainCtrl">
    <div ng-controller="FilterCtrl">
        <input type="text" ng-model="criteria" />
        <button ng-click="applyFilter()">Apply filter</button>
    </div>
    <ul>
        <li ng-repeat="person in people">
            {{person.name}}, {{person.jobTitle}}, {{person.companyName}}
        </li>
    </ul>
</section>
```

```javascript
angular.module('myApp', [])
    .controller('MainCtrl', function($scope) {
        $scope.people = [];
    })
    .controller('FilterCtrl', function($scope, PeopleService) {
        $scope.criteria = '';
        $scope.applyFilter = function() {
            // Some async call that returns a list of names
            PeopleService.getPeople($scope.criteria).then(function(response) {
                $scope.people = response.data.people;
            });
        }
    })
```

Clicking the Apply filter button will send an async request that returns a list of people based on the provided criteria, and the list gets updated.

You run the code, and what do you know, nothing happens. Or at least it looks like nothing happened.

But what did happened?

Well, when defining a primitive (e.g. string, boolean, array, etc), Angular does not check the parent scope to see if an object with the same name already exists, resulting in a new object being created in that particular scope.

In the above example, the async request was actually executed, but because we're assigning a primitive (in this case, an array), we're actually just creating a new object in the child scope. Since the newly created variable is not visible to the parent, our list view doesn't get updated.

However, if we were to do something like this:

```javascript
PeopleService.getPeople().then(function(response) {
    var people = response.data.people;
    for(var i = 0; i < people.length; i++) {
        $scope.people.push(people[i]);
    }
});
```

The code now works, and the list gets updated with the names of people retrieved from our async service.

This works because the object <code>people</code> did not exist in the child scope, and this triggers JavaScript to consult the prototype chain. The object is found in the parent scope, and the values are pushed into it. Note that no new objects are created in the child scope, because JavaScript was able to find the relevant object.

Of course, because Angular keeps track of the parent-child relationship within scopes, you can also use the <code>$parent</code> property to update a parent object inside the child scope, but this method is not recommended, as it makes the code dependent on some other code, which in turn makes it less testable.

The recommended way is to instead use an object when defining a variable within the parent scope that you know is going to require "two-way binding". Something like this:

```javascript
angular.module('myApp', [])
    .controller('MainCtrl', function($scope) {
        $scope.peopleList = {
            people: []
        };
    })
    .controller('FilterCtrl', function($scope, PeopleService) {
        $scope.criteria = '';
        $scope.applyFilter = function() {
            // Some async call that returns a list of names
            PeopleService.getPeople($scope.criteria).then(function(response) {
                $scope.peopleList.people = response.data.people;
            });
        }
    })
```

Since the <code>peopleList</code> object doesn't exist in the child scope, the code is forced to consult the prototype chain to find it.

This is pretty much what I've learnt so far about Angular scope inheritance. It's probably just the tip of the iceberg, and there's a lot more to learn, but as with everything, it's important to take things one step at a time, and take the time to really understand the branch you're sitting on before you start exploring the rest of the tree.

For a more detailed explanation on this topic, here's <a href="https://github.com/angular/angular.js/wiki/Understanding-Scopes" target="_blank">an excellent article by Mark Rajcok</a>.

Happy coding!

<em>Wassalam</em>
