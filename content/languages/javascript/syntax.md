---
title: Basic Syntax
tags:
- javascript
---

This page contains handy information on the basics of the Javascript Syntax.

# Basic JavaScript types/primitives

The basic JavaScript Types/primitives are:
* String
* Boolean
* Number
* BigInt
* Null
* Undefined
* Symbol (starting in ES6)
* Object

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

```javascript
// Global Variable
myPet = "cat";
```

If you have enabled **strict mode** (by adding `"use strict";` at the top of your file) you will get a warning if you using any global variables.

### Local Variables

To define a local variable use the keyword `var`.

```javascript
// Local variable
var myState = "ACT";
```

Functions are the main delimiter of variable scope when using the `var` keyword.

In ECMAScript 2015 or above you can also use `let` and/or `const`, these have *block* scope.

# Strings
String literals are Strings which are contained within quotation marks, for example:

`var myString = "A String Literal";`

## Escaping and Multi-line Strings

The backslash ( `\` ) is the Javascript escape character.

`"This is Joes' \"favorite\" string";`

The backslash ( `\` ) can also be used for strings that run over multiple lines.

```javascript
This is \
Joe'/s favorite \
string EVER
```

## String properties

String properties are accessed using the dot ( `.` ) notation to access the desired property.

```javascript
var myString = "This is my string.";
myString.length //returns 18
```

## String functions

String functions are accessed similar to **Java** methods using the format `string.funciton()`, for example:

```javascript
var myString = "This is my string.";
myString.toUpperCase() //returns "THIS IS MY STRING."
```

## String concatenation

To concatenate Strings use the plus ( `+` ) symbol.

```javascript
"Cat " + "Dog"; //returns "Cat Dog"
```

## Template Strings

Template strings allow you to use the contents of variables inside a String;

Note the use of **backticks** ( \` ) NOT the use of single quotes ( `'` ) around the statement.

```javascript
var myName = 'Bob';

// myGreeting is "Hello, my name is Bob!"
var myGreeting = `Hello, my name is ${myName}!`;


function hello(name){
    console.log(`Hello, my name is ${name}!`);
} 

// Prints 'Hello, my name is Fred!'
hello('Fred')
```

# Numbers

Unlike other programming languages, all numbers in Javascript are of the same type called **number**.

Javascript can also represent the number infinity, by using the keyword `Infinity`.

Javascript also as the concept of 'Not a Number', which is represented by the keyword `NaN`.

## The `Number` Object

There is a built in `Number` object in Javascript which has some handy methods.

The `Number.isNaN(number)` method is handy for checking if the number is a valid number.

```javascript
Number.isNaN(NaN); // returns true
```

## The `Math` Object

There is a global object called `Math` which contains a heap of properties and methods for dealing with number types.

```javascript
Math.round(12.522141415254) // returns 13
```

# Booleans

Booleans are either `true` or `false` values, which must be spelt correctly and be all lowercase.

# Objects

Javascript uses objects to represent and structure data beyond simple types such as booleans and Strings.

You can define object literals by using the curly braces ( `{}` ), for example:

```javascript
var myEmptyObject = {}; //Empty object
var notEmptyObject = {
    'label': 'value',
    'label2': 25,
    'label3': true,
};
```

You can also define the labels in an object without using quotation marks like this:

```javascript
var bird = {
    commonName: 'raven',
    callType: "squawky",
    quote: "Nevermore",
    noisy: true,
    deadly: false,
};
```

## Object Properties

To retrieve a property from an object you can use either dot notation or square braces. Square braces is useful if you have spaces or special characters in the property name.

```javascript
// Dot notation
bird.quote  // returns "Nevermore"

// Square braces
bird["quote"]   //returns "Nevermore"  
```

### Adding or Deleting properties

You can add new properties to an object by just using assignment.

```javascript
// Dot notation
bird.whereItLives = "in a tree" 
```

You remove properties from an object by using the `delete` keyword. Deleting works with either Dot or Square braces notation.

```javascript
//Removes the whereItLives property from the object
delete bird.whereItLives;
```

## Object References

Be aware that variables are really references to the object (similar to the Java programming language).

This means that if you assign one variable to equal another, they both refer to the same object.

Objects (and Arrays) are **passed by reference** to functions. Take this into consideration if you have a case where you do not want to modify the original object. In this case you may want to create and return a new object.

## Copying objects

A common quick way to make an exact copy of an object is by using the JSON object as follows:

```javascript
//Makes a copy of the animal object safely.
animal2 = JSON.parse(JSON.stringify(animal));
```

# Comments

Javascript supports two styles of comments, line comments and block comments.

Be careful when using block comments if you have valid `*/` characters in your code.

```javascript
// This is a line comment

/*
    This is a block comment,
    which can be multiple lines
*/
```

# Regular Expressions

To define a regular expression you use forward slashes ( `/` ) around the regular expression you want to use. Any flags appear after the closing forward slash.

```javascript
var string1 = 'Wow, this is the longest string Ever.';

// This is a regular expression
var regex = /ever/;

console.log( regex.test(string1) ); //returns false

// a case insensitive regular expression
regex = /ever/i;

console.log( regex.test(string1) ); //returns true
```

# Simple Comparisons

## Strict Equality ( `===` )

Strict equality compares two values for equality. If the values have the same type, are not numbers, and have the same value, they're considered equal. Finally, if both values are numbers, they're considered equal if they're both not NaN and are the same value, or if one is +0 and one is -0.

For more information see the [Mozilla Developer Equality page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness).

The **Strict Equality** operator ( `===` ) test whether the thing on the left is identical to the thing on the right. 

There is also a **Strict Inequality** operator ( `!==` ) which test whether the thing on the left is *not* identical to the thing on the right.

```javascript
var one = 1, two = 2;

one === one; // Returns true

one !== one; // Returns false

one !== two; // Returns true
```

## Loose Equality ( `==` )

Loose equality compares two values for equality, **after converting** both values to a common type. After conversions (one or both sides may undergo conversions), the final equality comparison is performed exactly as `===` performs it.

For more information see the [Mozilla Developer Equality page](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness).

```javascript
var one = 1, two = 2;

one == one; // Returns true

one == "1"; // Returns true

one != "1"; // Returns false
```

# Arithmetic Operators

In general Javascript arithmetic operators use the same syntax as **Java**.

## Modulus Operator ( `%` )

The modulus operator ( `%` ) return the remainder after the division.

```javascript
20 % 2; // Returns 0

21 % 2; // Returns 1

//Using modules to test if a number is even or odd
 10 % 2 === 0; // Returns true
```

## Incrementing and Decrementing

```javascript
var counter = 1;

counter += 1; // Returns 2

counter++; //Returns 2, but counter is now 3

counter *= 2; // Returns 6  
```

# Logical Operators

* Logical AND ( `&&` )
* Logical OR ( `||` )
* Logical NOT ( `!` )

# Conditionals

## If Statement

Javascript *if* statements use the same syntax as **Java** if and if-else statements.

```javascript
var answer = window.confirm("Click OK, get true. Click CANCEL, get false.");

if(answer === true){
    console.log("You said true!");
} else {
    console.log("You said something else");
}
```

# Switch Statement

Javascript *switch* statements use the same syntax as **Java** switch statements.

Remember that if you do not have a `break` the execution will 'fall through' each case until a `break` statement is encountered.

```javascript
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
```

# Terse ifs

## Single line ifs

You can leave off the curly braces of an *if* statement and it will execute the next line. Note it only executes a single line.

```javascript
var cherub = "Cupid";

if( cherub === "Cupid" ) console.log("Ouch, an arrow!");
else console.log("I feel nothing");
```

## if (variable) 'Truthy'

In JavaScript, a *truthy* value is a value that is considered true when encountered in a Boolean context. All values are truthy unless they are defined as falsy (i.e., except for false, 0, 0n, "", null, undefined, and NaN).

For more information see the [Truthy page on the Mozilla Developer network](https://developer.mozilla.org/en-US/docs/Glossary/Truthy).

```javascript
if("variable"){
    console.log("The value is truthy!")
}
```

## Ternary Operator

The Javascript ternary operator follows the same syntax as **Java**, it allows for a short form of an if statement.

```javascript
var animal = "cat";

animal === "cat"
    ? console.log("You'll need a cat herder!") // Executed if true
    : console.log("Call the dog catcher."); // Executed if false
```

# Type Checking

Because Javascript is a **loosely typed** language you may need to check the type of an object before you perform operations on it.

Type checking is done using the `typeof` keyword. The `typeof` function returns a String which is the name of the data type all in lowercase.

```javascript
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
```

## *hasOwnProperty()* method

Every object in Javascript has the method `hasOwnProperty("propertyName")`, this method allows you to check that an object has a particular property prior to accessing it.

```javascript
var thing = [];

typeof thing; // Returns "object"

typeof thing === "object" && thing.hasOwnProperty("length") // Returns true
```

## *instanceof* Operator

The `instanceof` operator checks that the instance of the class has a constructor which matches the given class.

```javascript
class MyClass {
    someProperty: number;
}

let obj = new MyClass(1);

console.log( obj instanceof MyClass); // Returns true.
```

### Use on Custom Errors

Note if you are using this on custom Error objects (i.e. Objects that extends `Error`) you will need to be using ES6 or greater, otherwise it will not work as expected.

```javascript
test('Test \'instanceof\' on Custom Errors', () => {

    class MyError extends Error {
        someProperty: number;
    }

    let obj = new MyError('an error');

    let result = obj instanceof Error;
    expect(result).toBe(true);

    /**
     * If you are using < ES6 then the use of 'instanceof'
     * on a Custom Error will not work as expected.
     */
    result = obj instanceof MyError;
    expect(result).toBe(true); // This will fail on < ES6
});
```

# Loops

Remember you can `break` (short circuit) a `for` loop, just the same as you can in **Java**.

## (Sequential) For Loops

The Javascript `for` loop syntax is the same as **Java**.

```javascript
for (var i = 0; i < 10; i++) {
    console.log(i); // Prints 0...9
}
```

## Enumerative (for each) Loops

Allows you to iterate over an object or array.
Note that the order of the iteration is **not** guaranteed, if you need a guaranteed order use the Sequential For Loop.

```javascript
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
```

## While Loops

While loops are handy if you don't know how many times you will be iterating over an array or object.

Be careful that if the terminating condition always equates to `true` then the while loop will continue indefinately.

```javascript
var i = 0;

while (i < 10 ) { // This is the terminating condition
    console.log(i + "... This will go until we hit 10");
    i += 1;
}
```

## Do While loop

The Do While loop is handy if you want the loop to execute at least once.

```javascript
var myArray = [true, true, true, false, true, true];

var myItem = false;

do {
    console.log("myArray has" +
                  myArray.length +
                    " items now. This loop will go until we pop a false."  
    );
    myItem = myArray.pop();
}while (myItem !== false);
```

# Functions

Functions are one of the fundamental building blocks in JavaScript. A function is a JavaScript procedure—a set of statements that performs a task or calculates a value. 

A function definition (also called a function declaration, or function statement) consists of the `function` keyword, followed by:

* The name of the function.
* A list of parameters to the function, enclosed in parentheses and separated by commas.
* The JavaScript statements that define the function, enclosed in curly brackets, {...}.

For more information see the [Functions Page on Mozilla Developer network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)

```javascript
// Function Definition
function speak() {
    console.log('Woof');
    console.log('Meow');
    console.log('Quack');
    console.log('Mooooo');
}

// Invoke the function
speak();
```

Functions are ***first class citizens*** in Javascript, they are objects that have the power to be invoked. 

All `functions` in Javascript inherit from the global `Function` object, which means that they are objects.

This also means that functions can be assigned to (and passed around using) variables.  An example of this can be seen when dealing with anonymous functions.

```javascript
// Anonymous function assigned to a variable
var say = function(what) {
    what = what || "Speaking!";

    for (var i = 0; i < 10; 1 += 1) {
        console.log(what);
    }
}

// Invoke the function
speak();
```

## Return statement

A function can return a value by using the `return` keyword.
The `return` keyword can also be used to terminate execution of the function, in this case the value returned is `undefined`.

```javascript
function myFunction(test) {
    if (test) {
        return;
    } else {
        return 0;
    }
}

myFunction(false); //Returns 0
myFunction(true); //Returns undefined
```

## Function Arguments

Primitive parameters (such as a `number`) are passed to functions by value; the value is passed to the function, but if the function changes the value of the parameter, this change is not reflected globally or in the calling function.

If you pass an object (i.e. a non-primitive value, such as Array or a user-defined object) as a parameter and the function changes the object's properties, that change is visible outside the function.

```javascript
function isEven(num) {
    return num % 2 === 0;
}

// Invoke the function
isEven(44); //Returns true
```

### The `arguments` object and variable numbers of arguments

Every `function` in Javascript has an `arguments` object. The `arguments` object is an array like object which can be used to access the functions arguments.

By using the `arguments` object you can make functions that can accept a variable amount of arguments.

```javascript
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
```

## Default Arguments

In ECMAScript 2015 (ES6) you can define default values for the function parameters when they are declared.

Be careful using defaults as the first argument provided to the function will always be considered to be the first, regardless of type and any defaults.

```javascript
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
```

## Member Functions (a.k.a methods)

You can defined a function so that it is a top level member function, also called a method. In this way the function can be invoked by calling the property name assigned to that function.

```javascript
var obj = {
    // The function is assigned to the property 'sayHello'
    sayHello: function() {
        console.log("Hello");
    }
};

// Invoking the function
obj.sayHello();
```

## Callback Functions

A callback is jargon for a function that is passed into another function as an argument, and executed in that function.

## Arrow Functions

In ECMAScript 2015 (ES6) there is a function called an **arrow function**. This is a very concise way of writing a function that reduces the amount of boiler-plate code that needs to be written.

```javascript
// Typical Function Definition
function doubleIt(number) {
    return (number *= 2);
}

// In arrow function format
doubleIt = number => (number *= 2);
```

Arrow functions follow the form:

`function name` = `arguments` => `return statement`;

## getter function (`get` keyword)

The `get` keyword binds an object property to a function that will be called when that property is looked up.

```javascript
const obj = {
    log: ['a', 'b', 'c'],
    get latest() {
        if (this.log.length == 0) {
        return undefined;
        }
        return this.log[this.log.length - 1];
    }
}
console.log(obj.latest);
// expected output: "c"
```


## setter function (`set` keyword)

The `set` keyword binds an object property to a function to be called when there is an attempt to **set** that property.

```javascript
const language = {
    set current(name) {
        this.log.push(name);
    },
    log: []
}

language.current = 'EN';
language.current = 'FA';

console.log(language.log);
// expected output: Array ["EN", "FA"]
```

# *Promise*s

`Promises` are objects that captures the result of an asynchronous action with a particular programming interface (API) for handling the data when it finally came through, or failed.

## Defining a *Promise*

`Promises` need to define a `resolve` argument, we call `resolve(...)` when what we were doing asynchronously was successful, and `reject(...)` when it failed.

```javascript
let myFirstPromise = new Promise((resolve, reject) => {
    // We call resolve(...) when what we were doing asynchronously was successful, and reject(...) when it failed.
    // In this example, we use setTimeout(...) to simulate async code. 
    // In reality, you will probably be using something like XHR or an HTML5 API.
    setTimeout( function() {
        resolve("Success!")  // Yay! Everything went well!
    }, 250)
});
```

## Using *Promise*s

`Promises` have a method named `then()` which accepts a callback/function which will be invoked after the asynchronous action has completed.

The `then()` method can be used to chain functions together that will be invoked in order,

For more information see the [Promise page on the Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

```javascript
// Function which returns a promise.
const delay = seconds => {
    return new Promise(resolve => {
        // The 'resolve' function is called after seconds * 1000 seconds.
        setTimeout(resolve, seconds * 1000);
    });
}

console.log('Zero Seconds');
// The anonymous function console.log() is called after 1 second.
delay(1).then(() => console.log('One Second'));
// The anonymous function console.log() is called after 3 second.
delay(3).then(() => console.log('Three Second'));
```

# *async* and *await* keywords

`async` and `await` are a part of the ECMAScript 2017 specification.

These keywords make working with `promises` much easier, and mean that you can reduce the code clutter.

```javascript
// One request
aysnc function getOneThing() {

    // await makes the return of a promise
    var response = await axios.get("https://httpbin.org/get");

    // Now I have the response
}
```

Many browsers (and Node.js) support `async` and `await`.

# Prototypes and Classes

Object-oriented Javascript uses Prototypical Inheritance, where child objects have a link ( `__proto__` ) to their parent object.

You can use prototypes to assign functions and properties to a prototype so that it is available to all objects that use that prototype.

```javascript
Cake.prototype.bake = function(temp, minutes) {
    // function details...
}
```

The `class` keyword was introduced to help developers migrating from other languages. It is *Syntactic Sugar* and doesn't really change the way that the programming language works.

Ultimately classes get turned into prototypes eventually.

For more information on classes see [Classes in JavaScript]().

# `import`

Dynamic Imports is the specification which describes how to load related scripts or library files in JavaScript.

The static `import` statement is used to import bindings which are exported by another module. [More Information](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)

```javascript
// One of the many versions of the import statement. 
import { export1 as alias1 } from "module-name";
```

Browser support for Dynamic Imports is still a bit flakey so tools (such as [webpack](https://webpack.js.org) and [rollup.js](https://rollupjs.org)) have been written to help with this.
