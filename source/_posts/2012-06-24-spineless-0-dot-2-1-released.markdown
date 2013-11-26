---
layout: post
title: "Spineless 0.2.1 Released"
date: 2012-06-24 14:43
comments: true
categories: ["JavaScript", "Spineless"]
---

I have just pushed a new release of my JavaScript application framework called Spineless. Spineless is a simple MVC stack without the need of a backbone.
[https://github.com/heavysixer/spineless](https://github.com/heavysixer/spineless)

The goal of Spineless is to provide "just enough framework" to succeed. If I have done my job, you should be able to write your first Spineless app in less than 10 minutes.

Spineless is meant to run with virtually no dependencies. In the age of frameworks with massive dependency chains, here is a list of things you *DO NOT* need to run spineless.

1. A persistance layer (e.g. database)
2. A backend server (e.g. node.js)
3. An internet connection! (srsly)

Spineless has only two dependencies, JQuery and Mustache.js, both which come bundled
with the project inside the /lib directory. 

Like any good MVC framework Spineless uses the concept of models, controllers and views.

1. Spineless models are essentially JavaScript objects and completely optional.
2. Controllers are used to marshall commands from the views to the models where needed.
3. Views are the visual interface that the user sees.

In addition to the normal MVC stack, Spineless also uses the concept of helpers and templates.

1. Templates are HTML snippets, which are used by views to get better use of reusable code.
2. Helpers are functions that modify a template's variables any way you choose.

Going Spineless in 10 minutes or less
---------------------------------------

The entire Spineless application resides inside the ".application" div. An application consists
of a collection of controllers which in turn contain a collection of views. 
Consider the following example:

```html
    <div class="application">
      <div class="controller" data-controller='application'>
        <div class="view" data-action='index'>
          Hello World!
        </div>
      </div>
    </div>
```

In this example you'll see that we have defined an application with a single controller. The name of the controller is defined by the `data-controller` attribute. This attribute is required by Spineless to route requests to the proper location. Views are much like controllers, but instead of using the `data-controller` attribute they use the `data-action`.

Routing Requests
----------------
Routing requests through Spineless is incredibly painless to make any link a spineless request just add the "route" class. For example:

``` html 
    <a class="route" href="/application/hello">Hello</a>
```

When the user clicks on this link they will now be routed to the application controller where the `#hello` method will be called. If you are not using an element that support the `href` attribute you can also place your url inside a `data-href` attribute:

``` html 
<div class="route" data-href="/application/hello">Hello</div>
```

If you want to manually trigger a route request from within JavaScript you can call the `get` function:

```javascript
    spineless.get('application', 'index');`
```


Passing local variables to templates
------------------------------------

When rendering templates, Spineless substitutes predefined template variables with those you supply using JSON. The JSON can be provided in at least two ways:

  1. By url encoded a json object into the `data-locals` attribute. 
  2. Creating of modifying the JSON object using a helper function.

I will explain the helper function method next, but here is a simple example of what the data-locals method looks like:

```html
    <div data-locals="{&quot;name&quot;:&quot;Mark&quot;}" data-template='hi-my-name-is'></div>
```

Helper functions
----------------

Helpers are developer-created functions that execute during the rendering of specific templates. Just like in Rails, helpers are available globally across all views. To demonstrate, imagine we have two DIV tags with locals supplied as urlencoded JSON object:

``` html
    <div data-locals="{&quot;name&quot;:&quot;Mark&quot;}" data-template='hi-my-name-is'></div>
    <div data-locals="{&quot;name&quot;:&quot;Slim Shady&quot;}" data-template='hi-my-name-is'></div>
```

As you can see these objects have a property called `name`, each with unique values. These locals are linked to the “hi-my-name-is” template. To create a helper we’ll bind a function to execute whenever the `hi-my-name-is` template is rendered. Doing this will allows us intercept the template instance's data-locals object and modify it anyway we choose before passing it along to Mustache to render. Here is the full example of the helper function:

``` javascript
    var sp = $.spineless({
        helpers: {
            'hi-my-name-is': function(obj) {
                if (obj.name === 'Slim Shady') {
                    obj.name = "*wikka wikka* " + obj.name;
                }
                return (obj);
            }
        }
    });
```

PubSub for Spineless events
---------------------------

Spineless now has a very minimal publisher subscriber (PubSub) events framework. The goal of this is to allow other code executing outside of Spineless to receive updates when internal Spineless events execute, without having to know anything about how Spineless is implemented. Here is a trivial example of creating an observer that is triggered every time a view is done rendering.

``` javascript
    $(document).ready(function() {
        var sp = $.spineless();
        sp.subscribe('afterRender',
        function(publisher, app) {
            app.request.view.append("<h1>Yes it has!</h1>")
        })
        sp.get('application', 'index');
    });
```

When the publisher executes a subscriber’s function it passes a reference to itself and the Spineless app instance as arguments. This allows the receiver to manage it’s subscriptions and gives the function access to the the Spineless current request, params hash among other things.

Controller functions
--------------------

Controller functions are optional code that developers can write to augment the rendering of the view. Controller functions work much like helper functions do, in that they are executed before the view is returned to the screen. Unlike helper functions which are linked to an arbitrary number of templates; controller functions are scoped to just one controller action. Consider this example which executes when someone visits `/users/update`:

```javascript
    var sp = $.spineless({
        controllers: {
            users: {
                update: function(elements, request) {
                    if ($.currentUser.isAdmin()) {
                        this.render(elements);
                    } else {
                        alert(“Access Denied”);
                    }
                }
            }
        }
    });
    sp.get('application', 'index');
```

I have added examples of all of these new features in the /samples folder of the public Github repo. Please feel free to open bug reports or feature requests, and I will do my best to oblige. 
