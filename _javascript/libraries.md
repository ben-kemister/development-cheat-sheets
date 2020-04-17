---
title: JavaScript libraries
---

This page contains some information about some handy or popular JavaScript libraries or frameworks.

# Lodash

[Lodash](https://lodash.com) is a popular library with methods which can help with type checking.

## Importing Lodash

The recommended way to import lodash is on a per function basis:

{% highlight javascript %}
    
    import isArray from 'lodash/isArray';
{% endhighlight %}

Because lodash holds all it's functions in a single file, so rather than import the whole 'lodash' library at 100k, it's better to just import lodash's has function which is maybe 2k.

For more information see [this stackoverflow post](https://stackoverflow.com/questions/35250500/correct-way-to-import-lodash).

# Jest

[Jest](https://jestjs.io/en/) is a common JavaScript testing framework.

## Testing Errors

### Test Error is thrown

{% highlight javascript %}
    
    // Function being tested
    const throwError = (): void {
        throw new Error('Something unexpected happened');
    }

    test('Test that an Error should be thrown', () => {
        // Will fail the test if an Error is not thrown
        expect(throwError).toThrowError();
    });
{% endhighlight %}