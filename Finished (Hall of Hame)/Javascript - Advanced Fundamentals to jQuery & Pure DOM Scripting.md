## Basic JS

###### Lifecycle of a simple HTML page with a Javascript alert "hello world"

- GET request (http://helloworld.com/index.html) to the server
- Server responds with an HTML string `"<html>\n<head><script ..."`
- The browser passes off the HTML string to something like Webkit, which will parse the HTML and build DOM elements one-by-one
- When the parser hits a script tag, it will defer to a Javascript interpreter (ex/ V8)

Interestingly enough, if you put the script tag before the body, the alert will run before the page is rendered. This is exactly the reason why you should put script tags at the bottom of the page.

An alert is blocking code in the sense that no other code can be run until the user clicks ok

## Javascript Features

`Javascript == ECMAScript == JScript`

When people talk about ECMAScript, it's usually a standard for all the browsers to implement.

Javascript is NOT the DOM, although they are kind of made for each other at this point.

#### Differences Between JS and Other Programming Languages

###### Javascript is dynamic

Most other languages such as C are compiled languages. What happens is: You write your C code, and you have to compile it. What a compiler will do is read that code and convert it into bytecode, and when you execute that, that bytecode is loaded into memory and the computer just goes through each instruction, one line at a time. There's a block of memory for the code, and a block of memory for everything else the application might use.

###### Javascript is Weakly Typed

Type associated with value, not variable:
```
var a = 1;
a = "one";
a = [1];
a = {one: 1};
```

###### Javascript has 1st Class Functions

Anything you can do with an object, you can do with a function

- Create a function
- Return a function
- Pass a function as an argument

###### Javascript is Prototype Based

##### Summary

The DOM is a JS representation of HTML and the browser. It's an interface provided to JS.

## Data Types and Operators

Undefined is a pointer that hasn't been set
Ex/ `var foo;`

Null is actually referring to the null pointer. If you come across `null` in a piece of code, it is by intent.

NaN is a number

## The delete Keyword

```
var me = {
	name: {
    	first: "justin"
    }
};

var name = me.name;
delete me.name;
name.first // "justin"
```

The variable `me` is on the `window` scope, and points to the object `name`, which points to the object `first`, which points to the primitive string `"justin"`.

When you call `delete me.name`, you're actually just removing the pointer `me.name`. This leaves the pointer `name.first` intact.

###### Deleting `me`

When you delete `me`, it doesn't do anything. `delete` actually returns false. You can't actually delete a variable.

`delete` actually returns true or false only depending on if your syntax is correct, not if the deletion actually occurred.

If you really want to remove `me`, set `me` to `undefined`.

## typeof

| Type | Result |
|--------|--------|
| Undefined | "undefined" |
| Null | "object" |
| Boolean | "boolean" |
| Number | "number" |
| NaN | "number" |
| String | "string" |
| Function | "function" |
| Array | "object" |
| Any other object | "object" |

Weirdness: 
- `typeof null` is `"object"`
- `typeof NaN` is `"number"`

To us, an array looks like `['a', 'b', 'c']`. In memory, it actually looks more like this:

```
{
	0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
	__proto__: {
    	slice: function(),
        __proto__: {
        	toString: function()
        }
    }
}
```

Everything in JavaScript is a pointer.
`var something` pointing to something else in memory.

## `==` vs `===`

```
21 == "21" // true
undefined == null // true
undefined === null // false
21 === 21 // true
{} === {} // false
NaN === NaN // false
true == {valueOf: function(){return "1"}} // true
```

## Comparison Operators Explained

```
var str1 = "hi";
var str2 = "hi";
str1 === str2
```

| Address | Value |
|--------|--------|
| .... | .... |
| x1001 | call-object |
| x1002 | str1 |
| x1003 | x2001 |
| x1004 | str2 |
| x1005 | x2101 |
| .... | .... |
| **x2001** | **STRING** |
| **x2002** | **hi** |
| .... | .... |
| **x2101** | **STRING** |
| **x2102** | **hi** |

`true`

In this case, the `===` actually finds the two primitives in memory and compares their values (comparison by value).

```
var obj1 = {};
var obj2 = {};
obj1 === obj2
```

| Address | Value |
|--------|--------|
| .... | .... |
| x1001 | call-object |
| x1002 | obj1 |
| **x1003** | **x2001** |
| x1004 | obj2 |
| **x1005** | **x2101** |
| .... | .... |
| x2001 | OBJECT |
| x2002 | 0 |
| .... | .... |
| x2101 | OBJECT |
| x2102 | 0 |

`false`

In this case, the 2 objects are compared by reference.

`===` is actually a lot more performant than `==` sinsce `==` recursively runs through a decision tree of batshit crazy rules.

The only time that `==` comparisons are commonly used is to compare `null` to `undefined`.

`foo === null || foo === undefined`
vs
`foo == null || undefined`

There's even an exception in JSLint for doing that.

# Closures

## Types as Arguments

Whenever a function is created in memory, it has a `prototype` property that points to an object in memory.

How closures work: functions know about their parent call object and call objects know about the function that was ran, and it creates this type of linking that JavaScript can go through to find variables.

Every time a function is run, a brand new call object is created.

If you understand closures, you understand JavaScript better than 90% of developers out there.

## Closure Gotchas

```
var a = {};
for(var i = 0; i < 3; ++i) {
	a[i] = function() {
    	alert(i)
    };
}

a[0](); // 3
a[1](); // 3
a[2](); // 3
```

The main gotcha here is that `var i` is on the window scope.

What's actually happening is you have:

```
a = {
	0: function(alert(i)),
    1: function(alert(i)),
    2: function(alert(i))
}
```

And `i` is set to 3 when the `for` loop condition fails.

A solution is to create an additional closure using an immediately invoked function.

```
var a = {};
for(var i = 0; i < 3; ++i) {
	(function(j) {
    	a[j] = function() {
        	alert(j);
        };
    })(i);
}

a[0](); // 3
a[1](); // 3
a[2](); // 3
```

(Self-discovery): If you made a for loop in the window scope, any variable within the for loop is still in the window scope.

`
for(var i = 0; i < 10; ++i) {
	var j = i;
}
`

The variable `j` is still in the window scope!

## Exercise: Making a Tag Library

Goal: Build a tag library that creates elements of the following types in the least lines of code: `a, div, span, form, h1, h2, h3, h4`

Example usage:

```
var h1 = make.h1();
h1.innerHTML = "Hello World";
document.body.appendChild(h1);

var a = make.a();
a.href = "http://canjs.com";
a.innerHTML = "CanJS";
document.body.appendChild(a);
```

Solution:

```
make = {};

var tags = ['a', 'div', 'span', 'form', 'h1', 'h2', 'h3', 'h4'];

for (var i = 0; i < tags.length; ++i) {
	(function(tag) {
		make[tag] = function() {
			return document.createElement(tag);
		};
	})(tags[i]);
}
```

Notice that properties are added to the `make` object outside of the make object's initial declaration, and using the array notation (ex/ `make["h4"]` since `make."h4"` is not valid dot notation).

Also notice the use of the closure function to get around the global `i` variable used by `tags[i]`.

There is a much cleaner solution however. Instead of writing:

```
for (var i = 0; i < tags.length; ++i) {
	(function(tag) {
		make[tag] = function() {
			return document.createElement(tag);
		};
	})(tags[i]);
}
```

You can use `forEach`:

```
tags.forEach(function(tag) {
	make[tag] = function() {
    	return document.createElement(tag);
    };
});
```

The parameter `tag` in the anonymous function is not in the window scope, which is why this works.

# Context

## What is `this`?

| Rule | Example | Context |
|--------|--------|--------|
| [[Call]] operator | var bark = function() {}; <br> bark(); | window |
| Dot [[Call]] operator | var puppy = { ... }; <br>  puppy.bark(); | What's left of the dot (in this case, puppy) |
| 'new' keyword | var puppy = new Dog(); | puppy |
| 'call' or 'apply' | var puppy = {...}; <br> Dog.bark.call(puppy, 1); | ? |

`call` and `apply` do the same thing

In JavaScript, there is no method overloading. Whatever is last wins.

When you use `call` or `apply`, the first argument you pass in is your context.

In the case of call, the arguments are structured as context, argument1, argument2, ...
In the case of apply, the arguments are structured as context, [argument1, argument2, ...]

In JavaScript, functions don't know where they live, they only know how they are called. If you're in C# or Java and you have a method on a class, the function does know where it lives. `this` will always point to the instance of that class.

Put another way, it doesn't matter how a function is defined, it matters how a function is called (at least for context).

## The Dot Operator

`var foo = {}; // same as new Object() `
`foo.toString()`

`toString` lives on the base object's prototype.

You think of a Java class as the blueprint for all newly created objects. At a very abstract level, you can think of a prototype as that same blueprint for all newly created objects.

All newly created objects have a `__proto__` property that point to the base object by default.

When you call `foo.toString()`, the `toString` method isn't found on the newly created object. JS then checks foo's `__proto__` for the method and finds it, but if it didn't, it would then search the base object's `__proto__`, and so on. This is called proto chaining.

Even though `toString` lives in the base object, the context of `toString` is still `foo` because we used the dot notation (`foo.toString()`).

Functions have prototypes, objects have `__proto__`s.

## Exercise: Finding Properties

Write the dot operator as if it was implemented in JS

```
var Person = function(name) {
	this.name = name;
}
Person.prototype.isPerson = true;

var person = new Person('Smith');

DOT(person, 'name'); // person.name
DOT(person, 'isPerson'); // person.isPerson
```

Answer:

```
var DOT = function(obj, prop) {
	if(obj.hasOwnProperty(prop)) {
		return obj[prop];
	}
	else {
		return DOT(obj.__proto__, prop);
	}
};
```

The two key steps in here are the use of `hasOwnProperty` and the recursive call `DOT(obj.__proto__, prop);`

JavaScript is a language of conventions. The `__proto__` property on an object is really just like every other property. If I put a `foo` property on an object, it's just like putting a `__proto__` property on an object. The only difference is that the dot operator is magically programmed to look at the proto property.

Side note: Make sure when you debug, you get in the habit of using watch expressions.

## Exercise: Invoking Functions

Exercise: Write the dot [[call]] operator as if it was implemented in JS.

```
var Person = function(name) {
	this.name = name;
}
Person.prototype.speak = function(comment, punctuation) {
	console.log('Hello ' + this.name);
};

var person = new Person('Smith');

DOTCALL(person, 'speak', []); // person.speak()
```

Solution:

```
// DOT function is the same as before
var DOT = function(obj, prop) {
	if(obj.hasOwnProperty(prop)) {
		return obj[prop];
	}
	else {
		return DOT(obj.__proto__, prop);
	}
};

var DOTCALL = function(obj, prop, args) {
	var fn = DOT(obj, prop);

	if(fn) {
		return fn.apply(obj, args);
	}
};
```

First, use `var fn = DOT(obj, prop);` to search the proto chain for the desired function invoked by `obj.prop(args)`. If the function exists, return the invokation of the function, but use apply so that you can set the context to the original object `obj`.

# Prototypal Inheritance

## Exercise: Implementing the `new` Keyword

Exercise: Write the new operator as if it was implemented in JS.

```
var Person = function (name) {
	this.name = name;
};

Person.prototype.speak = function () {
	console.log('Hello!');
};

var person = NEW(Person, ['name']); // var person = new Person('name')
person.speak(); // does what you would expect
```

Solution:

```
var NEW = function(constructor, args) {
	// create a new object
	// set new object's __proto__ to constructor's prototype
	// invoke our constructor with our new object as context
	// return our new object
	var obj = {};
	obj.__proto__ = constructor.prototype;
	constructor.apply(obj, args);
	return obj;
}
```

## Object.create()

`Object.create()` makes inheritance simple.

```
Animal = {
	init: function(name) {
    	this.name = name
    },
    eats: function() {
    	return this.name + " is eating."
    }
}
Chordate = Object.create(Animal, {
	has_spine: { value: true }
});
Mammal = Object.create(Chordate, {
	has_hair: { value: true }
});
m = Object.create(Mammal);
m.init('dog'); // m is now an Object {name: "dog"}
```

Note that `init` doesn't have any special meaning.

Why shouldn't you use `Object.create()`? At least in its current implementation, it is very slow compared to just using the `new` keyword.

## Exercise: Creating the Instance Operator

Write the instanceof operator as if it was implemented in JS.

```
var Person = function(name) {
	this.name = name;
};

person = new Person('Alexis');
INSTANCEOF(person, Person); // should return true
```

Solution:

```
var INSTANCEOF = function(obj, constructor) {
	if(obj.__proto__ === constructor.prototype) {
		return true;
	}
	else if(obj.__proto__) {
		return INSTANCEOF(obj.__proto__, constructor);
	}
	else {
		return false;
	}
};
```

Cool observation:

`var obj = {__proto__: {__proto__: {name: "Bar"}}}`
`obj.name // "Bar"`

This is how the dot operator works under the hood.

# jQuery Functional Units

## $.extend()

Write your own implementation of the `$.extend()` function. `$.extend()` merges the contents of two or more objects together into the first object.

My Solution:

```
$.extend = function(target, object) {
    Object.keys(object).forEach(function(prop) {
        if(!target[prop]) {
        	target[prop] = object[prop];
        }
    });
    return target;
};
```

Instructor's Solution:

```
$.extend = function(target, object) {
    for(var prop in object) {
        if(object.hasOwnProperty(prop)) {
        	target[prop] = object[prop];
        }
    }
    return target;
};
```

## Day 1 Recap

###### Creating Functions

```
var fn = function(arg1, arg2) {
	var foo = "Bar";
};
```

When you define a function, a function object is created in memory. The function always knows abouts its parent call object, and the function has a prototype object that's created automatically. The prototype object has a constructor property that points back to the function object. Whenever you see a function defined as shown above, it doesn't really know what's going on inside the function (in this case, the line `var foo = "Bar";`).

###### Calling Functions

`fn(me, val)`

When a function is called or invoked, a new call object is created. The first thing is its arguments are translated (`this`, parameters, variables internal to the function, etc). Then the function is actually run.

###### New

```
var Dog = function(name) {
	this.name = name;
};
Dog.prototype.speak = function(msg) {
	alert(this.name, "says", msg);
};
var pup = new Dog("fido");
```

`Dog` is a variable that points to a function object, whose prototype property points to an object. This object's constructor property points back to the function object, and its speak property points to a separate function object.

`pup` is a variable that points to an object. This object's name property points to the primitive string `"fido"`, and its `__proto__` property points to the `Dog` function's prototype.

## Exercise: Implementing isArrayLike()

Determine whether the argument is LIKE an array.

```
isArrayLike(['a','b','c']) == true;
isArrayLike(arguments) == true;

var divs = document.getElementsByTagName('div');
isArrayLike(divs) == true;
```

Note: `document.getElementsByTagName('div');` returns a node list, which is not a true array.

Solution:

```
var isArrayLike = function(obj) {
	if(typeof obj.length === 'number') {
    	if(obj.length === 0) {
        	return true;
        }
        else if(obj.length > 0) {
        	return (obj.length - 1) in obj;
        }
    }
    return false;
};
```

`isArrayLike` implements a concept called duck typing.

"If it walks like a duck and swims like a duck and quacks like a duck, I call that bird a duck".

Duck typing is concerned with those properties that are used on the object rather than the type of the object itself.

An object that is like an Array has the following characteristics:
- `obj.length;`
- `obj[0]`
- `(obj.length - 1) in obj`

We are not actually checking if each enumerable item exists in the array since that would be too expensive (eg/ an array with a million entries).

## $.each

Implement a `$.each` function that iterates over arrays or objects.

```
var collection = ['a','b','c'];
$.each(collection, function(index, item) {
	console.log(item + ' is at index ' + index);
});

collection = {foo: 'bar', zed: 'ted'};
res = $.each(collection, function(prop, value) {
	console.log('prop: ' + prop + ', value: ' + value);
});
```

Solution:

```
$.each = function(collection, cb) {
  if(isArrayLike(collection)) {
    for(var i = 0; i < collection.length; ++i) {
      var value = collection[i];
      cb.call(value, i, value);
    }
  }
  else {
    for(var prop in collection) {
      var value = collection[prop];
      cb.call(value, prop, value);
    }
  }
  return collection;
}
```

The only tricky thing here is setting the context of the callback to `value` in each case (as opposed to directly calling `cb(i, value)` or `cb(prop,value)`).

## $.makeArray(obj)

Convert an array-like object into a true JavaScript array.

```
$.makeArray(document.body.childNodes);
$.makeArray(document.getElementsByTagName('*'));
$.makeArray(arguments);
$.makeArray($('li'));
```

Solution:

```
$.makeArray = function(arr) {
  if(isArrayLike(arr)) {
    var result = [];
    $.each(arr, function(key, value) {
      result.push(value);
    });
    return result;
  }
  else {
    return [arr];
  }
}
```

## $.proxy(fn,context)

Exercise: Create a function `$.proxy` that takes a function and returns a new one that calls the original with a particular context.

```
var dog = {
	name: 'fido',
    speak: function() {
    	console.log(this.name + ' says woof');
    }
};
var speakProxy = $.proxy(dog.speak, dog);
```

Solution:

```
$.proxy = function(fn, context) {
  return function() {
    return fn.apply(context, arguments);
  };
}
```

What's a bit confusing is that `arguments` implicitly takes any parameters passed into `return function() {...}`. In fact, `arguments` is sort of a magic behind-the-scenes object, just like `this`.

# Finding Elements

## Finding from the Document

Listed in order of performance:

```
document.getElementById(id)
document.getElementsByTagName(tag)
document.getElementsByClassName(className)
document.querySelector(selector)
document.querySelectorAll(selector)
```

## Creating the $ Function

Exercise: Create a function named `$` that takes a selector as an argument, selects elements from the DOM, and returns an array-like object:

```
var breeds = new $('#breeds');
breeds.length // 1
breeds[0] // [ ul#breeds ]
```

Solution:

```
$ = function(selector) {
    var elements = document.querySelectorAll(selector);
    for(var i = 0; i < elements.length; ++i) {
    	this[i] = elements[i];
    }
    this.length = elements.length;
};
```

Another possible solution:

```
$ = function(selector) {
    var elements = document.querySelectorAll(selector);
    Array.prototype.push.apply(this, elements);
    // Can also use [].push.apply(this, elements);
};
```

Remember that when you do `var breeds = new $('#breeds');`, the context is set to `breeds`. This is relevant to the `[].push.apply(this, elements);` statement.

## Exercise: Implementing the html() Method

Add an `html` method to get/set the innerHTML of an element(s).

```
var dogs = new $('#breeds li');
dogs.html('<div>All Dogs</div>').html() // <div>All Dogs</div>
```

Solution:

```
$.html = function(newHtml) {
    if(arguments.length) {
        $.each(this, function(i, el) {
           el.innerHTML = newHtml;
        });
        return this;
    }
    else {
        return this[0].innerHTML;
    }
```

Note the different getter and setter behavior based on whether `arguments.length` is truthy.

## Exercise: Implementing the val() function

Add a `val` method to get/set the value of an element.

```
<input type="text" value="some text">
$('input').val() // 'some text'
$('input').val('new text');
```

Solution: Exactly the same implementation as in `.html()`, except you replace `.innerHTML` with `.value`.

## Exercise: Eliminating `new`

Remove the need to call `new` when using our jQuery object.

`var dogs = $('#breeds li);`

Solution:

Inside the `$` function, add the following lines:

```
if( !(this instanceof $) ) {
    return new $(selector);
}
```

Note that using `if(this === window)` works as well, but it runs into an issue if you're designing your own plugin on top of jQuery by doing something like:

```
tabs.$ = $;
tabs.$("lis"); // this is tabs, not window
```

## Exercise: Implementing the text() Method

Add a `text` method to get/set the text of an element.

```
$('ul').text();
//
// Doberman
// Beagles
// Boxer
//
$('ul li:first-child').text('TEETH!').text(); // 'TEETH'
```

Solution:

```
var getText = function(el) {
    var txt = '';
    $.each(el.childNodes, function(i, childNode) {
        if(childNode.nodeType === Node.TEXT_NODE) {
            txt += childNode.nodeValue;
        }
        else if(childNode.nodeType === Node.ELEMENT_NODE) {
            txt += getText(childNode);
        }
    });
    return txt;
};

$.text = function(newText) {
    if(arguments.length) {
        // setter
        return $.each(this, function(i, el) {
            var text = document.createTextNode(newText);
            el.innerHTML = '';
            el.appendChild(text);
        });
    }
    else {
        // getter
        return this[0] && getText(this[0]);
    }
}
```

The setter logic is pretty intuitive, except that `var text = document.createTextNode(newText);` has to be inside `$.each`, not outside. This has to do with how JavaScript handles text nodes internally.

The getter logic relies on a helper function `getText`, which takes an element and iterates over the element's children. If the current child is a text node, that child's text is concatenated to `txt`. Otherwise, if the current child is an element, recursively call `getText` on that child and concatenate the result to `txt`.

What's interesting is that within a `ul` with 3 `li` elements, there are actually 7 child nodes: The 3 `li` elements themselves and 4 text nodes, which are essentially just whitespace and carriage returns between the `li`s. Then of course the content inside of the `li` is a text node (assuming there are no nested tags like `<strong>`).

Another note: `Node.TEXT_NODE === 3` and `Node.ELEMENT_NODE === 1`. Often in open source code you'll see those numerical contants used instead of the descriptive text.

###### Awesome Debugging Trick

When you click on an HTML element in the dev console, in the right panel there is a tab called properties. This lets you see all the properties on that particular element!

Also try `console.dir( document.createTextNode('abc') );`

###### Simple Tip

If you want to learn about the DOM, use MDN, not W3Schools.

## Exercise: Adding a find() Method

Add a `find` method that returns items within the current elements:

`var dogImages = $('div').find('img');`

Solution:

First, you need to modify `$` so that it accepts strings as well as arrays as possible types for the `selector` argument.

```
$ = function(selector) {
    if( !(this instanceof $) ) {
        return new $(selector);
    }
    var elements;
    if(typeof selector === 'string') {
        elements = document.querySelectorAll(selector);
    }
    else if(typeof selector === 'object') {
        // Currently assuming it's an array
        elements = selector;
    }
    [].push.apply(this, elements);
};
```

As for `$.find`:

```
$.find = function(selector) {
    var elements = [];
    $.each(this, function(i, el) {
        var els = el.querySelectorAll(selector);
        [].push.apply(elements, els);
    });

    return $(elements);
}
```

Iterate through each element of `this`. For each element, run `querySelectorAll` and push the results to the `elements` array, and return `elements` as a jQuery object.

So in the case of `$('div').find('img')`, the context of `this` is `$('div')`, so `$.each(this, ...)` is iterating over that `$('div')`.

# Traversing Elements

## Implementing the next() Method

Helpful properties

```
element.childNodes
element.firstChild
element.lastChild
element.nextSibling
element.previousSibling
element.parentNode
element.children
```

`element.children` gives you all the child nodes without the text nodes.

Unfortunately, `nextSibling` and `previousSibling` still include text nodes in their selections.

Solution:

```
$.next = function() {
    var elements = [];
    $.each(this, function(i, el) {
        var next = el.nextSibling;
        while(next && next.nodeType === Node.TEXT_NODE) {
            next = next.nextSibling;
        }
        if(next && next.nodeType === Node.ELEMENT_NODE) {
            elements.push(next);
        }
    });
    return $(elements);
}
```

The implementation for `$.prev()` is exactly the same (just replace `nextSibling` with `previousSibling`).

## Refactoring Traversing Code (Metaprogramming)

Notice that in `$.next`, `prev` and `parent` we kept using the same setup:

```
var elements = [];
$.each(this, function(i, el) {
	// Some actions here
    elements.push(<some computed value>); // ex: el.parentNode
});
return $(elements);
```

Our goal is to use a helper function to abstract away this repetition. The helper will take a callback function as an argument, and the callback's role is to return whatever it is you want to be pushed onto `elements`.

Solution:

```
var makeTraverser = function(cb) {
  return function() {
      var args = arguments;
      var elements = [];
      $.each(this, function(i, el) {
          var ret = cb.apply(el, args);
          if(ret && isArrayLike(ret)) {
              [].push.apply(elements, ret)
          }
          else if(ret) {
              elements.push(ret);
          }
      });
      return $(elements);
  }
};
```

As a result, we can do the following:

```
find: makeTraverser(function(selector) {
    return this.querySelectorAll(selector)
}),
next: makeTraverser(function() {
    var current = this.nextSibling;
    while(current && current.nodeType === Node.TEXT_NODE) {
        current = current.nextSibling;
    }
    if(current && current.nodeType === Node.ELEMENT_NODE) {
        return current;
    }
}),
prev: makeTraverser(function() {
    var current = this.previousSibling;
    while(current && current.nodeType === Node.TEXT_NODE) {
        current = current.previousSibling;
    }
    if(current && current.nodeType === Node.ELEMENT_NODE) {
        return current;
    }
}),
parent: makeTraverser(function() {
    return this.parentNode;
}),
children: makeTraverser(function() {
    return this.children;
})
```

# Attributes and Properties

## Creating a css() Method

`$('li')[0].style` only gives you the inline styles of the element.

To get the CSS applied to an element using native JavaScript, you actually need to do the following:

`document.defaultView.getComputedStyle($('li')[0]).getPropertyValue('display'); // 'inline-block'`

Solution:

```
$.css = function(cssPropName, value) {
    if(arguments.length === 2) {
        return $.each(this, function(i, el) {
            el.style[cssPropName] = value;
        });
    }
    else if(arguments.length === 1) {
        return this[0] && document.defaultView.getComputedStyle(this[0]).getPropertyValue(cssPropName);
    }
}
```

## DOM Layout and Positioning

In the DOM API:
- Width of div + padding + border: `offsetWidth` and `offsetHeight`
- Width of div + padding: `clientWidth` and `clientHeight`
- Width of div: Not given

To get the width of the element itself, can do something like:

```
$.width = function() {
    var clientWidth = this[0].clientWidth;
    var paddingLeft = parseInt(this.css('padding-left'));
    var paddingRight = parseInt(this.css('padding-right'));
    return clientWidth - paddingLeft - paddingRight;
}
```

Where `parseInt` is used to get rid of unwanted suffixes such as `px`.

Can check if `$('ul').width()` gives the same result as what is shown in the dev console's computed styles.

###### Offsets

An element's `offsetParent` is the first parent element that has a position property set to something other than `static`.

`offsetLeft` and `offsetTop` measure your element's offset from its `offsetParent`.

Do not confuse jQuery's `offset`, `offsetTop` and `offsetLeft` methods. Author's opinion that they got the naming backwards.

- `$().offset()` givs you the position on the page
- `el.getBoundingClientRect()` gives you the position relative to the viewport
- `$().position()` gives you `el.offsetLeft` and `el.offsetTop`
- `window.pageYOffset` tells you how far you've scrolled down the page

# Events

## Event API

A tiny gotcha with event handlers:

```
var breeds = document.getElementById('breeds');
breeds.addEventListener('click', function(ev) {
	console.log('clicked!');
}, false);
breeds.removeEventListener('click', function(ev) {
	console.log('clicked!');
}, false);
```

You cannot actually remove event listeners in this manner. Instead, you need to set your listener callback to a variable as follows:

```
var handler = function(ev) {
	console.log('clicked!');
};
var breeds = document.getElementById('breeds');
breeds.addEventListener('click', handler, false);
breeds.removeEventListener('click', handler, false);
```

###### Event Data

```
breeds.addEventListener('click', function(ev) {
	// click, keypress, etc
    ev.type;
    // element event was invoked on
    ev.target;
    // current element in propaation
    ev.currentTarget
    // other event properties of interest
    ev.timestamp;
    ev.bubbles;
    ev.cancelable;
    ev.eventPhase;
}, false);
```

## Event Propagation

Events have a capture (down) and bubble (up) phase. In the third parameter of `addEventListener`, you can set `useCapture` to `true` or `false` (it is `false` by default).

You can stop propagation on both phases
`ev.stopPropagation()`

`stopPropagation` will still fire the current event. To prevent that, use `stopImmediatePropagation`.

## Event Delegation

Register a handler on a parent element and do work if target element is the right type.

```
document.body.addEventListener('click', function(ev) {
	if(ev.target.nodeName === 'A') {
    	console.log('clicked on an anchor!');
        // do stuff
    }
}, false);
```

This listens on the body for a click, checks if the target was an anchor and then says you've clicked on an anchor. This approach is way faster than going onto every single anchor element and setting up an event handler.

jQuery has event delegation in its API:
```
$('body').on('click', 'div', function() {
	console.log('Here I am!');
});
```

# Building a Tabs Widget

Those who want to write a jQuery plugin would do something like the following:

```
$.fn.tabs = function() {
	// implementation
};
$('#breeds').tabs();
```

Note that `$.fn.<name>` is essentially just writing to `$`'s prototype.