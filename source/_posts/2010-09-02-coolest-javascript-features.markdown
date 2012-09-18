---
layout: post
title: "Coolest Javascript Features - Part 1: let, logical operators, destructuring, labels"
date: 2010-09-02 13:48
comments: true
categories: [programming, ECMAScript, JavaScript, Mozilla, hint]
---

This is going to be an unsorted list about the nicest Javascript features. Look closely at the title: Javascript! Thus this list may contain features only present in the Mozilla-implementation and not standardized in ECMAScript! I am going to update this list every now and then.

## Block scoping with `let`

*Support: [ECMAScript Harmony](http://wiki.ecmascript.org/doku.php?id=proposals:block_expressions&s=let), [Javascript 1.7](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Statements/let), Mozilla-only (as of September 2010)*

Finally [lexical scoping / block scoping](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Statements/let) in Javascript. Declare variables with `let` instead of `var`. May come in handy when you are creating closures in a loop.

## Logical operators `&&` and `||`

*Support: should work in all modern browsers*

[Logical operators](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/Logical_Operators) are nothing spectecular in programming languages. But Javascript handles them a little different. `&&` and `||` don't necessarily return boolean values, but can also return other types (objects, etc). Here are some examples from [Angus Croll's JavaScript blog](http://javascriptweblog.wordpress.com/2010/07/26/no-more-ifs-alternatives-to-statement-branching-in-javascript/):

{% codeblock lang:js %}
//invoke callback if there is one
callback && callback();

//delay by argument or 20
delayBy(delay || 20);
{% endcodeblock %}

But you should be careful, when using this with values that evaluate to false. See the following example:

{% codeblock lang:js %}
//delay by argument or 20
var delay = 0;
delayBy(delay || 20);
{% endcodeblock %}

`0` will evaluate to `false` and thus `delayBy` will be called with `20`.

`||` is often used for setting default-values for functions parameters (well, harmony may contain that [natively](http://wiki.ecmascript.org/doku.php?id=harmony:parameter_default_values)), but as shown that may not always lead to the expected behaviour, thus I wouldn't recommend setting default values that way.

## Destructuring

*Support: [ECMAScript Harmony](http://wiki.ecmascript.org/doku.php?id=harmony:destructuring), [Javascript 1.7](https://developer.mozilla.org/en-US/docs/JavaScript/New_in_JavaScript/1.7#Destructuring_assignment_%28Merge_into_own_page.2Fsection%29), Mozilla-only (as of September 2010)*

Don't confuse this with destructors known from languages like Java or C++. [Destructuring](https://developer.mozilla.org/en-US/docs/JavaScript/New_in_JavaScript/1.7#Destructuring_assignment_%28Merge_into_own_page.2Fsection%29) assignment makes it possible to extract data from arrays or objects using a syntax that mirrors the construction of array and object literals

This allows things like swapping variables without a temporary variable, multiple return values and [more](https://developer.mozilla.org/en-US/docs/JavaScript/New_in_JavaScript/1.7#Looping_across_objects). Here is an example for multiple return values from MDN:

{% codeblock lang:js %}
function f() {
    return [1, 2];
}

var a, b;
[a, b] = f();
document.write ("A is " + a + " B is " + b + "\n");
{% endcodeblock %}

The function simply returns an array, but the destructuring syntax allows to split this into multiple variables.

## Labeling statements

*Support: should work in all modern browsers*

By [labeling](https://developer.mozilla.org/en/Core_JavaScript_1.5_Guide/Statements#label_Statement) statements you can give them an identifier that you can refer to from somewhere else. That may come in handy when using loops with `continue` or `break`.

{% codeblock lang:js %}
loopX:
for(var x = 0; x < 5; ++x)
{
    for(var y = 0; y < 5; ++y)
    {
        // ... do something
        if(...)
            break loopX;
    }
}
{% endcodeblock %}

