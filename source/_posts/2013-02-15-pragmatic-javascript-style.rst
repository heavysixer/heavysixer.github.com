---
layout: post
title: "Pragmatic JavaScript Style"
date: 2013-02-15 09:08
comments: true
categories: ["Relearning JavaScript", "JavaScript"]
---

The Elements Of Style
=====================

My goal is to make you a better programmer, and I am going to do this by teaching you about style. I am not talking about fashion, because I 
think most programmers would flunk that test; unless, comic-con couture is a thing. Instead we'll talk about the importance of style, how it 
forms, how it spreads and when to kill it. Specifically, we'll look at style as it applies to programming. Ultimately, once we have a context 
for evaluating style I will introduce you to elements of programmatic style which have served me well over the years as a professional 
software developer.

What Is Style?
--------------

Style is often used as a measurement of quality. When someone is described as having style or being stylish, it is almost universally meant as 
a complement. If someones' style ever comes into question it is usually in comparison to someone else's style. "My style's the best and so I 
challenge you" screams 70's era martial arts star.

Stylishness is a fresh approach, a unique perspective, an unexpected insight into an activity. The application of a style can become so 
prominent that it expands the activity itself; that house is built in a Frank Lloyd Wright style. What starts as a personal style in painting 
can become an art movement almost overnight. Style spreads like a virus, it is the original meme, a mind virus that changes the way you see 
the world forever. Style is often the conduit where new ideas pulsate.

How does style effect programmers? Well the good news about style for those algorithmically inclined is that, no matter how personal a style 
may seem, for it to exist it all, it must at some level be repeatable. Style must be codified into a series of steps, rules or combinations 
that can be followed, and then recognized by others. Therefore if style is a measurement of quality, and at the same time repeatable then it 
can be taught. Just ask Strunk and White.

William Strunk Jr. wrote "The Elements of Style" while he was a professor at Cornell. He began with 7 rules for the usage of language, and 11 
principles of composition. His student E.B. White revised the book forty years later, by adding an additional 4 rules. The goal of the book 
was to give aspiring writers and grammarians a context from which to evaluate their own work.

According to White, Strunk was compelled to write the "little book" out of sympathy for those afflicted with reading the writer's ill-composed 
dreck: "Will felt that the reader was in serious trouble most of the time, floundering in a swamp and that it was the duty of anyone 
attempting to write English to drain the swap quickly and get the reader up on dry ground, or at least throw a rope."

Over the years the book has remained wildly popular by those learning to write efficiently, and is affectionately referred to as "Strunk and 
White." That is not to say it has been universally loved or followed. Dorothy Parker is quoted in the New York Times as saying "If you have 
any young friends who aspire to become writers, the second-greatest favor you can do them is to present them with copies of 'The Elements of 
Style.' The first-greatest, of course, is to shoot them now, while they're happy."

Many found the rules too restrictive, and opinionated. White said Strunk believed "...it was worse to be irresolute than to be wrong." 
Strunk's assertions is that it takes passion to be stylish. You need to be able to draw boundaries, to allow this idea to flourish while 
forcing that one to die. Style is a sine wave attracting some and repelling others.

What is programmatic style?
---------------------------

As mentioned previously, Stunk and White wrote their book not only to empower and train writers, but to save readers from slogging through 
what was in their minds a textual tar pit. So too, good programmatic style services two audiences, the developer and the processor. That is 
to say that the code should be well-written, both syntactically, and technically. Below are qualities I consider essential in application 
of programmatic style:

**Consistency** - By repeatedly applying rules to the codebase we ensure consistency. Consistency, mitigates noise in the codebase, and 
brings the intent of the code into clearer focus. Put another way, if a developer is trying to piece together how to read your code, you have 
prevented them from understanding what it does. Consistency is concerned with how the code looks, e.g. naming conventions, use of whitespace, 
and method signatures; and how the code is written for example ensuring that all functions don't return a string in one context and an integer 
in another.

**Expressiveness** - Code is by nature a symbolic language, where variability and abstractness is implicit. Therefore the developer must find a 
way to make the code meaningful to the reader. This can be archived though naming variables and methods precisely. When reviewing a class, 
method or variable the reader should understand the roles and responsibilities of the code by reading the code itself. If a method can only 
be understood by reading the comments above left by the writer it should be a clue that the code is not expressive.

**Succinctness** - Strive to do just enough. Good programming like good writing is  about clarity of purpose, and not merely 
compactness. It should be about reducing the complexity of a method, not it's usefulness.

**Restraint** - Style should never overpower the subject itself. At that point style becomes the 
subject it becomes a facile artifice, a dish ruined by too much spice. I am reminded of a minimalist chess set I saw in college every piece 
was either a white or black cube, and all pieces were the same size. It was aesthetically beautiful and simultaneously unplayable.

JavaScript Style Guide
----------------------

This style guide was compiled by compiling, reviewing and considering choices I have made in my own work over the years, and coding practices 
of individuals, and development teams I admire in the JavaScript community. As such this style guide should be seen as an amalgamation of 
inputs and influences from the larger JavaScript community rather than the creative output of a singular individual. You can find a list of 
resources used in this guide in the additional resources section. This guide is broken into two sections: "Rules for Visual Clarity" and 
"Rules for Computational Effectiveness".

Caveats
-------

Style guides are just that guides, they are meant to point you in the right direction, but they are at best mutable truth. Moreover,  coding 
theory changes constantly and it is important not to lock yourself into a dogmatic approach to the application of these styles. As my 
professor Clyde Fowler told me in my studio drawing class, "you must think with your hands", and what he meant by that was you must think
through doing, while maintaining the ability to get critical distance from your work.


Rules for Clarity - How others see code
---------------------------------------

Rules Of Thumb
~~~~~~~~~~~~~~

* **Write Clearly And Expressively** - When naming variables, functions, or organizing code remember you are writing for humans to read it 
  not compilers.
* **Following Existing Conventions** - If you share your code anywhere, work on a team, or are hired to write code, then you are not writing 
  for yourself
* **Write in Only One Language** - Where possible don't use JavaScript as a surrogate for other languages.
  This means resisting the urge to write inline HTML, or CSS where possible.
* **Enforce A Uniform Column Width** - Strive for consistent line lengths in source code. Long lines tire the eyes, and cause needless
  horizontal scrolling. An industry standard is 80 characters per line.

Document Formatting
~~~~~~~~~~~~~~~~~~~

1. Naming Conventions

JavaScript is a terse language of brackets, and symbols and one of the only way to make your code expressive to humans is through the names 
you choose for variables, functions and classes among others. Remember when choosing a name it should describe the role and responsibilities 
of that object. Vague or obtuse names like *doStuff* is like telling the reader *you figure it out*, which often times they won't.

* Choose variables and functions with meaningful, expressive and descriptive names. Write for the reader not the compiler.

.. code-block:: javascript

  // Bad
  var a = 1,
      aa = function(aaa) {
          return '' + aaa;
      };

  // Good
  var count = 1,
      toString = function(num) {
          return '' + num;
      };

* Constants should always belong to a namespace, and be written in uppercase with spaces replaced with underscores

.. code-block:: javascript

  // Bad
  MY_CONSTANT = 43;

  // Good
  com.humansized.MY_CONSTANT = 43;

* Variables should be CamelCase

.. code-block:: javascript

  myVariableName

* Classes should be PascalCase

.. code-block:: javascript

  MyAwesomeClass

* Functions should be CamelCase

.. code-block:: javascript

  isLie(cake)

* Namespaces should be CamelCase and use periods as a delimiter

.. code-block:: javascript

  com.site.namespace

* Hungarian notation is not required but you can use it to convey they are objects constructed through or dependent on a library or framework

.. code-block:: javascript

  // JQuery infused variable
  var $listItem = $("li:first");

  // Angular.js uses the dollar sign to refer to angular-dependent variables
  $scope, $watch, $filter

2. Constants And Variables

* Variables and constants definitions always go at the top of the scope

.. code-block:: javascript

  // Bad
  function iterate() {
      var limit = 10;
      for (var x = 0; x < limit; x++) {
          console.log(x);
      }
  }

  // Good
  function iterate() {
      var limit = 10,
          x = 0;

      for (x = 0; x < limit; x++) {
          console.log(x);
      }
  }

* Avoid polluting the global namespace by always declaring variables using *var*

.. code-block:: javascript

  // Bad
  foo = 'bar';

  // Good
  var foo = 'bar';

* Declare multiple variables using a single var declaration, but separate each variable with a newline

.. code-block:: javascript

  // Bad
  var foo = "foo";
  var note = makeNote('Huge Success');

  // Good
  var foo = "foo",
      note = makeNote('Huge Success');

* Declare unassigned variables last. This allows the reader to know they are needed but have delayed initialization.
* Do not assign variables inside a conditional statement, it often masks errors.

.. code-block:: javascript

  // Bad because it is easily misread as an equality test.
  if (foo = bar) {...}

* Do not clobber arguments with variables names.

.. code-block:: javascript

  // Bad
  function addByOne(num) {
      var num = num + 1;
      return num;
  }

  // Good
  function addByOne(num) {
      var newNum = num + 1;
      return newNum;
  }

3. Page Layout

* Blank lines

  * Should always proceed the start of a comment
  * Should be used to separate logically related code

    .. code-block:: javascript

      // Bad
      var = wheels;

      wheels.clean()

       car.apply(wheels);

      truck.drive();

      // Good
      var = wheels;
      wheels.clean()
       car.apply(wheels);

      truck.drive();

* Commas

  * Remove trailing comments in object declarations. It will break some runtime environments.

    .. code-block:: javascript

      // Bad
      var foo = {
          bar: 'baz',
          foo: 'bar',
      }

      // Good
      var foo = {
          bar: 'baz',
          foo: 'bar'
      }

  * Don't use comma first formatting, if you don't know what that means keep it that way!

* Semicolons

  * Even though JavaScript determines semicolons to be optional many compilers expect them, therefore it is better to use them.
  * Useful for clearly delineating the end of a logical statement
  * Do not add meaningless semicolons

* Whitespace

  * Should be removed from the end of a line
  * Should be removed from a blank line
  * Should not mix spaces and tabs
  * Should appear after each comma in a function declaration

    .. code-block:: javascript

      // Bad
      function findUser(foo,bar,baz)

      // Good
      function findUser(foo, bar, baz)

  * Should not appear inside empty functions or literals

    .. code-block:: javascript

      doThis();
      var foo = {};
      var arr = [];

* Brackets And Braces

  * Use only where the compiler calls for it or where it enhances readability
  * Brackets should appear on the line that requires them

    .. code-block:: javascript

      // Bad
      if (hidden)
      {
       ...
      }

      // Good
      if (hidden) {

      }

  * Add whitespace in front and between brackets to aid readability.

    .. code-block:: javascript

      // Bad
      if (condition) goTo(10);

      // Good
      if (condition) {
          goTo(10);
      }

  * There are a couple of exception to the previous rule

    .. code-block:: javascript

      // No Whitespace needed when there is a single argument
      if (foo) ...

      // No whitespace when a parenthesis is used as a scope container
      ;(function () {...})

      // No white space when brackets are used as a function argument
      function sortThis([2,3,4,1])

* Strings

  * String should be constructed using single quotes

    .. code-block:: javascript

      // Bad
      var foo = "Bar";

      // Good
      var foo = 'Bar';

  * Strings longer than the pre-determined character line limit should be reconsidered, if required they should be concatenated

* Functions

  * Method signatures must be consistent. If a function returns a variable in one context it should return a variable in all contexts

  .. code-block:: javascript

    // Bad
    var findFoo(isFoo) {
        if ( isFoo === true ) {
            return true;
        }
    }

    // Good
    var findFoo(isFoo) {
        if ( isFoo === true ) {
            return true;
        }
        return false;
    }

  * While not a requirement, returning early from a function can make the intent more clear

    .. code-block:: javascript

      // Good
      var findFoo(isFoo) {
          if ( isFoo === true ) {
              return true;
          }
          return false;
      }

* Comments

  * Should never trail a statement
  * Comments should be used sparingly, overuse of comments should suggest to the developer that their code is not expressive enough.
  * Comments should aways be written as a complete thought.
  * Multiline comments should always use the multiline syntax

    .. code-block:: javascript

      // Some really
      // bad multiline comment

      /**
       * A well-formed multiline comment
       * so there...
       */

Rules for Computational Effectiveness
-------------------------------------

Rules Of Thumb
~~~~~~~~~~~~~~
* **Assume File Will Be Concatenated** - Modern applications often munge source JavaScript into a streamline file for production. You should
  defensively program your scripts to protect from switches in operation context and scope corruption.

* **Keep your code browser agnostic** - Keep your business logic free of browser specific code by abstracting them into interfaces. This will
  keep your code on a clean upgrade path as browser fall in and out of fashion.
* **Never Use eval()** - Ever
* **Never Use with()** - Ever
* **Keep Prototype Pristine** - Never modify the prototype of a builtins like Array.prototype because it can silently break other's code which
  expect standard behavior.

1. Equality Comparisons And Conditional Evaluation

* Use "===" instead of "==" use "!==" instead of "!=" this is because JavaScript is very loose when testing equality.
* When just testing for truthiness you can coerce the values

  .. code-block:: javascript

    if (foo) {...}
    if (!foo) {...}

* When testing for emptiness

  .. code-block:: javascript

    if (!arr.length) { ... }

* You must be explicit when testing for truth

  .. code-block:: javascript

    // Bad because all of these will be coerced into true
    var zero = 0,
    empty = "",
    knull = null,
    notANumber = NaN,
    notDefined

    if (!zero || !empty || !knull || !notANumber || !notDefined ) ...

    // Bad
    var truth = "foo",
    alsoTrue = 1

    if (truth && alsoTrue) ...

    // Good
    if (foo === true) ...

2. Constants and Variables

* When deleting a variable set it to null instead calling #delete or setting it to undefined

  .. code-block:: javascript

    // Bad because undefined means the variable is useful but as yet has no value
    this.unwanted = undefined;

    /**
     * Bad because calling delete is much slower than reassigning a value.
     * The only reason to use delete is if you want to remove the attribute from an objects list of keys.
     */
    delete this.unwanted;

    // Good
    this.unwanted = null;

3. Functions

* Function Expressions

  .. code-block:: javascript

    // Anonymous Function
    var anon = function () {
        return true;
    }

    // Named Function
    var named = function named() {
        return true;
    };

    // Immediately-invoked function, hides its contents from the executing scope.
    ;(function main() {
        return true;
    })();

* Anonymous functions are defined at parse-time, and therefore do not have their names hoisted to the top of the scope.

  .. code-block:: javascript

    // Bad - Runtime Error
    iGoBoom();

    var iGoBoom = function () {
        alert('boom');
    }

    // Good
    iGoBoom();
    function iGoBoom() {
        alert('boom');
    }

* Do not use function declaration within block statements it is not part of  ECMAScript; instead use a function expression.

  .. code-block:: javascript

    // Bad
    if (ball.is(round)) {
        function bounce(){

            // Statements Continue
        }
        return bounce()
    }

    // Good
    if (ball.is(round)) {
      var bounce = function () {

          // Statements Continue
      }
    }

* Do not hide the native arguments object by using the same name in a function

  .. code-block:: javascript

    // Bad
    var foo = function(arguments) {
        alert(arguments.join(' '));
    }

    // Good
    var foo = function(args) {
        alert(args.join(' '));
    }

4. Strings

* When concatenating a string use Array#join for performance reasons.

  .. code-block:: javascript

    // Bad
    var lorem = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.\
    Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in\
    reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in\
    culpa qui officia deserunt mollit anim id est laborum.';

    // Good
    var lorem = ['Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.',
    'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in',
    'reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in',
    'culpa qui officia deserunt mollit anim id est laborum.'].join('');


5. Objects

* Should use object literal vs new Object

  .. code-block:: javascript

    // Bad
    var person = new Object();
    person.firstName = "John";
    person.lastName = "Doe";

    // Good
    var person = {
      firstName: "John",
      lastName: "Doe"
    }

* Don't overwrite reserved words as keys

  .. code-block:: javascript

    // Bad
    var person = { class : "Person" };

    // Good
    var person = { klass : "Person" };

6. Arrays

* Should use literal syntax for creation

  .. code-block:: javascript

    // Bad
    var arr = new Array();

    // Good
    var arr = [];

7. Responsibility Delegation

* Only write code that is the responsibility of the program. Keep your code free of view layer and template code. Use a template library
  like mustache.js instead

  .. code-block:: javascript

    var view = {
        title: "Joe",
        calc: function () {
            return 2 + 4;
        }
    }, output;

    // Bad
    output = '<div><h5>' + title + '</h5><p>' + calc() + '</div>';

    // Good
    var output = Mustache.compilePartial('my-template', view);

* Keep JavaScript out of the HTML

  .. code-block:: javascript

    // Bad
    <button onclick="doSomething()" id="something-btn">Click Here</button>

    // Good
    var element = document.getElementById("something-btn");
    element.addEventListener("click", doSomething, false);

8. Operating Context And Scope

* Where possible wrap your code inside self executing functions. This will insulate your code from pollution by others, and make it easier
  to abstract

  .. code-block:: javascript

    // Good
    ;(function( window, document, undefined) {

      // My Awesome Library
    })(this, document);

* Design for duration-agnostic execution of code. This will prevent your code from building up a backlog of requests that may no longer
  be relevant

  .. code-block:: javascript

    // Bad because this might take longer than 100 milliseconds to complete.
    setInterval(function () {
      findFoo();
    }, 100);

    // Good this will only be called again once findFoo has completed.
    ;(function main() {
        findFoo();
        setTimeout(main, 100);
    })();

* Only use *this* in object constructors, methods and creating closures

* To prevent breaking community code declaring an operating context e.g. `"use strict"` should be wrapped inside a self-executing function
  for modules or inside a function itself when needed

  .. code-block:: javascript

    // Bad
    var bar = findLooseyGoosey();

    "use strict";

    var foo = findStrictly();

    // Good
    var bar = findLooseyGoosey();

    ;(function () {
      "use strict";
      var foo = findStrictly();
    })();

9. Coercion

* Conversion over Coercion

  .. code-block:: javascript

    var num = '1';

    // Bad implicit coercion
    num = +num;

    // Good expressive conversion
    num = Number(num);
