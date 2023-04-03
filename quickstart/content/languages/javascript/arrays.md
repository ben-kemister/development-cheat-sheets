---
title: Arrays
tags:
- javascript
---

This page contains information, and simple code examples, about the use of Arrays in JavaScript.

# Introducing Arrays

Arrays allow you to store an ordered list of data of any type.
The order of the elements are preserved and the keys are automatically assigned.
An array can also store mixed data types (e.g. booleans, Strings, and objects can all exist in the same array).

# Defining an Array

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

# Accessing Array Items

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

# *push()* - Adding items to an Array

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

# *pop()* - Removing an item from the end of an Array

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

# *splice()* - Removing items from the middle of an Array

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

# *map()* method

The `map()` method accepts a callback function which operates on each item in the array and returns a new array.

{% highlight javascript %}
    
    function doubleIt(number) {
        return (number *= 2);
    }

    var myNumbers = [1, 2, 3, 4, 5];

    var myDoubles = myNumbers.map(doubleIt);

    myDoubles; // Returns [2, 4, 6, 8, 10]

{% endhighlight %}

# *forEach()* method

Operates on each element in an array, but does not return anything.

Note that the `break` keyword will not work inside the `foreach()` method.

# *find()* method

The `find()` method returns the value of the first element in the provided array that satisfies the provided testing function. If no matching element is found then `undefined` is returned.

{% highlight javascript %}
    
    const find = (code: string): AirlineCode => {

        const upperCode = code.toUpperCase();

        let found = airlineCodes.find( element => element.code === upperCode);

        // If no matching element is found then undefined is returned
        if (found) {
            return found;
        }
        throw new Error(No matching code found.');
    }
{% endhighlight %}

