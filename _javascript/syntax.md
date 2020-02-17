---
title: Javascript (Basic) Syntax
---

This page contains handy information on the basics of the Javascript Syntax.


# Variables
Can be named anything that starts with a letter, underscore ( `_` ), or dollar sign ( `$` ).

`var monster1 = "Grover", monster2 = "Cookie Monster";`

**camelCase** is a common naming convention used for Javascript variables.

You cannot use reserved words (such as `var`) for variable names.

## Variable Scopes

Variables can either be **global** or **local**.
 
The use of global variables is generally harmful. Global variables are shared amongst all of the scripts that are running, and as a result you might get unexpected behavior if another script is using a variable with the same name.

### Global Variables

If you assign a variable without using a keyword (such as `var`) it will be created as a global variable.

{% highlight javascript %}

    // Global Variable
    myPet = "cat";

{% endhighlight %}

If you have enabled **strict mode** (by adding `"use strict";` at the top of your file) you will get a warning if you using any global variables.

### Local Variables

To define a local variable use the keyword `var`.

{% highlight javascript %}

    // Local variable
    var myState = "ACT";

{% endhighlight %}

Functions are the main delimiter of variable scope when using the `var` keyword.

In ECMAScript 2015 or above you can also use `let` and/or `const`, these have *block* scope.

# Strings
String literals are Strings which are contained within quotation marks, for example:

`var myString = "A String Literal";`

## Escaping and Multi-line Strings

The backslash ( `\` ) is the Javascript escape character.

`"This is Joes' \"favorite\" string";`

The backslash ( `\` ) can also be used for strings that run over multiple lines.

{% highlight javascript %}
    
    This is \
    Joe'/s favorite \
    string EVER
{% endhighlight %}

## String properties

String properties are accessed using the dot ( `.` ) notation to access the desired property.

{% highlight javascript %}
    
    var myString = "This is my string.";
    myString.length //returns 18
{% endhighlight %}

## String methods

String methods are accessed similar to **Java** using the format *string.method()*, for example:

{% highlight javascript %}
    
    var myString = "This is my string.";
    myString.toUpperCase() //returns "THIS IS MY STRING."
{% endhighlight %}

## String concatenation

To concatenate Strings use the plus ( `+` ) symbol.

{% highlight javascript %}
    
    "Cat " + "Dog"; //returns "Cat Dog"
{% endhighlight %}

# Numbers

Unlike other programming languages, all numbers in Javascript are of the same type called **number**.

Javascript can also represent the number infinity, by using the keyword `Infinity`.

Javascript also as the concept of 'Not a Number', which is represented by the keyword `NaN`.

## The `Number` Object

There is a built in `Number` object in Javascript which has some handy methods.

The `Number.isNaN(number)` method is handy for checking if the number is a valid number.

{% highlight javascript %}
    
    Number.isNaN(NaN); // returns true
{% endhighlight %}

## The `Math` Object

There is a global object called `Math` which contains a heap of properties and methods for dealing with number types.

{% highlight javascript %}
    
    Math.round(12.522141415254) // returns 13
{% endhighlight %}

# Booleans

Booleans are either `true` or `false` values, which must be spelt correctly and be all lowercase.

# Objects

Javascript uses objects to represent and structure data beyond simple types such as booleans and Strings.

You can define object literals by using the curly braces ( `{}` ), for example:

{% highlight javascript %}
    
    var myEmptyObject = {}; //Empty object
    var notEmptyObject = {
        'label': 'value',
        'label2': 25,
        'label3': true,
    };
{% endhighlight %}

You can also defined the labels in an object without using quotation marks like this:

{% highlight javascript %}
    
    var bird = {
        commonName: 'raven',
        callType: "squawky",
        quote: "Nevermore",
        noisy: true,
        deadly: false,
    };
{% endhighlight %}

## Object Properties

To retrieve a property from an object you can use either dot notation or square braces. Square braces is useful if you have spaces or special characters in the property name.

{% highlight javascript %}
    
    // Dot notation
    bird.quote  // returns "Nevermore"

    // Square braces
    bird["quote"]   //returns "Nevermore"  
{% endhighlight %}

### Adding or Deleting properties

You can add new properties to an object by just using assignment.

{% highlight javascript %}
    
    // Dot notation
    bird.whereItLives = "in a tree" 
{% endhighlight %}

You remove properties from an object by using the `delete` keyword. Deleting works with either Dot or Square braces notation.

{% highlight javascript %}
    
    //Removes the whereItLives property from the object
    delete bird.whereItLives;
{% endhighlight %}

## Object References

Be aware that variables are really references to the object (similar to the Java programming language).

This means that if you assign one variable to equal another, they both refer to the same object.

Objects (and Arrays) are **passed by reference** to functions. Take this into consideration if you have a case where you do not want to modify the original object. In this case you may want to create and return a new object.

## Copying objects

A common quick way to make an exact copy of an object is by using the JSON object as follows:

{% highlight javascript %}
    
    //Makes a copy of the animal object safely.
    animal2 = JSON.parse(JSON.stringify(animal));
{% endhighlight %}

# Arrays

Arrays allow you to store an ordered list of data of any type.
The order of the elements are preserved and the keys are automatically assigned.
An array can also store mixed data types (e.g. booleans, Strings, and objects can all exist in the same array).

## Defining an Array

{% highlight javascript %}
    //Empty Array
    var myArray = [];

    var daysOfTheWeek = ['Sunday', 'Monday', 'Tuesday'];

    // Array storing mixed types
    var arrayOfStuff = [
        {'name': 'value'},
        [1,2,3],
        true,
        'nifty'
    ];
{% endhighlight %}

## Accessing Array Items

Arrays use a 0 based index, so the index of the first element an array is 0.

You access the elements in the array using the square braces notation.

{% highlight javascript %}
    var chips = [
        'Smiths',
        'Doritos',
        'Twisties',
        'Thins'
    ];

    chips[2] //returns 'Twisties'
{% endhighlight %}

## Adding items to an Array

There are two ways that you can add elements to an array, using the square braces notation or the `push()` method.

{% highlight javascript %}
    var chips = [
        'Smiths',
        'Doritos',
        'Twisties',
        'Thins'
    ];

    // Adds "Red Rock Deli" to the end of the array
    chips[chips.length] = "Red Rock Deli"

    // Adds "Lays" to the end of the array
    chips.push("Lays"); //returns 6, the number of items in the array
{% endhighlight %}

## Removing an item from the end of an Array

To remove (and return) the last item from an array use the `pop()` method.

{% highlight javascript %}
    var chips = [
        'Smiths',
        'Doritos',
        'Twisties',
        'Thins'
    ];

    // Removes and returns the last item in the array
    chips.pop() // returns 'Thins' 
{% endhighlight %}

## Removing items from the middle of an Array

If you simply `delete` a particular index in an array (e.g. `delete chips[2]`) it will not change the size of the array, it will just replace the value of that index with `empty`.

To remove an item and reduce the arrays length use the `splice(index, items)` method.

{% highlight javascript %}
    var chips = [
        'Smiths',
        'Doritos',
        'Twisties',
        'Thins'
    ];

    // Starting at index 2 removes 1 item from the array
    chips.splice(2, 1); // returns 'Twisties'

    chips.length // returns 3.
{% endhighlight %}

## `map` method

The `map` method accepts a callback function which operates on each item in the array and returns a new array.

{% highlight javascript %}
    
    function doubleIt(number) {
        return (number *= 2);
    }

    var myNumbers = [1, 2, 3, 4, 5];

    var myDoubles = myNumbers.map(doubleIt);

    myDoubles; // Returns [2, 4, 6, 8, 10]

{% endhighlight %}

## 'forEach' method

Operates on each element in an array, but does not return anything.

Note that the `break` keyword will not work inside the `foreach()` method.

# Comments

Javascript supports two styles of comments, line comments and block comments.

Be careful when using block comments if you have valid `*/` characters in your code.

{% highlight javascript %}
    // This is a line comment

    /*
        This is a block comment,
        which can be multiple lines
    */
{% endhighlight %}

# Regular Expressions

To define a regular expression you use forward slashes ( `/` ) around the regular expression you want to use. Any flags appear after the closing forward slash.

{% highlight javascript %}
    
    var string1 = 'Wow, this is the longest string Ever.';
    
    // This is a regular expression
    var regex = /ever/;

    console.log( regex.test(string1) ); //returns false

    // a case insensitive regular expression
    regex = /ever/i;

    console.log( regex.test(string1) ); //returns true

{% endhighlight %}

# Simple Comparisons

## Strict Equality ( `===` )

Strict equality compares two values for equality. If the values have the same type, are not numbers, and have the same value, they're considered equal. Finally, if both values are numbers, they're considered equal if they're both not NaN and are the same value, or if one is +0 and one is -0.

For more information see the [Mozilla Developer Equality page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness).

The **Strict Equality** operator ( `===` ) test whether the thing on the left is identical to the thing on the right. 

There is also a **Strict Inequality** operator ( `!==` ) which test whether the thing on the left is *not* identical to the thing on the right.

{% highlight javascript %}
    
    var one = 1, two = 2;

    one === one; // Returns true

    one !== one; // Returns false

    one !== two; // Returns true

{% endhighlight %}

## Loose Equality ( `==` )

Loose equality compares two values for equality, **after converting** both values to a common type. After conversions (one or both sides may undergo conversions), the final equality comparison is performed exactly as `===` performs it.

For more information see the [Mozilla Developer Equality page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness).

{% highlight javascript %}
    
    var one = 1, two = 2;

    one == one; // Returns true

    one == "1"; // Returns true

    one != "1"; // Returns false

{% endhighlight %}

# Arithmetic Operators

In general Javascript arithmetic operators use the same syntax as **Java**.

## Modulus Operator ( `%` )

The modulus operator ( `%` ) return the remainder after the division.

{% highlight javascript %}
    
    20 % 2; // Returns 0

    21 % 2; // Returns 1

    //Using modules to test if a number is even or odd
     10 % 2 === 0; // Returns true

{% endhighlight %}

## Incrementing and Decrementing

{% highlight javascript %}
    
    var counter = 1;

    counter += 1; // Returns 2

    counter++; //Returns 2, but counter is now 3

    counter *= 2; // Returns 6  

{% endhighlight %}

# Logical Operators

* Logical AND ( `&&` )
* Logical OR ( `||` )
* Logical NOT ( `!` )

# Conditionals

## If Statement

Javascript *if* statements use the same syntax as **Java** if and if-else statements.

{% highlight javascript %}
    var answer = window.confirm("Click OK, get true. Click CANCEL, get false.");
    
    if(answer === true){
        console.log("You said true!");
    } else {
        console.log("You said something else");
    }

{% endhighlight %}

# Switch Statement

Javascript *switch* statements use the same syntax as **Java** switch statements.

Remember that if you do not have a `break` the execution will 'fall through' each case until a `break` statement is encountered.

{% highlight javascript %}
    
    switch (answer){
        case "YES":
            console.log("You said YES!");
            break;
        case "MAYBE":
            console.log("You said MAYBE...");
            break;
        case "NO":
            console.log("You said NO. :(");
            break;
        default:
            console.log("Not sure how to deal with that");
            break;
    }
{% endhighlight %}

# Terse ifs

## Single line ifs

You can leave off the curly braces of an *if* statement and it will execute the next line. Note it only executes a single line.

{% highlight javascript %}
    var cherub = "Cupid";

    if( cherub === "Cupid" ) console.log("Ouch, an arrow!");
    else console.log("I feel nothing");
{% endhighlight %}

## if (variable) 'Truthy'

In JavaScript, a *truthy* value is a value that is considered true when encountered in a Boolean context. All values are truthy unless they are defined as falsy (i.e., except for false, 0, 0n, "", null, undefined, and NaN).

For more information see the [Truthy page on the Mozilla Developer network](https://developer.mozilla.org/en-US/docs/Glossary/Truthy).

{% highlight javascript %}
    if("variable"){
        console.log("The value is truthy!")
    }
{% endhighlight %}

## Ternary Operator

The Javascript ternary operator follows the same syntax as **Java**, it allows for a short form of an if statement.

{% highlight javascript %}
    var animal = "cat";

    animal === "cat"
        ? console.log("You'll need a cat herder!") // Executed if true
        : console.log("Call the dog catcher."); // Executed if false
{% endhighlight %}

# Type Checking

Because Javascript is a **loosely typed** language you may need to check the type of an object before you perform operations on it.

Type checking is done using the `typeof` keyword. The `typeof` function returns a String which is the name of the data type all in lowercase.

{% highlight javascript %}
    var thing = 12;

    typeof thing; // Returns "number"

    thing = "twelve";

    typeof thing; // Returns "string"

    thing = {};

    typeof thing; // Returns "object"

    thing = [];

    typeof thing; // Returns "object"

    typeof NaN; // Returns "number"

    typeof null; // Returns "object"
{% endhighlight %}

## `hasOwnProperty()` method

Every object in Javascript has the method `hasOwnProperty("propertyName")`, this method allows you to check that an object has a particular property prior to accessing it.

{% highlight javascript %}
    var thing = [];

    typeof thing; // Returns "object"

    typeof thing === "object" && thing.hasOwnProperty("length") // Returns true
{% endhighlight %}

# Loops

Remember you can `break` (short circuit) a `for` loop, just the same as you can in **Java**.

## (Sequential) For Loops

The Javascript `for` loop syntax is the same as **Java**.

{% highlight javascript %}
    for (var i = 0; i < 10; i++) {
        console.log(i); // Prints 0...9
    }
{% endhighlight %}

## Enumerative (for each) Loops

Allows you to iterate over an object or array.
Note that the order of the iteration is **not** guaranteed, if you need a guaranteed order use the Sequential For Loop.

{% highlight javascript %}
    //Iterating over an array
    var pageNames = [
        "Home",
        "About Us",
        "Contact",
        "News",
        "Posts"
    ];

    for ( var p in pageNames) {
        console.log(p, pageNames[p]); // p is the index of element
    }

    //Iterating over the properties of an Object
    var pages = {
        first: "Home",
        second: "About Us",
        third: "Contact",
        fourth: "News",
        fifth: "Posts"
    };

    for (var p in pages) {
        if(pages.hasOwnProperty(p)) {
            console.log( p, pages[p] );  // p is the name of the property
        }
    }

{% endhighlight %}

## While Loops

While loops are handy if you don't know how many times you will be iterating over an array or object.

Be careful that if the terminating condition always equates to `true` then the while loop will continue indefinately.

{% highlight javascript %}
    var i = 0;

    while (i < 10 ) { // This is the terminating condition
        console.log(i + "... This will go until we hit 10");
        i += 1;
    }

{% endhighlight %}

## Do While loop

The Do While loop is handy if you want the loop to execute at least once.

{% highlight javascript %}
    var myArray = [true, true, true, false, true, true];

    var myItem = false;

    do {
        console.log("myArray has" +
                      myArray.length +
                        " items now. This loop will go until we pop a false."  
        );
        myItem = myArray.pop();
    }while (myItem !== false);

{% endhighlight %}

# Functions

Functions are one of the fundamental building blocks in JavaScript. A function is a JavaScript procedure—a set of statements that performs a task or calculates a value. 

A function definition (also called a function declaration, or function statement) consists of the `function` keyword, followed by:

* The name of the function.
* A list of parameters to the function, enclosed in parentheses and separated by commas.
* The JavaScript statements that define the function, enclosed in curly brackets, {...}.

For more information see the [Functions Page on Mozilla Developer network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)

{% highlight javascript %}

    // Function Definition
    function speak() {
        console.log('Woof');
        console.log('Meow');
        console.log('Quack');
        console.log('Mooooo');
    }

    // Invoke the function
    speak();
{% endhighlight %}

Functions are ***first class citizens*** in Javascript, they are objects that have the power to be invoked. 

All `functions` in Javascript inherit from the global `Function` object, which means that they are objects.

This also means that functions can be assigned to (and passed around using) variables.  An example of this can be seen when dealing with anonymous functions.

{% highlight javascript %}

    // Anonymous function assigned to a variable
    var say = function(what) {
        what = what || "Speaking!";

        for (var i = 0; i < 10; 1 += 1) {
            console.log(what);
        }
    }

    // Invoke the function
    speak();
{% endhighlight %}

## Return statement

A function can return a value by using the `return` keyword.
The `return` keyword can also be used to terminate execution of the function, in this case the value returned is `undefined`.

{% highlight javascript %}
    
    function myFunction(test) {
        if (test) {
            return;
        } else {
            return 0;
        }
    }

    myFunction(false); //Returns 0
    myFunction(true); //Returns undefined
{% endhighlight %}

## Function Arguments

Primitive parameters (such as a `number`) are passed to functions by value; the value is passed to the function, but if the function changes the value of the parameter, this change is not reflected globally or in the calling function.

If you pass an object (i.e. a non-primitive value, such as Array or a user-defined object) as a parameter and the function changes the object's properties, that change is visible outside the function.

{% highlight javascript %}
    
    function isEven(num) {
        return num % 2 === 0;
    }

    // Invoke the function
    isEven(44); //Returns true
{% endhighlight %}

### The `arguments` object and variable numbers of arguments

Every `function` in Javascript has an `arguments` object. The `arguments` object is an array like object which can be used to access the functions arguments.

By using the `arguments` object you can make functions that can accept a variable amount of arguments.

{% highlight javascript %}
    
    function total() {
        var total = 0;

        for (var i = 0; i < arguments.length; i += 1) {

            var number = arguments[i];

            if (typeof number === 'number') {
                total += number;
            }
        }
        return total;
    }

    // Invoke the function
    total(); // Returns 0
    total(1,2,3,4); // Returns 10
{% endhighlight %}

## Default Arguments

In ECMAScript 2015 (ES6) you can define default values for the function parameters when they are declared.

Be careful using defaults as the first argument provided to the function will always be considered to be the first, regardless of type and any defaults.

{% highlight javascript %}
    
    // Default values in ES6
    function speakNew(what = "Default speech", times) {

        for (var i = 0; i < times, i += 1) {
            console.log(what + " (" + i + ")" );
        }
    }

    // Default values in < ES6
    function speakOld(what, times) {
        // Check if the argument is defined.
        var what = (typeof what !== "undefined") ? what : "Default speech";

        for (var i = 0; i < times, i += 1) {
            console.log(what + " (" + i + ")" );
        }
    }

{% endhighlight %}

## Member Functions (a.k.a methods)

You can defined a function so that it is a top level member function, also called a method. In this way the function can be invoked by calling the property name assigned to that function.

{% highlight javascript %}
    
    var obj = {
        // The function is assigned to the property 'sayHello'
        sayHello: function() {
            console.log("Hello");
        }
    };

    // Invoking the function
    obj.sayHello();

{% endhighlight %}

## Callback Functions

A callback is jargon for a function that is passed into another function as an argument, and executed in that function.

## Arrow Functions

In ECMAScript 2015 (ES6) there is a function called an **arrow function**. This is a very concise way of writing a function that reduces the amount of boiler-plate code that needs to be written.

{% highlight javascript %}
    
    // Typical Function Definition
    function doubleIt(number) {
        return (number *= 2);
    }

    // In arrow function format
    doubleIt = number => (number *= 2);

{% endhighlight %}

Arrow functions follow the form:

`function name` = `arguments` => `return statement`;

# `Promises`

`Promises` are objects that captures the result of an asynchronous action with a particular programming interface (API) for handling the data when it finally came through, or failed.

`Promises` have a method called `then()` which accepts a callback/function which will be invoked when the asynchronous action has completed.

# `async` and `await`

`async` and `await` are a part of the ECMAScript 2017 specification.

These keywords make working with `promises` much easier, and mean that you can reduce the code clutter.

{% highlight javascript %}
    
    // One request
    aysnc function getOneThing() {

        // await makes the return of a promise
        var response = await axios.get("https:httpbin.org/get");

        // Now I have the response
    }

{% endhighlight %}

Many browsers (and Node.js) support `async` and `await`.

# Prototypes and Classes

Object-oriented Javascript uses Prototypical Inheritance, where child objects have a link ( `__proto__` ) to their parent object.

You can use prototypes to assign functions and properties to a prototype so that it is available to all objects that use that prototype.

{% highlight javascript %}
    
    Cake.prototype.bake = function(temp, minutes) {
        // function details...
    }

{% endhighlight %}

The `class` keyword was introduced to help developers migrating from other languages. It is *Syntactic Sugar* and doesn't really change the way that the programming language works.

Ultimately classes get turned into prototypes eventually.

# `import`

Dynamic Imports is the specification which describes how to load related scripts or library files in JavaScript.

The static `import` statement is used to import bindings which are exported by another module. [More Information](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)

{% highlight javascript %}
    
    // One of the many versions of the import statement. 
    import { export1 as alias1 } from "module-name";

{% endhighlight %}

Browser support for Dynamic Imports is still a bit flakey so tools (such as [webpack](https://webpack.js.org) and [rollup.js](https://rollupjs.org)) have been written to help with this.
