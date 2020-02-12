---
layout: default
title: Javascript Syntax
---

This page contains handy information on the basics of the Javascript Syntax.


# Variables
Can be named anything that starts with a letter, underscore ( `_` ), or dollar sign ( `$` ).

`var monster1 = "Grover", monster2 = "Cookie Monster";`

**camelCase** is a common naming convention used for Javascript variables.

You cannot use reserved words (such as `var`) for variable names.

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

# Numbers

Unlike other programming languages, all numbers in Javascript are of the same type called **number**.

Javascript can also represent the number infinity, by using the keyword `Infinity`.

Javascript also as the concept of 'Not a Number', which is represented by the keyword `NaN`.

## The Math Object

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

## Strict Equality

Strict 

The **Strict Equality** operator ( `===` ) test whether the thing on the left is identical to the thing on the right. 

There is also a **Strict Inequality** operator ( `!==` ) which test whether the thing on the left is *not* identical to the thing on the right.

{% highlight javascript %}
    
    var one = 1, two = 2;

    one === one; // Returns true

    one !== one; // Returns false

    one !== two; // Returns true

{% endhighlight %}