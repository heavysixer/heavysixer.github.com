---
layout: post
title: "Getting Closure"
date: 2013-02-25 15:21
comments: true
categories: ["Relearning JavaScript", "JavaScript"]
---

Understanding the Dark Arts of JavaScript Closures
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

     "No matter where you go, there you are." 
     - Buckaroo Banzai

The purpose of this post is to explain how closures work in plain english, and to give a few compelling examples where the use of closures really improve 
the quality of your code.

Like many others I am a self-taught programmer, and little over a decade ago I was also a freshly minted Creative Director working in Los Angels. I was employed 
by a major footwear brand, and had inherited a team of very bright and technically gifted programmers. I felt that I needed to learn enough code to speak 
intelligently to them. I didn't want to propose a feature that wasn't possible, and more importantly I wanted to understand the promise and the problems 
inherent in the medium we were building within. More generally though, I am just a very curious person who likes to learn. Once I started to pull that tread 
the world of programming began to unwind for me. Now years later, here I sit writing about the internals of JavaScript.

Being that my computer science education has been ad-hoc there are many core concepts in JavaScript (and programming in general) that I wanted to understand 
better. My hypothesis is that there are others like me who have been using and abusing JavaScript for years. For this reason I decided to write on closures an 
often used but equally often misunderstood concept in JavaScript. Closures are important for a variety of reasons:

1. They are both a feature and a philosophy that once understood makes many other concepts (e.g. data binding, promise objects) in JavaScript easier.
2. They are one of the most powerful internals of the language, which many other so-called *real* languages don't support.
3. They are where JavaScript is trending due to the rise in popularity of asynchronous execution.

For all the potential benefits that closures offer, there is a *black magic* quality to them that can make them hard to understand. Let's start with a 
definition, **A closure is the act of binding all free variables, and functions into a closed expression, that persist beyond the lexical scope from which they 
were created.** While this is a succinct definition it is pretty impenetrable for the uninitiated; let's dig deeper.

The Straight Dope On Scope
~~~~~~~~~~~~~~~~~~~~~~~~~~
Before we can truly understand closures we must take a step back and look at how scope works in JavaScript. When reading about JavaScript periodically 
writers will make reference to lexical scope, or the current and/or executing scope. Lexical scope simply means that where a statement is placed within the 
body of the script is important and effects how it can be accessed, and what in turn it has access to. In JavaScript unlike other languages the only way to 
create a new scope is through a function invocation [1]_. This is what programmers mean when they say JavaScript has function level scoping. This form of 
scoping may be anti-intuitive to programmers coming from languages that support block-level scoping e.g. Ruby. 

The following example demonstrates lexical scope:

.. code-block:: javascript

  // Free Variable
  var iAmFree = 'Free to be me!';

  function canHazAccess(notFree){

    var notSoFree = "i am bound to this scope";

    // => "Free to be me!"
    console.log(iAmFree);
  }

  // => ReferenceError: notSoFree is not defined
  console.log(notSoFree)

  canHazAccess();

As you can see the function declaration *canHazAccess()* can reference the *iAmFree* variable; this is because the variable belongs to the enclosing scope. The 
*iAmFree* variable is an example of what in JavaScript is called a **free variable** [2]_. Free variables are any non-local variable which the function 
body has access to. To qualify as a free variable it must be defined outside the function body and not be passed as a function argument.

Conversely, we see that referencing *notSoFree* from the enclosing scope produces an error. This is because at the point at which this variable
was defined it was inside a new lexical scope (remember function invocation creates a new scope).

Put another way, **function level scopes act like one-way mirrors; they let elements inside the function body spy on variables in the outer scope, while they 
remain hidden.** As we'll see below closures short-circuit this relationship, and provide a mechanism whereby the inner scopes internals can
be accessed by the outer scope.

Thisunderstandings
~~~~~~~~~~~~~~~~~~

One feature of scopes, that routinely throw developers off (even seasoned ones) is the use of the *this* keyword as it pertains to the lexical
scope. In JavaScript the **this keyword always refers to the owner of scope from which it is executing**. Misunderstanding how *this* works can 
cause all sorts of weird errors where a developer assumes they are accessing a particular scope but are actually using another. Here is how this might 
happen:

.. code-block:: javascript

  var Car, tesla;

  Car = function() {
    this.start = function() {
      console.log("car started");
    };

    this.turnKey = function() {
      var carKey = document.getElementById('car_key');
      carKey.onclick = function(event) {
        this.start();
      };
    };
    return this;
  };
  tesla = new Car();

  // Once a user click's the #carKey element they will see "Uncaught TypeError: Object  has no method 'start'"
  tesla.turnKey();

The developer who wrote this was headed in the right direction, but ultimately a *thisunderstanding* forced them off the rails. They correctly bound 
the click event to the *car_key* DOM element. However, they assumed that nesting the click binding inside the car class would give the DOM element a reference 
to the car's *this* context. The approach is intuitive and *looks legit*, especially based on what we know about free variables and lexical scope. Unfortunately, 
it's hopelessly borked; because as we learned earlier a new scope is created each time a function is invoked. Once the onclick event fired *this* now 
referred to the DOM element not the car class.

Developers sometimes get around this scoping confusion by assigning *this* to a local free variable (e.g. that, _this, self, me). Here is the previous method 
rewritten to use a local free variable instead of *this*.

.. code-block:: javascript

  var Car, tesla;

  Car = function() {
    this.start = function() {
      console.log("car started");
    };

    this.turnKey = function() {
      var that = this;
      var carKey = document.getElementById('carKey');
      carKey.onclick = function(event) {
        that.start();
      };
    };
    return this;
  };
  tesla = new Car();

  // Once a user click's the #carKey element they will see "car started"
  tesla.turnKey();

Because *that* is a free variable, it won't be redefined when the onclick event is triggered. Instead it remains as a pointer to the previous *this* context.
Technically, this solves the problem, and I am going to resist the urge of calling this an anti-pattern (for now). I have used this technique thousands of 
times over the years. However, it always *felt* like a hack, and fortunately, closures can help us marshall scopes in a much more elegant way.

My First Closure
~~~~~~~~~~~~~~~~

In it's most basic form a closure is simply an outer function that returns an inner function. Doing this creates a mechanism to return an enclosed scope on 
demand. Here is a simple closure:

.. code-block:: javascript

  function outer(name) {
    var hello = "hi",
    inner;

    return inner = function() {
      return hello + " " + name;
    }
  }

  // Create and use the closure
  var name = outer("mark")();

  // => 'hi mark'
  console.log(name);

In this example you can see that the local variable *hello* can be used in the return statement of the inner function. At the point of execution *hello* is a 
free variable belonging to the enclosing scope. This example borders on meaninglessness though; lets look at a slightly more complex closure:

.. code-block:: javascript

  var car;
  function carFactory(kind) {
    var wheelCount, start;
    wheelCount = 4;
    start = function() {
      console.log('started with ' + wheelCount + ' wheels.');
    };

    // Closure created here.
    return (function() {
      return {
        make: kind,
        wheels: wheelCount,
        startEngine: start
      };
    }());
  }

  car = carFactory('Tesla');

  // => Tesla
  console.log(car.make);

  // => started with 4 wheels.
  car.startEngine();


Why Use Closures
~~~~~~~~~~~~~~~~
Now that we know what closures are, let's look at some use cases on where they can elegantly solve common problems in JavaScript.

* **Object Factories**

  The previous closure implements what is commonly known as the Factory Pattern [3]_. In keeping with a Factory Pattern the internals of the factory can be 
  quite complex but are abstracted away in part thanks to the closure. This highlights one of the best features of closures which is their ability to 
  hide state. JavaScript doesn't have the concept of private or protected contexts, but using closures give us a good way to emulate some level of privacy.

* **Create A Binding Proxy**

  As promised lets revisit the Car class we wrote earlier. We solved the scoping problem by assigning the outer function's *this* reference to a *that* free 
  variable. Instead of that approach we'll solve it through the use of closures. First we create a reusable closure function called *proxy*, which takes
  a function and a context and returns a new function with the supplied context applied. Then we wrap the onclick function with our *proxy* and pass in the *this*
  that references the current instance of the *Car* class. Coincidentally, this is a simplified version of what jQuery does in their own proxy function [4]_.

  .. code-block:: javascript

    var Car, proxy, tesla;

    Car = function() {
      this.start = function() {
        return console.log("car started");
      };
      this.turnKey = function() {
        var carKey;
        carKey = document.getElementById("carKey");
        carKey.onclick = proxy(function(event) {
          this.start();
        }, this);
      };
      return this;
    };

    // Use a closure to bind the outer scope's reference to this into the newly created inner scope.
    proxy = function(callback, self) {
      return function() {
        return callback.apply(self, arguments);
      };
    };

    tesla = new Car();

    // Once a user click's the #carKey element they will see "car started"
    tesla.turnKey();

* **Contextually Aware DOM Manipulation**

  This example comes from directly from Juriy Zaytsev's excellent article "Use Cases for JavaScript Closures" [5]_ . His example code demonstrates how to use a 
  closure to ensure a DOM element has a unique ID. The larger takeaway is that you can use closures as a way to maintain internal states about your program in
  an encapsulated manner.

  .. code-block:: javascript

    var getUniqueId = (function() {
      var id = 0;
      return function(element) {
        if (!element.id) {
          element.id = 'generated-uid-' + id++;
        }
        return element.id;
      };
    })();

    var elementWithId = document.createElement('p');
    elementWithId.id = 'foo-bar';
    var elementWithoutId = document.createElement('p');

    // => 'foo-bar'
    getUniqueId(elementWithId);

    // => 'generated-id-0'
    getUniqueId(elementWithoutId);

* **Singleton Module Pattern**

  Modules are used to encapsulate and organize related code together under one roof. Using modules keeps your codebase cleaner, easier to test, and reuse. 
  Attribution for the Module Pattern is typically given to Richard Conford [6]_, though a number of people most notably Douglas Crockford are responsible for 
  popularizing it. The Singleton Module is a flavor that restricts more than one instance of the object from existing. It is very useful for instances where 
  you want several objects to share a resource. A much more in depth example of the Singleton Module can be found here [7]_, but for now consider the following 
  example:

  .. code-block:: javascript

    // Create a closure
    var SecretStore = (function() {
      var data, secret, newSecret;

      // Emulation of a private variables and functions
      data = 'secret';
      secret = function() {
        return data;
      }
      newSecret = function(newValue) {
        data = newValue;
        return secret();
      }

      // Return an object literal which is the only way to access the private functions and variables
      return {
        getSecret: secret,
        setSecret: newSecret,
      };
    })();

    var secret = SecretStore;

    // => "secret"
    console.log(secret.getSecret());

    // => "foo"
    console.log(secret.setSecret("foo"));

    // => "foo"
    console.log(secret.getSecret());

    var secret2 = SecretStore;

    // => "foo"
    console.log(secret2.getSecret());

TLDR Takeaways
~~~~~~~~~~~~~~

1. **Lexical scope gives importance to where code is located within the script body.**
2. **Free variables are any non-local variable which the function body has access to.**
3. **The only way for new scopes to be created in JavaScript is through function invocation.**
4. **The *this* keyword always refers to the owner of scope from which it is executing.**
5. **A closure allows a function to access variables outside of its lexical scope.**

.. [1] http://howtonode.org/what-is-this
.. [2] http://en.wikipedia.org/wiki/Free_variable
.. [3] http://en.wikipedia.org/wiki/Factory_method_pattern
.. [4] https://github.com/jquery/jquery/blob/master/src/core.js#L685
.. [5] http://msdn.microsoft.com/en-us/magazine/ff696765.aspx
.. [6] http://groups.google.com/group/comp.lang.javascript/msg/9f58bd11bd67d937
.. [7] http://www.addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript