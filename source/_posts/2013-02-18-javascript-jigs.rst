---
layout: post
title: "JavaScript Jigs"
date: 2013-02-18 09:06
comments: true
categories: ["Relearning JavaScript", "JavaScript"]
---

In the excellent book "The Pragmatic Programmer: From Journeyman to Master" Hunt and Thomas use the metaphor of a wood worker's jig to describe
how a smart programmer reduces the repetitive nature of coding by creating reusable templates or code generators:

    "When woodworkers are faced with the task of producing the same thing over and over, they cheat. They build themselves a jig or a template.
    If they get the jig right once, they can reproduce a piece of work time after time. The jig takes away complexity and reduces the chances
    of making mistakes, leaving the craftsman free to concentrate on quality."

To be a jig the solution is highly specific and good for one task, for example making a complex cut. At first you might
want to conflate jigs and design patterns together, because they are both reusable solutions to a problem. Jigs are precise where design
patterns are generalized. While Hunt and Thomas said jigs are generators, I will use them in the context of helpers, friendly little functions
or classes that do one thing well. Many of the most popular JavaScript libraries started as a collection of jigs. Prototype and JQuery for
example, were initially just a collection of reusable snippets that acted like speed-boosts, and shortcuts for discrete problems.

What follows are a collection of jigs that are useful in modern JavaScript applications.

Self Executing Functions
~~~~~~~~~~~~~~~~~~~~~~~~

The immediately invoked function expression (IIFE) is one jig you will see various libraries and frameworks use repeatedly. In its most basic form it can be
written in a couple of ways

.. code-block:: javascript

  ;(function(){
      ...
  })();

  ;!function(){
      ...
  }();

  ;-function(){
      ...
  }();

  ;+function(){
      ...
  }();

  ;~function(){
      ...
  }();

  // Not Recommended
  ;void function(){
      ...
  }();

  // Not Recommended
  ;delete function(){
      ...
  }();

The beauty of the IIFE is that it uses a unary expression to coerce a function declaration, which would normally need to be explicitly called
into a function expression that can self-execute. Behind the scenes JavaScript is running a unary operation on the function declaration, the
result of that operation is the function expression, which is immediately invoked with the trailing parentheses "()". Besides being elegant code
the IIFE also affords the following:

* It provides a closure which prevents naming conflicts
* It provides elegant block scoping
* It prevents pollution of the global namespace.
* It promotes the developer to think in terms of modular code.

One other point worth mentioning is the use of the semicolon prepending the statement. Adding this provides a bit of defensive programming 
against other malformed modules that might have a trailing semicolon. If this were just a function declaration it would be absorbed into the 
preceding module. This can often occur when multiple scripts are concatenated together as part of a deploy process. It is highly recommended
that you follow this convention to protect yourself against mystery bugs in production.

Modules
~~~~~~~

Modules are very common is many programming languages, though JavaScript doesn't have a native representation for them. As such other developers
have developed a spec for encapsulating your code inside a reusable module. The following code is based off an example in the "Principles of
Writing Consistent, Idiomatic JavaScript" [1]_.

There are a couple of elements that should be called out in this jig:

* We see two different examples of the self executing function jig being used. This is to ensure proper closure around the module itself and the
  initializer function that adds it to the global namespace.
* Invoking this function returns an object with a bound reference to the private variable "data". This allows the developer to enforce
  the use of getters and setters for access to the data variable.

.. code-block:: javascript

  ;!function(global) {
    var Module = (function() {

      // Mostly Private Variable
      var data = 'secret';

      return {

        bool: true,
        string: 'a string',
        array: [1, 2, 3, 4],
        object: {
          lang: "en-Us"
        },
        getData: function() {
          return data;
        },
        setData: function(value) {
          return (data = value);
        }
      };
    })();

    // expose our module to the global object
    global.Module = Module;

  }(this);

safeEval
~~~~~~~~

The eval function and its siblings setTimeout, setInterval and Function all have access to the JavaScript compiler, which means it is a bit
like running with scissors. Since eval typically does more harm than good people try to work around it as much as possible. This jig does just
that giving you eval like features without calling the function.

.. code-block:: javascript

  // A string representation of an a object similar to what you might get with JSON.
  var dataString = '{"foo":"bar"}';

  ;!function(global, data){ 

      // the variable name provided is replaced with the evaluated code.
      global[data] = new Function("return" + global[data])() 
  }(this, "dataString");

  // dataString is now Object {foo: "bar"}

PubSub
~~~~~~

PubSub is short for a publish-subscribe message system, where objects ask to receive messages that are broadcast by publishers. The main
advantage of PubSub is that the subscribers are loosely coupled allowing just about any object to publish and subscribe to messages. PubSub
systems also have been proven to scale much nicer that tightly coupled client / server paradigms. This implementation of PubSub was written
by Ben Alman and can be download from his Github account [2]_. Let's take a look at this jig in detail. Again, the first thing you should
notice is that this jig uses the IIFE jig too (see a pattern yet?). This jig does depend on jQuery for access to the "on","off", and "trigger"
functions. This jig stores an internal list of subscribers as keys of the internal object "o". When a message is broadcast all the subscribers
have the arguments supplied by the publisher transferred to them.

.. code-block:: javascript

  ;(function($) {

    var o = $({});

    $.subscribe = function() {
      o.on.apply(o, arguments);
    };

    $.unsubscribe = function() {
      o.off.apply(o, arguments);
    };

    $.publish = function() {
      o.trigger.apply(o, arguments);
    };

  }(jQuery));

  // Usage Examples
  // Creates a "named" logging function.

  function createLogger(name) {
    return function(event, a, b) {

      // Skip the first argument (event object) but log the name and other args.
      console.log(name, a, b);
    };
  }

  // Subscribe to the "foo" topic (bind to the "foo" event, no namespace).
  $.subscribe('foo', createLogger('foo'));

  // Subscribe to the "foo.bar" topic (bind to the "foo" event, "bar" namespace).
  $.subscribe('foo.bar', createLogger('foo.bar'));

  /*
   * logs:
   * foo 1 2
   * foo.bar 1 2
   * foo.baz 1 2
   */
  $.publish('foo', [1, 2]);

  /*
   * logs:
   * foo.bar 3 4
   */
  $.publish('foo.bar', [3, 4]);

Your Jigs Go Here
~~~~~~~~~~~~~~~~~
Please send me your favorite jigs. I would love to expand this post with more great Jigs. 

Footnotes
~~~~~~~~~

.. [1] https://github.com/rwldrn/idiomatic.js/
.. [2] https://github.com/cowboy/jquery-tiny-pubsub