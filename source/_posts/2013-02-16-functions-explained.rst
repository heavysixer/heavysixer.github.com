---
layout: post
title: "Functions Explained"
date: 2013-02-15 12:10
comments: true
categories: ["Relearning JavaScript", "JavaScript"]
---


A Deep Dive into JavaScript Functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Based on my readership I have to assume most of you are familiar with JavaScript already. Therefore, it may seem odd to include a post on functions. After all, they are 
one of the most rudimentary components of JavaScript. My assertion is this, just as a person can speak a language without the ability to read 
or write it, so too can developers use functions in JavaScript and yet be blissfully unaware of their complexities.

Typically developers only become aware of the specifics of functions when something they wrote explodes in their face. My goal in this section 
is to expose the intricacies of JavaScript functions to you, which will hopefully save you from having to pull syntactic shrapnel from your 
codebase.

A word of caution before we begin; JavaScript is only as good as its interpreter. While the concepts we'll consider are well-covered in
the language spec, it does not mean that all runtime environments will work the same way. In other words your milage may vary. This section will
discuss common misconceptions of JavaScript functions, and the silent bugs they introduce. However, debugging functions in detail is not 
covered. Fortunately, debugging has been documented by others in the JavaScript community especially in Juriy Zaytsev's excellent article "Named 
Function Expressions Demystified" [1]_.

Blocks in JavaScript
~~~~~~~~~~~~~~~~~~~~

Before we can understand functions in JavaScript we have to understand blocks. JavaScript blocks are nothing more than statements grouped 
together. Blocks start with a left curly bracket "{" and end with a right one "}". Simply put, **blocks allow statements inside the brackets
to be executed together**. Blocks form the most basic control structure in JavaScript. The following are a few examples of how blocks in 
JavaScript:

.. code-block:: javascript

  // Block as an anonymous self-executing function
  ;!function () {
      var triumph = false,
          cake = false,
          satisfaction = 0,
          isLie,
          note;

      // Block used as part of function expression
      var isLie = function (val) {
          return val === false;
      }

      // Block used as part of a conditional statement
      if (isLie(cake)) {
          triumph = true;
          makeNote('huge success');
          satisfaction += 10;
      }

      // Block used as part of a function declaration
      function makeNote(message) {
          note = message;
      }
  }();

As we saw above, functions are essentially *named blocks*, which the developer can invoke on demand. This is easy to demonstrate:

.. code-block:: javascript

  // The inline conditional block statement is executed only once per cycle.
  if (isLie(cake)) {
      ...
  }

  function makeNote(message) {
      ...
  }

  // The function declaration is executed as many times as it is called.
  makeNote("Moderate Success");
  makeNote("Huge Success");

Function Arguments
~~~~~~~~~~~~~~~~~~

Functions like control flow statements (if, for, while etc.) can be initialized by passing arguments into the function body. In JavaScript 
variables are either a **complex type** (e.g. Object, Array) or a **primitive type** (e.g. String, Integer). When a complex object is supplied 
as an argument it is **passed by reference** to the function body. Instead of sending a copy of the variable, JavaScript sends a pointer to its 
location in memory. Conversely, when passing a primitive type to a function JavaScript **passes by value**. This difference can lead to subtle bugs 
because conceptually we often treat functions as a black box, and assume they can only effect the enclosing scope by returning a variable. 
With pass by reference, the argument object is modified even though it may not returned by the function. Pass by reference and pass by value 
are demonstrated below:

.. code-block:: javascript

  var object = {
      'foo': 'bar'
  },
  num = 1;

  // Passed by reference
  ;!function(obj) {
      obj.foo = 'baz';
  }(object);

  // => Object {foo: "baz"} 
  console.log(object);

  // Passed by value;
  ;!function(num) {
      num = 2;
  }(num);

  // => 1
  console.log(num);

Function Types
~~~~~~~~~~~~~~~

Now that we have a better understanding of blocks, and arguments lets dive deeper into **Function Declaration** and **Function Expression**, 
the two types of functions used in JavaScript. To the casual reader the two appear very similar:

.. code-block:: javascript

  // Function Declaration
  function isLie(cake){
      return cake === true;
  }

  // Function Expression
  var isLie = function(cake){
      return cake === true;
  }

The only real difference between the two, is when they are evaluated. A function declaration can be accessed by the interpreter as it is being 
parsed. The function expression on the other hand is part of an assignment expression, which prevents JavaScript from evaluating it until the
program has completed the assignment. This difference may seem minor, but implications are huge; consider the following example:

.. code-block:: javascript

  // => Hi, I'm a function declaration!
  declaration();

  function declaration() {
      console.log("Hi, I'm a function declaration!");
  }

  // => Uncaught TypeError: undefined is not a function
  expression();

  var expression = function () {
      console.log("Hi, I'm a function expression!");
  }

As you can see in the previous example the *expression* function threw an exception when it was invoked, but the *declaration* function 
executed just fine. This exception gets to the heart of the difference between declaration and expression functions. JavaScript *knows* 
about declaration function and can parse it before the program executes. Therefore, it doesn't matter if the program invokes the function 
before it is defined. This is because behind the scenes JavaScript has *hoisted* the function to the top of the current scope. The function 
expression is not evaluated until it is assigned to a variable; therefore it is still *undefined* when invoked. This is why good code style is 
to define all variables at the top of the current scope. Had we done this then our script would visually match what JavaScript is doing during 
parsetime. 

The concept to take away is that **during parsetime JavaScript moves all function declarations to the top of the current scope**. This is why 
it doesn't matter where declarative functions appear in the script body.

To further explore the distinctions between declarations and expressions, consider the following:

.. code-block:: javascript

  function sayHi() {
      console.log("hi");
  }

  var hi = function sayHi() {
      console.log("hello");
  }

  // => "hello"
  hi();

  // => 'hi'
  sayHi();

Casually reading this code, one might assume that the declaration function would get clobbered because it function expression has an identical 
name. However, since the second function is part of an assignment expression it is given its own scope, and JavaScript treats them as seperate 
entities. To make things even more confusing look at this example:

.. code-block:: javascript

  var sayHo

  // => function
  console.log(typeof (sayHey))

  // => undefined
  console.log(typeof (sayHo))

  if (true) {
      function sayHey() {
          console.log("hey");
      }

      sayHo = function sayHo() {
          console.log("ho");
      }

  } else {
      function sayHey() {
          console.log("no");
      }

      sayHo = function sayHo() {
          console.log("no");
      }

  }

  // => no
  sayHey();

  // => ho
  sayHo();

In the previous example we saw that functions of the same name were considered different if one was an expression and the other was a 
declaration. In this example we are attempting to conditionally define the function based on how the program executes. Reading the script's 
control flow you'd expect *sayHey* to return "hey" since the conditional statement evaluates true. Instead it returns "no", meaning the second 
version of the *sayHey* function clobbered the first. Even more confusing is that the *sayHo* function behaves the opposite way! Again, the 
difference comes down to parsetime versus runtime. 

We already learned that when JavaScript parses the script it collects all of the function declarations and hoists them to the top of the current 
scope. When this happens it clobbers the first version of *sayHey* with the second because they exist in the same scope. This explains why 
it returns "no." We also know that function expressions are ignored by the parser until the assignment process completes. Assignment happens 
during runtime, which is also when the conditional statement is evaluated. That explains why the *sayHo* function was able to be conditionally 
defined. The key to remember here is that **function declarations can not be conditionally defined. If you need conditional definition use a function expression**.
Furthermore, **function declarations should NEVER be made inside a control flow statement**, due to the different ways interpreters handle it.

Function Scopes
~~~~~~~~~~~~~~~

Unlike many other languages which are scoped to the block, JavaScript is scoped to the function. In Ruby (version 1.9.+) you can write this:

.. code-block:: javascript

  x = 20
  10.times do |x|

    # => 0..9
    puts x
  end

  # => 20
  puts x

What this demonstrates is that each block gets its own scope. Conversely, if we wrote similar code in JavaScript:

.. code-block:: javascript

  var x = 20;

  // Functions have their own scope
  ;!function() {
      var x = "foo";

      // => "foo"
      console.log(x);
  }();

  // => 20
  console.log(x);

  for (x = 0; x < 10; x++) {

      // => 0..9
      console.log(x);
  }

  // => 10
  console.log(x);

In JavaScript *x* is available inside the for loop, because as a control statement it belongs to the enclosing scope. This is not intuitive to 
many developers used to block level scope. JavaScript handles the need of block level scope at least partially through the use of closures 
which we'll discuss later.

Debugging Functions
~~~~~~~~~~~~~~~~~~~

Before we wrap this topic up, lets briefly touch on debugging functions. In JavaScript naming a function expression is completely optional; 
so why do it? The answer is to aid the debugging process. Named function expressions have access to their name within the newly defined scope, 
but not in the enclosing scope. Without a name their anonymous nature can make them feel a bit like ghosts in the machine when it comes to 
debugging.

.. code-block:: javascript

  var namedFunction = function named() {

      // => function
      console.log(typeof(named));
  }
  namedFunction();

  // => undefined
  console.log(typeof(named));

Nameless function expressions will be displayed in the stack trace as "(anonymous function)" or something similar. Naming your function 
expression gives you clarity when trying to unwind an exception whose call stack may feel miles long.

.. code-block:: javascript

  /*
   * It is much harder to debug anonymous function expressions
   * Uncaught boom
   *    - (anonymous function)
   *    - window.onload
   */
  ;!function(){
      throw("boom");
  }();

  /*
   * Naming your function expressions give you a place to start looking when debuggin.
   * Uncaught boom
   *    - goBoom
   *    - window.onload
   */
  ;!function goBoom() {
      throw("boom")
  }();

.. [1] http://kangax.github.com/nfe/
