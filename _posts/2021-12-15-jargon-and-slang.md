---
layout: post
title:  "Jargon & Slang in JavaScript"
author: mark
categories: [ JavaScript ]
description: "Jargon & Slang in JavaScript"
comments: false
---


# Jargon and Slang in Programming Languages
> “One of the reasons there are so many terms for conditions of ice is that the mariners observing it were often trapped in it, and had nothing to do except look at it.” 
-- Alec Wilkinson, The Ice Balloon: S. A. Andrée and the Heroic Age of Arctic Exploration

## Introduction
Several years ago I came across a presentation by Gary Bernhardt, simply titled "Wat." Wat is a colloquialism used on the internet to describe confusion or amused disbelief towards a subject, in this case JavaScript. Bernhardt's presentation used a question and answer format. First he displayed a seemingly reasonable line of JavaScript and then asked the audience to give him the output. In one case he asked the audience what `{}+[]` would produce. Most in the audience thought the result would be some sort of error because it didn't make sense that you could add a literal object and array together. Instead the result was `'0'`. The audience groaned and laughed in bemusement. The presentation continued on this way, asking questions and then giving results that seemed to be too wrong to be correct. 

To the chagrin of many defenders of JavaScript this presentation went viral, mostly because it is funny and lighthearted, and gave the JavaScript community a mirror to laugh at themselves with. Eventually, even Brendan Eich, the creator of JavaScript joined the fray, making a half-hearted effort in a recent presentation to explain some of the seemingly idiotic things that his language did in Bernhardt's presentation.

I originally, thought this post was going to be spent unpacking and then explaining examples of WAT within JavaScript. However, as I dug deeper into the various examples used in Bernhardt's presentation I began to realize that many of these inconsistencies were not defects of the language, but instead a secret handshake inside the language, a kind of programmatic jargon. At that point my direction for this post shifted, and now the goal is to define jargon as it relates to programming. I will give examples of jargon in JavaScript, how to embrace it, or avoid it depending on your own style.


## Jargon.prototype = new Slang()

Before we can accurately define jargon we must first understand what constitutes slang. Slang is the use of words or expressions, which are outside of the normal, and standard vocabulary of a culture. Slang's ability to transmit meaning depends on the receiver's ability to unpack the often highly contextual references in the words or expression. In an effort to codify the mechanics of slang Bethany K. Dumas and Jonathan Lighter (Duman & Lighter, 1978) suggest that an example of slang must meet at least two of the following criteria:
1. It lowers, if temporarily, "the dignity of formal or serious speech or writing"; in other words, it is likely to be considered in those contexts a "glaring misuse of register."
2. Its use implies that the user is familiar with whatever is referred to, or with a group of people who are familiar with it and use the term.
3. "It's a taboo term in ordinary discourse with people of a higher social status or greater responsibility."
4. It replaces "a well-known conventional synonym". This is done primarily to avoid discomfort caused by conventional phrases or by further elaboration.

As we can see from these rules, Jargon only meets the second criteria. However, even this is enough to begin to see the faint outlines of what we might call programmatic jargon.

## What is programmatic jargon?
**Programmatic jargon is a compression of code through the use of highly specific often technical rules of the language.** Like other forms of jargon, the programmatic form is used to efficiently reference complex ideas between members of a community. It can become a kind shorthand used to describe a concept more tursely. However, because jargon is so highly contextual it often acts as a social divider, or lingual border guard between communities. This can be what makes jargon feel so impenetrable by outsiders. Knowing this we can begin to identify criteria for defining programmatic jargon:
1. In short-circuits mechanics of the language.
2. It is confusing or easily misunderstood by the casual member of the community.
3. It subverts visual clarity in the service some other goal e.g. smaller code or faster execution.
4. It serves as means for stratification within a community.

Often jargon gets a bad reputation because it is often used by those who have only an inkling of what the terms mean. In these cases jargon becomes noise in the conversation, verbal filler to make the speaker seem more intelligent. In the case of programmatic slang, this could be represented by the misapplication, or misuse of a programming concept in the hopes of appearing clever. Of course the misuse of jargon makes the speaker seem like a fraud, and an idiot. Richard Mitchell sums up this sentiment when he writes:
> “His jargon conceals, from him, but not from us, the deep, empty hole in his mind. He uses technological language as a substitute for technique.” 
― Richard Mitchell, Less Than Words Can Say

In JavaScript there are three components of the language that especially lend themselves to the creation of jargon. They are **coercion**, **logical operators**, and **bitwise manipulations** (sometimes known as bit twiddling.) Now that we have a basis for recognizing programmatic jargon we will spend the rest of the chapter exploring and understanding specific examples of how it occurs in JavaScript.

----
Jargon is often used in a pejorative way to describe the use of technical terminology to make a speaker seem intelligent or expert. However, jargon used correctly can be a succinct pointer to a concept that an experienced listener need not have explained. In this chapter jargon simply means highly contextual code that is often impenetrable to the uninitiated, but not necessarily a bad approach.


## Coercion
In JavaScript as in most other languages, coercion is the act of forcing an object or entity of one type into another. This is not to be confused with type conversion with is the explicit transformation between types. In JavaScript explicit type conversion would look like:

```javascript
// => "1"
var a = (1).toString();
```
However, the number can also be implicitly coearsed into a string this way:

```javascript
// => "1"
var a = 1 + "";
```

Many of the most cryptic code examples that have puzzled me over the years have involved coercion at some level. Much of my confusion was due to how JavaScript handles Ad hoc polymorphism. This form of polymorphism uses the context of execution to help shape the outcome. Specifically JavaScript uses overloading to shift the behavior of its operators depending on how they are called. 

For example the binary operator can be used for summation or concatenation, but it also coerces values in the process. Much of the confusion over coercion is knowing how or when it occurs. **In JavaScript coercion is always about simplifying complex objects to a primitive form or converting between two primitive types.** You cannot coerce a number into array, but you can coerce an array into a number. The following examples help explain the various ways JavaScript coerces values.

### To string
JavaScript uses the binary operator to concatenate two values together. However, to make this work JavaScript first silently coerces the zero into a string. When JavaScript attempts to convert an object to a string it calls the toString() method first. If `toString()` does not return a primitive representation it defers to the `valueOf()` function. If the `valueOf()` function cannot produce a primitive value either, then JavaScript throws a TypeError exception.

```javascript
// => '0'
var s = ''+0;
```

### To number
The unary operator's job is to convert the operand that follows into a number. Like the concatenation process above, this also involves coercing the object into a primitive form, this time a number. This is the equivalent of writing `1*'10'`. Just as in the string conversion process, JavaScript relies on the results of toString() or `valueOf()`. However the order is reversed, JavaScript calls `valueOf()` first and then `toString()`. Here is a simple example:

```javascript
// => 10
console.log(+'10');
```

### Context aware coercion
Many built-in core objects can be coerced, and therefore as a result support unary and binary operations. The coerced object tailors the return values of `valueOf()` and `toString()` to be contextually meaningful. Take the built-in Date object for example. When converting the object to a primitive number it returns the milliseconds since epoch, which is a useful result for performing calculations.

```javascript
// => 1373558473636
console.log(+new Date());
```

However a string representation of epoch is not as useful, therefore when a date is converted to a string the object returns a textual representation of the current date and time.

```javascript
// => Thu Jul 11 2013 11:01:13 GMT-0500 (CDT)
console.log(new Date() + '');
```

### Coercion Gotchas
Knowing the order of operations for type conversion should give us the ability to create meaningful conversion values for our own objects. That way when our object is coerced just like the built-in Date object it can return a contextual aware result. However, as you'll see in the following code it turns out to be harder to do than it appears at first blush:

```javascript
const Money = function (val, sym) {
    this.currencySymbol = sym;
    this.cents = val;
};

let dollar = new Money(100, '$');

// Not helpful
// => NaN
console.log(+dollar);

// Not helpful
// => Total: [object Object]
console.log("Total: " + dollar);

Money.prototype.toString = function () {
    return this.currencySymbol + (this.cents / 100).toFixed(2);
};

Money.prototype.valueOf = function () {
    return this.cents;
};

// Helpful!
// => 100
console.log(+dollar);

// Wait what?! I wanted $1.00
// => 100
console.log(dollar + '');

// Now I am totally confused!
// => $1.00
console.log([dollar] + '');
```

The order in which the conversion occurs seems to be at odds with what we learned in the Date examples above. To get an answer we need to look at the steps JavaScript takes when coercing this object into a String. Here operator overloading again is the problem. We would assume that because we are concatenating a string JavaScript would use `toString()` instead of `valueOf()`, like it does for the Date object. Here is what the spec says in regards to type conversion:

----
The abstract operation ToPrimitive takes an input argument and an optional argument PreferredType. The abstract operation ToPrimitive converts its input argument to a non-Object type. If an object is capable of converting to more than one primitive type, it may use the optional hint PreferredType to favour that type.


In our case conversion of the object follows the following sequence:

----
Return a default value for the Object. The default value of an object is retrieved by calling the `[[DefaultValue]]` internal method of the object, passing the optional hint PreferredType. The behaviour of the `[[DefaultValue]]` internal method is defined by this specification for all native ECMAScript objects in 8.12.8.


So it seems that we need to understand how DefaultValue is derived in our object. Digging ever deeper in the spec we find that JavaScript has two ways of determining a DefaultValue, one for string and the other for numbers. It makes this decision based on the hint argument supplied to the DefaultValue method. If hint is not supplied then JavaScript defaults to a Number. Below is a hypothetical version of the `ToPrimitive()` method might look like:

```javascript
var ToPrimitive;

ToPrimitive = function (obj) {
    var funct, functions, val, _i, _len;
    functions = ["valueOf", "toString"];
    if (typeof obj === "object") {
        if (obj instanceof Date) {
            functions = ["toString", "valueOf"];
        }
        for (_i = 0, _len = functions.length; _i < _len; _i++) {
            funct = functions[_i];
            if (typeof obj[funct] === "function") {
                val = obj[funct]();
                if (typeof val === "string" || typeof val === "number" || typeof val === "boolean") {
                    return val;
                }
            }
        }
        throw new Error("DefaultValue is ambigious.");
    }
    return obj;
};

// => 1 (as string)
console.log(ToPrimitive([1]));

// => Thu Jul 11 2013 15:55:11 GMT-0500 (CDT) 
console.log(ToPrimitive(new Date()));
```

Now we can understand why the concatenation of our object fails to use our custom toString() method. It is because without specifying a hint for the internal DefaultValue function JavaScript assumes we want a number. This results in a call to valueOf() instead. All we need to do now is figure out how to set the hint to a string, the same way the built-in Date object does. Unfortunately, there is no way to specify a hint for custom objects! At the bottom of the DefaultValue method description we find this warning:
When the `[[DefaultValue]]` internal method of O is called with no hint, then it behaves as if the hint were Number, unless O is a Date object (see 15.9.6), in which case it behaves as if the hint were String.

The above specification of `[[DefaultValue]]` for native objects can return only primitive values. If a host object implements its own `[[DefaultValue]]` internal method, it must ensure that its `[[DefaultValue]]` internal method can return only primitive values.

We have now found a limitation in JavaScript that we cannot get around (at least not in an elegant way). With no built-in way to specify a hint to the DefaultValue function our object cannot prefer toString() the same way the Date object does. All is not lost though, if you refer back to the example above you will see we did ultimately find a way to get the dollar object to concatenate in the manner we wanted. Oddly, it worked if we first wrapped the object in an array. Only then would JavaScript correctly coerce the value using the toString() method, but why? Here is a hint:

```javascript
// => object
console.log(typeof [1].valueOf());

// => string
console.log(typeof [1].toString())
```

Did you figure it out? Remember that the rules of ToPrimitive say that the function must return a primitive value. The Array's valueOf() method however returns an object, this causes the ToPrimitive function to move on and call toString(). The subsequent call to toString() does return the desired primitive value. Internally, the array's toString() function must be iterating over all the elements in its collection and calling toString() on each them. This theory is easy to test; we can simply push an object into an array which cannot be coerced into a string:

```javascript
var noConversions = [{
    toString: undefined
}];

// => Uncaught TypeError: Cannot convert object to primitive value 
console.log(noConversions + '');
```

As expected the attempted coercion throws an error.

#### Mixed type comparison through coercion
Up to this point we have been talking about coercion as it applies to type conversion for summation or concatenation. However, the equals operator also coerces the operands into primitive values before performing the equality test. Consider the following examples: 

```javascript
// => true
console.log([1] == 1);

// => true
console.log([1] == "1");

// => true
console.log([{
    toString: function () {
        return 1;
    }
}] == "1");

// => false
console.log([1] === 1);

// => false
console.log([1] === "1");

// => false
console.log([{
    toString: function () {
        return 1;
    }
}] === "1");
```

It can be worrisome that an object can essentially be equal to a primitive value through coercion, but at least now you know what that occurs. Moreover, you can see why comparing values using the strictly equals operator is promoted so heavily in JavaScript best practices.

#### Complex Coercion
Now that we have the basics of coercion down let's try an advanced example and by advanced I mean mind-numbingly obtuse. Consider this gem:

```javascript
// => '10'
++[[]][+[]]+[+[]]
```

The best way to understand what is going on, is to unpack the innards first and work outwards. First, we'll look at the inner arrays starting from left to right:

```javascript
// => [Array[0]]
[[]]

// An array which contains a single value, a coersed zero thanks to the unary operation.
// => [0]
[+[]]

// A second array also containing a coersed zero.
// => [0]
[+[]]
```
Next we'll ponder the two operands on either side of the binary operator. Starting with the left:

```javascript
// => 1
++[[]]['0']
```

This statement is a tiny bit tricky. Essentially what is happening is that the inner array is being accessed at index '0' and being returned. At the point of return the left-hand unary operator is incrementing it, which also changes it to a number. Now all that is left is to combine the two remaining values together. Since the left operand is a number and the right is an array, the combination will be through concatenation not summation. Therefore the final sequence looks like this:

```javascript
// => '10'
1 + ['0']
```

Now that we have a basic understanding of why this is jargon, because it performs a tasks through a deep understanding of the internal coercion mechanics. Let's move on to the topic of logical operators to understand the role they play in programmatic jargon.

### Logical Operators
Logical operators are used to return Boolean values, but under certain conditions they can be used to short-circuit control flows within a statement. This short-circuiting often shortens code but at the expense of being expressive. In this way logical operators are perfect for creating programmatic jargon. The following section steps through the various logical operators to explain how they can be used to produce jargon.

##### Logical AND (&&)
The logical OR and logical AND are both used for chaining comparisons that will return a Boolean. In the case of logical AND all conditional evaluations must be true otherwise false will be returned.
Assignments through comparisons or implicit fallback
Knowing the behavior of && it becomes possible to leverage both the chaining and the return value in a single statement.

```javascript
var car = {
    hasWheels: function () {
        return true;
    },
    engineRunning: function () {
        return true;
    },
    wheelsTurning: function () {
        return true;
    }

};

if (car.inMotion = car.hasWheels() && car.engineRunning() && car.wheelsTurning()) {
    // make vrrrooommmm noise with your mouth
}
```

Though the code above is technically correct, it is not considered good practice to have an assignment statement inside a conditional expression. This is because people often misread assignment statements as an equality comparison, which can lead to confusion.

#### Logical OR (||)
Much like the logical AND operator the logical OR operator can be used as a control flow mechanism, one that compares operands from left to right looking for the first true value. Unlike the AND operator the OR operator only needs one operand to be true for a success.

#### Default values 
A common way that the logical OR is used is to assign default values to variables which many be considered optional in the method signature. The OR operator will test the left-hand operand, and finding an undefined will look for a value that can be coerced into a Boolean. Once found the value is assigned the variable.

```javascript
var Car = function(){
    var args = Array.prototype.slice.call(arguments);

    this.name = args[0] || 'tesla'
    this.mpg = args[1] || 100
    this.mph = args[2] || 80
    
    // => Volt
    console.log(this.name);
    
    // => 90
    console.log(this.mpg);
    
    // => 80
    console.log(this.mph);
}

new Car('Volt',90);
```

#### Logical NOT (!)
The logical not operator expects a single right operand, that is a Boolean value or can be coerced into one. It will return true only if the operand is false.

#### Shorthand Boolean
As we saw in the section on coercion, implicit type conversion can be hard to understand by just reading the code. One of the most widespread conventions I see in JavaScript is using the logical NOT as a shortcut to a Boolean. Consider the following ways the NOT operator can coerce and then express Boolean values.

```javascript
// number is coerced to a Boolean false
// NOT inverts it to true
// => true
console.log(!0);

// number is coerced to a Boolean true
// NOT inverts it to false
// => false
console.log(!1);

// number is coerced to a Boolean true
// NOT inverts it to false
// => false
console.log(!-1);

// string is coerced to a Boolean truthy *something*
// NOT inverts it to false
// => false
console.log(!'0');

// string is coerced to a Boolean truthy *something*
// NOT inverts it to false
// => false
console.log(!'1');

// this is coerced to a Boolean falsey *nothing*
// NOT inverts it to true
// => true
console.log(!undefined);

// this is coerced to a Boolean truthy *something*
// NOT inverts it to true
// => false
console.log(!this);

// unary operator coerces empty array into zero
// zero is coerced into Boolean false
// NOT inverts it to true
// => true
console.log(!+[]);

// inner NOT coerces the empty array to false
// false is not a valid array index so undefined is returned
// undefined is coerced into Boolean false
// NOT inverts it to true
// => true
console.log(![][![]]);
```

#### Double NOTs
As we saw in the last example the logical NOT operator can cast many kinds of entities to variables, including undefined variables. Knowing this allows you to treat the lack of a variable as a de facto false variable. In the following example you can see how the use of double NOTs allow the code to treat both the undefined and explicit false Boolean the same way. However, this code is very opaque and what is saves in visual space it loses in conceptual clarity.

```javascript
var user = {
    isAdmin: function () {
        return !!this.admin;
    }
};

// undefined this.admin is coerced to false
// then inverted to true
// then inverted again to false
// => false
console.log(user.isAdmin());

user.admin = true;

// this.admin is true without coercion
// inverted to false
// inverted back to true
// => true
console.log(user.isAdmin());

user.admin = false;

// => false
console.log(user.isAdmin());
```

#### Immediately Invoked Function Expression
Using the logical NOT operator you can write a more succinct version of an immediately invoked function expression. In this case the logical NOT tells the parser to treat the function not as a function declaration but an expression, which afford the a new execution context.

```javascript
// Uncaught SyntaxError: Unexpected token (
function(){console.log('foo')}()

// => foo
!function(){console.log('foo')}()
```

Now that we have taken this section to its logical conclusion, we can transition to some of the real back roads of JavaScript, better known as bitwise operations. 

#### Bit twiddling
Just like it sounds, a bitwise operation is the process of working with data at the bit level. Generally, this is useful to algorithms that require fast execution, and/or have limited resources in which to operate. Specifically, these operations must only require primitive transformations to data to benefit from this kind of manipulation. Bitwise operations are standard fare for many low-level tasks including communicating over sockets, compressing or encrypting information, or manipulating bitmap graphics. It is also very common to see bitwise operations used to implement role based access control (RBAC) systems, because their access permissions can be described using only a bit field, and yet remain a single number in the database.

The bitwise operators come in four distinct flavors NOT, AND, OR and XOR respectively. In addition to the logical operators JavaScript has left and right bit shifting operators too. As you might expect properly explaining the hows and whys of these operators is quite involved and must also include understanding how   bit shifting works in general. As such, it is outside the scope of this chapter. Instead, we will maintain our focus on jargon expressions, but now with an emphasis on the use bitwise operations. What follows are example and explanations of bit twiddling jargon.

#### Bitwise AND (&)
The bitwise OR function will return a one in each bit position where both operands have a one in the specified position.

#### Converting Hex to RGB
Occasionally it's useful to convert a hex number to a RGB value, for example in the service of a CSS class.

```javascript
// my favorite hex color
var color = 0xC0FFEE;

// Red
// => 192
console.log((color>>16) & 0xFF);

// Green
// => 255
console.log((color>>8) & 0xFF)

// Blue
// => 238
console.log(color & 0xFF)
```

We can extend this function a bit further and create a gradient factory that returns a gradient of colors when given a beginning and end color and a number of stops.

```javascript
var GradientFactory = (function () {
    var _beginColor = {
        red: 0,
        green: 0,
        blue: 0
    };
    var _endColor = {
        red: 255,
        green: 255,
        blue: 255
    };
    var _colorStops = 24;
    var _colors = [];
    var _colorKeys = ['red', 'green', 'blue'];
    var _rgbToHex = function (r, g, b) {
        return '#' + _byteToHex(r) + _byteToHex(g) + _byteToHex(b);
    };
    var _byteToHex = function (n) {
        var hexVals = "0123456789ABCDEF";
        return String(hexVals.substr((n >> 4) & 0x0F, 1)) + hexVals.substr(n & 0x0F, 1);
    };
    var _parseColor = function (color) {
        if ((color).toString() === "[object Object]") {
            return color;
        } else {
            color = (color.charAt(0) == "#") ? color.substring(1, 7) : color;
            return {
                red: parseInt((color).substring(0, 2), 16),
                green: parseInt((color).substring(2, 4), 16),
                blue: parseInt((color).substring(4, 6), 16)
            };
        }
    };
    var _generate = function (opts) {
        var _colors = [];
        var options = opts || {};
        var diff = {
            red: 0,
            green: 0,
            blue: 0
        };
        var len = _colorKeys.length;
        var pOffset = 0;
        if (typeof (options.from) !== 'undefined') {
            _beginColor = _parseColor(options.from);
        }
        if (typeof (options.to) !== 'undefined') {
            _endColor = _parseColor(options.to);
        }
        if (typeof (options.stops) !== 'undefined') {
            _colorStops = options.stops;
        }
        _colorStops = Math.max(1, _colorStops - 1);
        for (var x = 0; x < _colorStops; x++) {
            pOffset = parseFloat(x, 10) / _colorStops;
            for (var y = 0; y < len; y++) {
                diff[_colorKeys[y]] = _endColor[_colorKeys[y]] - _beginColor[_colorKeys[y]];
                diff[_colorKeys[y]] = (diff[_colorKeys[y]] * pOffset) + _beginColor[_colorKeys[y]];
            }
            _colors.push(_rgbToHex(diff.red, diff.green, diff.blue));
        }
        _colors.push(_rgbToHex(_endColor.red, _endColor.green, _endColor.blue));
        return _colors;
    };
    return {
        generate: _generate
    };
}).call(this);

// From hex to hex
// => ["#000000", "#262626", "#4C4C4C", "#727272", "#999999"] 
console.log(GradientFactory.generate({
    from: '#000000',
    to: '#999999',
    stops: 5
}));

// From color object to hex
// => ["#C0FFEE", "#CFFFF2", "#DFFFF6", "#EFFFFA", "#FFFFFF"] 
console.log(GradientFactory.generate({
    from: {
        red: 192,
        green: 255,
        blue: 238
    },
    to: {
        red: 255,
        green: 255,
        blue: 255
    },
    stops: 5
}));
```

#### Bitwise OR (|)
The bitwise OR function will return a one in each bit position where either of the two operands have a one in the specified position.
Truncating numbers
As we learned in the previous section this function performs a bitwise OR operation on a pair of bits. This can also be used to round numbers down.

```javascript
// => 30
var x = (30.9|0) 
```

#### Bitwise XOR (^)
The following examples make use of the fact that the bitwise XOR operator returns a one in the place where a specific bit of two bit patterns do not match.
Determine sign equality
This expression is an easy way to determine if two operands have opposing signs. The reason this works is because JavaScript uses two's compliment to represent negative numbers, which makes the XOR possible.

```javascript
var signsMatch = function (x, y) {
    return !((x ^ y) < 0);
};

// => false
console.log(signsMatch(10, -10));

// => true
console.log(signsMatch(0, 0));

// => true
console.log(signsMatch(0, -0));

// => true
console.log(signsMatch(-10, -10));

// => true
console.log(signsMatch(1, 1e0));

// => false
console.log(signsMatch(-1, 1e0));
```

#### Toggling bits
Occasionally you will see the XOR operator used to toggle bits. This can be helpful for toggling the state of an object. For example:

```javascript
var light = {
    on: 1,
    toggle: function () {
        return this.on ^= 1;
    }
};

// => 0
console.log(light.toggle());

// => 1
console.log(light.toggle());

// => 0
console.log(light.toggle());
```

#### Bitwise NOT (~)
The bitwise NOT function essentially swaps the sign of a number and subtracts one from it. Behind the scenes actually JavaScript converts the operand into a binary representation and then computes a new number by swapping all the bits from one to zero and vice versa. This new number is called the one's complement of the original. Finally, the one's complement is converted back into a base 10 number. Knowing the behavior of NOT gives us some clever ways to exploit it.

#### Bitwise arithmetic
Occasionally you will see developers using the bitwise NOT to perform arithmetic on a variable. For example:

```javascript
// => 9
~-10

// => 11
-~10

// => 18
2*~-10
```

#### Parsing strings into numbers
The bitwise NOT operator will return the inverted value of the operand. Strings will be coerced as part of this process. Therefore supplying a double NOT will return the number its original sign.

```javascript
var num = "100.7"

// => true
console.log(parseInt(num,10) === ~~num);
```

#### Bitwise Shifting (<<, >>, >>>)

#### Signum function
The purpose of the signum (also called sign) function is to determine if a number is less, equal to or greater than zero, and therefore can return `-1`, `0` or `1` as a result.

```javascript
var sign = function(x) {
    return (x >> 31) | ((-x) >>> 31);
};

// => -1
console.log(sign(-100));

// => 0
console.log(sign(0));

// => 1
console.log(sign(100));
```

While we used bit shifting to calculate the sign of a number you could also use two plain old terinary expressions grouped together:

```javascript
// => 1
console.log(100 ? 100 < 0 ? -1 : 1 : 0);
```

Now that we know the function works let's figure out why. First consider the right shift operator. The job of this operator is to shift the operand the specified number of bits, in our case 31 places. Because we are using the end of the bit field, positive numbers will always return 0 and negative numbers will always return `-1`. Here are a couple of examples:

```javascript
// => -1
console.log(-200 >> 31);

// => -1
console.log(-100 >> 31);

// => 0
console.log(0 >> 31);

// => 0
console.log(100 >> 31);

// => 0
console.log(200 >> 31);
```

Next we use the zero-fill right shift operator >>> to shift 31 bits to the right, and shifting any zeros needed in from the left. Again you can see this play out in the following code:

```javascript
// => 1
console.log(-200 >>> 31);

// => 1
console.log(-100 >>> 31);

// => 0
console.log(0 >>> 31);

// => 0
console.log(100 >>> 31);

// => 0
console.log(200 >>> 31);
```

Finally, to get our return value we utilize the bitwise OR operator. However, we do not get the results expected unless we reverse the sign of the number to the right of the OR operand. Therefore our simplified function would like this:

```javascript
// => -1
console.log(-200 >> 31 | 200 >>> 31);

// => -1
console.log(-100 >> 31 | 100 >>> 31);

// => 0
console.log(0 >> 31 | 0 >>> 31);

// => 1
console.log(100 >> 31 | -100 >>> 31);

// => 1
console.log(200 >> 31 | -200 >>> 31);
```

#### Opaque code
It is possible in any language to write obtuse or obfuscated code. There are whole communities dedicated to these pursuits. For example, hackers use hard to read code as a layer of defense against white hats. Others find sport in writing cryptic code. There is an entire pastime called Programming Golf, where players attempt to return the desired result of a function (hole) in the shortest number of characters (strokes). What follows are examples of purposely muddy syntax for the sheer sport of it. Many of these examples are what might be considered true WAT examples in JavaScript. Many examples were inspired by the site wtfjs.com.

#### Sneaky eval
As the name implies this function gives the executing code a backdoor to access eval. Some sites attempt to give users a sanitized subset of JavaScript to us. This is very hard if not impossible to do with a dynamic language like JavaScript and this code demonstrates this script works by accessing the constructor function of the String.sub method. The JavaScript constructor method accepts a string which is then evaluated in place.

```javascript
// => foo
""["sub"]["constructor"]("console.log('foo')")()
```

#### All your base
Careful when comparing numbers of different bases. For example here we compare an octal number and one using scientific notation to a base ten number. Unless you read it carefully you might be confused at the results.

```javascript
// comparing against octals
// => false
1 + 064 == 65

// => false
064 > 60

// comparing against scientific notation
// => false
3000000000 > 4e9
Unicode for variables
JavaScript allows Unicode to be used as property descriptors and variable names. This can lead to some spectacularly unreadable code. Consider the following:
var\u1000 = {\u1001: function () {
        return 'Unicode';
    }
};
// => 'Unicode'
console.log(\u1000.\u1001());
```

#### WAT indeed
Even the Unicode example above might be a bit hard to read it cannot compare to what follows. The fact that this code somehow produces the word 'secret' seems almost magical. This code was generated by a program called jsfuck  which, judging by the name was inspired by the equally offensively yet aptly titled Brainfuck language . This is truly code to make even the most seasoned developer say WAT!

```javascript
// => 'secret'
console.log((![]+[])[+[[!+[]+!+[]+!+[]]]]+(!![]+[])[+[[!+[]+!+[]+!+[]]]]+([][(![]+[])[+[[+[]]]]+([][[]]+[])[+[[!+[]+!+[]+!+[]+!+[]+!+[]]]]+(![]+[])[+[[!+[]+!+[]]]]+(!![]+[])[+[[+[]]]]+(!![]+[])[+[[!+[]+!+[]+!+[]]]]+(!![]+[])[+[[+!+[]]]]]+[])[+[[!+[]+!+[]+!+[]]]]+(!![]+[])[+[[+!+[]]]]+(!![]+[])[+[[!+[]+!+[]+!+[]]]]+(!![]+[])[+[[+[]]]])
```

To understand how this code ultimately produces the string 'secret' we need to simply figure out the mechanism by which it does its converting. As it turns out it is easier than it first appears. For the sake of brevity let's just figure out how to reproduce the leter "s" in our hidden word. In the code "s" can be found in this expression: `(![]+[])[+[[!+[]+!+[]+!+[]]]]`. Knowing what we do about coercion and unary operations we can begin to step through this code little by little. First we'll look at the inner arrays: 

```javascript
// => [3]
[!+[]+!+[]+!+[]]
```
We know that unary operator will convert the empty array into a number in this case a zero. Next we see the logical NOT operator, which we know gives us the opposite Boolean value of the operand. In our case the operand is coerced into `false`, which the NOT operator dutifully flips to `true`. This leaves us with the equation `true + true + true`. Next the binary operator adds the true values together, which first requires coercing them to numbers. That means `true + true + true` is now `1 + 1 + 1`. Adding them all together gives us `3`. The following code proves what I just stepped through:

```javascript
// => true
+[[!+[]+!+[]+!+[]]] == [3]
```

Next we need to understand what is happening inside the parenthesis. Again this is pretty easy to figure out once you break it down. First we'll consider:

```javascript
// => false
!+[]
```
Ok that's clear, we saw the same sequence above. However in this version the Boolean false is concatenated with the empty array. This means the Boolean false becomes the string `"false."` Now is essence or code has been simplified to a string being accessed like an array to get the fourth item, which is the letter "s" we were after, success!

```javascript
// => 's'
("false")[3]

// => true
"s" == (![]+[])[+[[!+[]+!+[]+!+[]]]]
```

I encourage you to look at the source for the jsfuck  project because there is some interesting ship in a bottle style contortions used to get all the required characters needed, to fully encode anything. Some of the encodings are pretty epic. For example: 
```javascript
// => true
'(' == ([][(![]+[])[+[[+[]]]]+([][[]]+[])[+[[!+[]+!+[]+!+[]+!+[]+!+[]]]]+(![]+[])[+[[!+[]+!+[]]]]+(!![]+[])[+[[+[]]]]+(!![]+[])[+[[!+[]+!+[]+!+[]]]]+(!![]+[])[+[[+!+[]]]]]+[])[+[[+!+[]]]+[[!+[]+!+[]+!+[]+!+[]+!+[]]]]
```
That's 209 characters to encode a single right parenthesis, WAT indeed!

#### Summary
We just spent an entire chapter learning about coercion, bitwise operations and logical operators. We can better understand why there is often a disconnection between the actual and expected results when employing these features of JavaScript. Programmers who are adept at exploiting these nuances can often pack very complex behavior into a just a couple of characters. As I mentioned in my introduction this highly contextual code is what I am calling programmatic jargon but others have derogatorily tagged as WAT style programming. Here are a couple of points to remember when trying to use or read jargon in JavaScript.

1. Programmatic jargon is a compression of code through the use of highly specific often technical rules of the language.
2. Jargon is neither good nor bad, it depends on whether the speaker and the audience both understand the context and point of reference for the expression.
3. Coercion is the act of forcing an object or entity of one type into another.
4. In JavaScript coercion is either for simplifying complex objects to a primitive form or converting between two primitive types.
5. Logical operators are used to return Boolean values, but under certain conditions they can be used to short-circuit control flows within a statement.
6. Bitwise operations can only be performed on integers.
7. Bitwise operations are useful to algorithms that require fast execution, and/or have limited resources in which to operate.
8. Bitwise operations can often be used in place of other math related functions, for example using `~~'10'` instead of `parseInt('10')`.

Additional References
1. [http://rocha.la/JavaScript-bitwise-operators-in-practice](http://rocha.la/JavaScript-bitwise-operators-in-practicehttp://rocha.la/JavaScript-bitwise-operators-in-practice)
2. [http://sla.ckers.org/forum/read.php?24,32930](http://sla.ckers.org/forum/read.php?24,32930)
3. [http://javascriptissexy.com/12-simple-yet-powerful-javascript-tips/](http://javascriptissexy.com/12-simple-yet-powerful-javascript-tips/)
4. [http://codegolf.stackexchange.com/questions/2682/tips-for-golfing-in-javascript](http://codegolf.stackexchange.com/questions/2682/tips-for-golfing-in-javascript)
5.	[http://stackoverflow.com/questions/2350718/are-there-any-short-tricks-in-javascript-1-8-that-i-can-use-to-reduce-my-golf](http://stackoverflow.com/questions/2350718/are-there-any-short-tricks-in-javascript-1-8-that-i-can-use-to-reduce-my-golf)
6.	[http://www.benlesh.com/2012/05/javascript-fun-part-6-code-golf.html?m=1](http://www.benlesh.com/2012/05/javascript-fun-part-6-code-golf.html?m=1)
7.	[http://wtfjs.com/](http://wtfjs.com/)
8.	[http://jscoercion.qfox.nl/](http://jscoercion.qfox.nl/)

