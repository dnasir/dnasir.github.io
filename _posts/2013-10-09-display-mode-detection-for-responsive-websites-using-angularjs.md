---
layout: post
title: Display Mode Detection for Responsive Websites using AngularJS
date: 2013-10-09 15:14

comments: true
categories: [AngularJS, angularjs, display mode detection, JavaScript, javascript, Web Development]
---
I've recently started using AngularJS in a project I'm currently working on. So far, AngularJS has been great, and I've especially enjoyed building my own custom directives.

Here's one I'm using to determine the current display mode for responsive websites.

[javascript]
angular.module('myApp', [])
    .directive('dnDisplayMode', function($window) {
        return {
            restrict: 'A',
            scope: {
                dnDisplayMode: '='
            },
            template: '<span class="mobile"></span><span class="tablet"></span><span class="tablet-landscape"></span><span class="desktop"></span>',
            link: function(scope, elem, attrs) {
                var markers = elem.find('span');

                function isVisible(element) {
                    return element
                        && element.style.display != 'none'
                        && element.offsetWidth
                        && element.offsetHeight;
                }

                function update() {
                    angular.forEach(markers, function (element) {
                        if (isVisible(element)) {
                            scope.dnDisplayMode = element.className;
                            return false;
                        }
                    });
                }

                var t;
                angular.element($window).bind('resize', function() {
                    clearTimeout(t);
                    t = setTimeout(function() {
                        update();
                        scope.$apply();
                    }, 300);
                });

                update();
            }
        };
    });
[/javascript]

And here's the CSS you'll need to get this working.

[css]
.display-mode {
  height: 0;
  margin: 0;
  overflow: hidden;
  padding: 0;
}

.display-mode span {
  display: inline-block;
  height: 1px;
  width: 1px;
}

@media only screen and (max-width: 712px) {
  .display-mode .tablet,
  .display-mode .tablet-landscape,
  .display-mode .desktop {
    display: none;
  }
}

@media only screen and (min-width: 713px) and (max-width: 954px) {
  .display-mode .mobile,
  .display-mode .tablet-landscape,
  .display-mode .desktop {
    display: none;
  }
}

@media only screen and (min-width: 955px) and (max-width: 1195px) {
  .display-mode .mobile,
  .display-mode .tablet,
  .display-mode .desktop {
    display: none;
  }
}

@media (min-width: 1196px) {
  .display-mode .mobile,
  .display-mode .tablet,
  .display-mode .tablet-landscape {
    display: none;
  }
}
[/css]

And here's how you would use it.

[html]

<html ng-app="myApp">
  ...
  <body ng-controller="MainCtrl">
    <div class="display-mode" dn-display-mode="displayMode"></div>
    ...
[/html]

[javascript]
angular.module('myApp')
    .controller('MainCtrl', function($scope) {
        $scope.displayMode = 'mobile'; // default value

        $scope.$watch('displayMode', function(value) {
            switch(value) {
                case 'mobile':
                    // do stuff for mobile mode
                case 'tablet':
                    // do stuff for tablet mode
                // and so on
            }
        });
    });
[/javascript]

As you can see, I'm not doing any calculations in determining the current display mode. I figured that it was far simpler to just use CSS and media queries to show or hide the elements I've dynamically added to the DOM. The directive just checks to see which element is visible whenever the window is resized, and sets the model value accordingly.

This is pretty useful if you have widgets, like the site navigation, that operates differently depending on the display mode.

Hopefully someone will find this useful.

<em>Wassalam</em>
