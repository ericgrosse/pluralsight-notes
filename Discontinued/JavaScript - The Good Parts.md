# Programming Style & Your Brain

In the browser, you can open files directly using file -> open file -> browse to some html page

Start using plunker more often (plnkr). It is much better than JSFiddle.

## JavaScript: Good Parts and Bad Parts

JSLint was written by the author of Javascript: The Good Parts.

In most languages, the decision to put curly braces on the right or left is completely arbitrary. In Javascript, it's better to put them on the right.

```
return {
	ok: true
};
// perfectly fine

return
{
	ok: false
};
// silent error, returns undefined
```

This is because of automatic semicolon insertion. In this case, it puts a semicolon next to return.

A good reason to not use `switch` statements is because of the risk of unintentional code fallthrough (ie cascading if you forget a `break` statement).

We imagine that we spend most of our days power typing. In reality, most of our time goes towards making mistakes and correcting them, and going down to the abyss, in that cold dark place and finding what went wrong, fix it and then we forget about it.

## Understanding Good Programming Style

A programming style should not be about preference and self-expression, but rather be about reducing your error rate.

You shouldn't care about reducing the amount of code you type because that's not where we spend our time. We spend our time debugging. If I can save time debugging by typing in a little more code, that turns out to be a hugely good trade-off.

Good use of style can help reduce the occurrence of errors. Powerful analogy: Adding spacing, lowercasing and punctuation to language.

Programs must communicate clearly to people.

###### Immediately Invoked Function Expression Syntax

Two common styles for IIFEs:

```
(function() {
	...
})();
```

```
(function() {
	...
}());
```

The author prefers the 2nd style. What's not important is the function, it's the invokation of the function, and so you want the parentheses to be inclusive of the whole thing.

---

Don't use the `with` statement

## Avoiding Confusing Code

Always use `===`, never `==`

"If there is a feature of a language that is sometimes problematic, and if it can be replaced with another feature that is more reliable, then always use the more reliable feature."

---

Most programming languages use **block** scope, but JavaScript instead uses **function** scope. This means anything declared inside a function is not visible outside that function. The problem is that JavaScript has syntax which is identical to block scoped languages. And so when people come to JavaScript from Java, C, C++, etc., they get confused.

In a **block** scoped language, the best advice is to declare the variable in the innermost block where it makes sense to, probably at the sight of first use.

In a **function** scoped language, you want to declare all variables at the top of the function because this is how JavaScript interprets them.

---

Declare all functions before calling them. In other words, the definition of the function has to come before any invokation of it.

```
// Bad
foo();
var foo = function() {
	console.log('hello');
}
```

```
// Good
var foo = function() {
	console.log('hello');
}
foo();
```

---

`for (var i ...) {...}`
Variable `i` is not scoped to the loop! The bizarre solution to this is to place `var i` at the top of the function since that's what JavaScript does internally through hoisting.

Write in the language you're writing in. What's good in Java won't necessarily work in JavaScript, and vice versa.

## Avoiding Confusing Code (B)

Global variables are evil. However, if you have to use one, be explicit (ex/ `UPPER_CASE`).

If you have a function that's intended to be used as a constructor and someone calls that function without the `new` prefix, instead of initializing a new object, it's going to clobber global variables instead. Fortunately fixed in ES5 with `'use strict'`.

Because of this bug, constructor functions should be named with InitialCaps. Nothing else should be named with InitialCaps.

---

A simple gotcha:

`var a = b = 0`

You'd think it does

`var a = 0, b = 0`

What it actually does is

`b = 0; // global variable`
`var a = b`

---

Author joke: The last popular language to have pointer arithmetic in it was C++, a language so bad it was named after this operator.

If you're complaining about the typing speed of x += 1 compared to x++, use a keyboard macro.

Don't use the increment operator (pre and post-increment).

---

Programming is the most complicated thing that humans do. Computer programs must be perfect, and humans are not good at perfect. Designing a programming style demands discipline. It is not about selecting features because they are liked, or pretty, or familiar. The only thing that matters is that they **reduce your error rate**.

###### The Abyss

To be an effective developer, you must minimize your time in the abyss.

## Using JSLint

JSLint assumes the philosophy of language subsetting. Only a madman would use all of C++ for example. Every language designer makes mistakes, and once the language gets popular, they can't take back those mistakes.

One of the dangers of bad features in a language is not that they're useless, it's that they're dangerous. Clever people will always find a use for anything.

The best compromise you can make is to choose which features of a language you will use.

---

Most of the code has a negligible impact on performance. Only optimize the code that is taking the time.

Algorithm replacement is vastly more effective than code fiddling.

In web applications, most of the time is spent in the DOM. Supposedly the DOM API is horribly inefficient, constantly marshalling, doing layout, doing rendering. The amount of time spent running JavaScript is a tiny fraction of that. If you cut down the performance cost of your JavaScript to 0, most users would not notice any difference.

# And Then There Was JavaScript

## The History of JavaScript

People used to think there was nothing good about JavaScript.

The creator of JavaScript was given 10 days to design and implement the language.

Javascript, JScript and ECMAScript are all the same language.

## JavaScript Fundamentals

An object is a dynamic collection of properties. Every property has a key string that is unique within that object.

Javascript doesn't have classes, it has prototypes. It turns out prototypes are the more powerful idea, and the proof of that is you can easily simulate a class system with prototypes, but it's really hard to go the other way (classes simulating prototypes). Put another way, it's much easier to write JavaScript in a Java style than vice versa.

###### Working with prototypes

- Make an object that you like
- Create new instances that inherit from that object
- Customize the new objects
- Taxonomy and classification are not necessary (this is the advantage over a class based system)

###### How the `new` prefix operator works

```
function new(func, args) {
	var that = Object.create(func.prototype),
    	result = func.apply(that, args);
	return (typeof result === 'object && result) || that;
}
```

## Numbers

- Only one number type
- No integer types
- 64-bit floating point
- IEEE-754 (aka "Double", short for double precision in Fortran)

Decimal fractions are approximate

```
a = 0.1;
b = 0.2;
c = 0.3;

(a + b) + c === a + (b + c) // false
```

This isn't a bug in JavaScript, it's an issue with the IEEE binary floating point standard. In binary floating point, you cannot accurately represent binary fractions, you can only approximate. In retrospect, JavaScript should've used a decimal floating point format.

###### NaN

- NaN (not a number) is actually a special number
- It is the result of undefined or erroneous operations
- Toxic: Any arithmetic operation with NaN as an input will have NaN as a result.
- NaN is not equal to anything, including NaN
- `NaN === NaN // false`
- `NaN !== NaN // true`

## Strings and Arrays

### Strings

We don't know why strings are called strings!

Use "" for external strings and '' for internal strings.

The + operator in JavaScript can concatenate or add. This is a bad idea JavaScript inherited from Java. An especially common issue is when taking a number from an HTML form and adding it to another. Programmers often forget that the number in the form data is treated as a string, and it gets concatenated to the number they're trying to add, which gives a result that is orders of magnitude off.

Two ways to convert a number to a string
`str = num.toString();`
`str = String(num); // more commonly used`

Two ways to convert a string to a number
`num = Number(str);`
`num = +str;`

There is also the `parseInt` function. Unfortunately, it stops at the first non-digit character (without telling you).
Ex/ `parseInt("12em") === 12`

Another issue:

`parseInt("08") === 0`
`parseInt("08", 10) === 8`

`parseInt` is stupid in that if it encounters a leading 0, it treats the number as if it were octal. For this reason, the radix (10) should always be used as the 2nd parameter.

### Arrays

Do not use 'for in' with arrays because it doesn't guarantee the order of the iterated objects.

Good method to append to an array:
`myList[myList.length] = "barley";`
(Why not just use `push`?)

The dot notation should not be used with arrays.

###### Deleting Elements

```
myArray = ['a', 'b', 'c', 'd'];
delete myArray[1];
// ['a', undefined, 'c', 'd']
myArray.splice(1,1);
// ['a', 'c', 'd']
```

Delete an element of an array leaves a "hole" in it, although you can fix this by calling the splice method instead.

The first parameter of `splice` selects the index from where you wish to begin your deletion, and the second parameter is the number of elements you wish to delete.

###### Sorting

Javascript's array sort function is horrible for numbers (since they are coerced to type `String` for the comparison)

```
var n = [4, 8, 15, 16, 23, 42];
n.sort();
// n is [15, 16, 23, 4, 42, 8]
```

## Objects

All values are objects, except `null` and `undefined`

`null` is a value that isn't anything

`undefined` is a value that isn't even that. It is the default value for variables and parameters, as well as the value of missing members in objects.

`typeof null === 'object'` was a design mistake in JavaScript

Since `typeof array === 'object'`, you can instead use `Array.isArray(myArray)` to check whether something's an array

Falsy values:

```
false
null
undefined
""
0
NaN
```

All other values (including all objects) are truthy
Ex/ `"0"`, `"false"`
This means `[]` and `{}` are truthy

## Common JavaScript Statements

`&&`, the logical and, is sometimes called the guard operator. If the first operand is truthy, then the result is the second operand. Otherwise, the result is the first operand.

It is often used to avoid null references.

`return a && a.member;` is shorthand for

```
if (a) {
	return a.member;
}
else {
	return a;
}
```

`||`, the local or, is sometimes called the default operator. If first operand is truthy, then result is first operand, else result is second operand.

It is often used to fill in default values

`var last = input || nr_items;`

If `input` is truthy, then `last` is `input`, otherwise set `last` to `nr_items`

May not work as expected if the first operand is a number because 0 is falsy.

###### not Operator

- `!` is the prefix *logical not* operator
- If the operand is truthy, the result is false. Otherwise, the result is true
- `!!` produces booleans

###### Errors

In JavaScript you can `throw` anything

```
throw new Error(reason);
throw {
	name: exceptionName,
    message: reason
};
```

---

Adding something to a prototype is extremely efficient because the prototypes link to it, they don't copy from it. You can have one function that gets incorporated into thousands of objects and you only pay for one function.

# Function the Ultimate

## Background on Functions

Whereas other languages have methods, classes, constructors and modules, in JavaScript, functions do all of these things.

###### function expression

- Produces an instance of a function object
- Function objects are first class
 - May be passed as an argument to a function
 - May be returned from a function
 - May be assigned to a variable
 - May be stored in an object or array
- Function objects inherit from Function.prototype

I was surprised you can store a function in an array since you hardly ever see that. But sure enough:

```
var x = function() {return 6};
var y = function() {return 4};

var result = [];
result.push(x);
result.push(y);

console.log(result[0]()); // 6
```

Also of interest:

```
var fun = new Function();
console.dir(fun); // function anonymous() {}
console.dir(fun.__proto__.proto__) // Object
```

Since `Function.prototype` has functions on it, it essentially means **every function has methods**. For example, functions have the `call` and `apply` methods.

---

A variable declared anywhere within a function is visible everywhere within the function (this probably relates to hoisting).

###### function statement

The function statement is a short-hand for a `var` statement with a function value

`funtion foo() {}`

expands to

`var foo = function foo() {};`

which further expands to

`var foo = undefined;`
`foo = function foo() {};`

The assignment of the function is also hoisted

Keep in mind the implications of this!

```
function bill() {
	console.log(4);
}
var bill = 8;
bill(); // Uncaught TypeError: bill is not a function
```

###### Return statement

`return;` actually returns `undefined`

###### this

- The `this` parameter contains a reference to the object of invocation
- `this` allows a method to know what object it is concerned with
- `this` allows a single function object to service many objects
- `this` is key to prototypal inheritance

The use of `this` is one of the big code re-use ideas in JavaScript, where we could have a single inherited function which can work on many, many objects, and the way that function knows which object it is working on is by the `this` binding.

###### Function Invocation

If a function is called with too many arguments, the extra arguments are ignored.

If a function is called with too few arguments, the missing values will be set to `undefined`.

There is no implicit type checking on the arguments.

There are four ways to call a function:
- Function form
 - `functionObject(arguments)`
- Method form
 - `thisObject.methodName(arguments)`
 - `thisObject["methodName"](arguments)`
- Constructor form
 - `new FunctionObject(arguments)`
- Call/Apply form
 - `functionObject.apply(thisObject, [arguments])`

An inner function does not get access to the outer `this`. The workaround is to use `var self = this;` in the outer function.

###### Constructor form

`new FunctionValue(arguments)`

- When a function is called with the `new` operator, a new object is created and assigned to `this`
- If there is not an explicit return value, then `this` will be returned
- Used in the Pseudoclassical style

## Prototypal Inheritance (A)

###### Pseudoclassical

```
function Gizmo(id) {
	this.id = id;
}
Gizmo.prototype.toString = function() {
	return "gizmo " + this.id;
}
```

Author point blank said "functions in JavaScript are objects". Every function object has a prototype property, which is another object. The original, primordial Object in JavaScript is also a function. The prototype object has a constructor property pointing back to the original function object.

When you create `new Gizmo(string)`, the new gizmo inherits from `Gizmo.prototype`, which itself inherits from `Object.prototype`

###### Pseudoclassical Inheritance

If we replace the original prototype object, the we can inherit another object's stuff.

```
function Hoozit(id) {
	this.id = id;
}
Hoozit.prototype = new Gizmo();
Hoozit.prototype.test = function(id) {
	return this.id === id;
};
```

The key line there is `Hoozit.prototype = new Gizmo();` as this makes Hoozit's prototype point to Gizmo instead of Object.

This style of pseudoclassical inheritance is upsetting to Java programmers because stuff is being defined outside of the pseudoclass, meaning it is not self-contained. Replacing `Hoozit.prototype` with an instance of another object, which doesn't look anything like `extends`. Another issue is that if you ask `Hoozit` what is its constructor, it will claim it is `Gizmo` (not clear why `Hoozit` doesn't have a constructor). It's possible to repair this, but it's more work. The system is not looking out for you.

Virtually every AJAX framework has its own sense of how you should be building objects. Everyon is dissatisfied with the way the way language does it and so everyone tries to put something on top of it to make it neater. Author not sure if this is the right way to think about it but it's neater.

## Prototypal Inheritance (B)

A better way to think about inheritance: Function as a module.

###### A Module Pattern

```
var singleton = (function() {
	var privateVariable;
    function privateFunction(x) {
    	...privateVariable...
    }
    return {
    	firstMethod: function(a,b) {
        	...privateVariable...
        },
        secondMethod: function(c) {
        	...privateVariable...
        }
    };
}());
```

###### Power Constructors

1. Make an object
 - Object literal, `new`, `Object.create` , call another power constructor
2. Define some variables and functions
 - These become private members
3. Augment the object with privileged methods
4. Return the object

Example

```
function myPowerConstructor(x) {
	var that = otherMaker(x);
    var secret = f(x);
    that.priv = function() {
    	... secret x that ...
    };
    return that;
}
```

`myPowerConstructor` spelled with a lowercase because it doesn't need the `new` prefix. If you call this with the `new` prefix, all you'll do is waste time, it's not going to break.

`var that = otherMaker(x)`: Basically asking some other constructor to make the object for me. This gives another dimension of inheritance.

`var secret = f(x)`: I'm going to create my secret variables, my secret functions, and I can initialize them now or later

```
that.priv = function() {
    ... secret x that ...
};
```

I will create my privileged methods, which will have access to `secret`, `that`, and any parameters passed into the function (in this case, `x`)

---

Supposedly this is a really powerful way of creating objects. One benefit of this is that objects can have private state.

###### Functional Inheritance

Redefine our hoozit and gizmo with the new approach

```
function gizmo(id) {
	return {
    	id: id,
        toString: function() {
        	return "gizmo " + this.id;
        }
    };
}

function hoozit(id) {
	var that = gizmo(id);
    that.test = function(testid) {
    	return testid === this.id; // shouldn't it be that.id?
    };
    return that;
}
```

One benefit of this approach is privacy. To make the id private, you'd simply change the following 2 lines:

`return "gizmo " + this.id;` to `return "gizmo " + id;`

and

`return testid === this.id;` to `return testid === id;`

Other benefits: Shared secrets, super methods (which you don't even need)

---

Don't make functions in a loop

# Problems

## Problems 1-5

```
function identity(x) {
	return x;
}

function add(x,y) {
	return x + y;
}

function mul(x,y) {
	return x * y;
}

function identityf(x) {
	return function() {
  	return x;
  }
}

function addf(x) {
	return function(y) {
  	return add(x,y);
  };
}

function applyf(binary) {
	return function(x) {
  	return function(y) {
    	return binary(x, y);
    }
  }
}

console.log( applyf(add) );
console.log( applyf(add)(3) );
console.log( applyf(add)(3)(4) ); // 7
console.log( applyf(mul)(3)(4) ); // 12
```

The most interesting function here is `applyf`

## Problems 6-9

Write a function that takes a function and an argument, and returns a function that can supply a second argument

```
function curry(cb, x) {
	return function(y) {
  	return cb(x,y);
  }
}
```

Without writing any new functions show three ways to create the `inc` function

```
inc = addf(1);
inc = applyf(add)(1);
inc = curry(add,1);
```

Write methodize, a function that converts a binary function to a method

```
function methodize(binary) {
	return function(y) {
  	return binary(this, y);
  }
}

Number.prototype.add = methodize(add);
console.log( (3).add(4) ); // 7
```

Write demethodize, a function that converts a method to a binary function

```
function demethodize(method) {
	return function (that, y) {
  	return method.call(that, y);
  }
}

console.log( demethodize(Number.prototype.add)(5,6) ); // 11
```

## Problems 10-12

Write a function `twice` that takes a binary function and returns a unary function that passes its argument to the binary function twice

```
function twice(binary) {
	return function(a) {
  	return binary(a, a);
  }
}

var double = twice(add);
console.log( double(11) ); // 22
var square = twice(mul);
console.log( square(11) ); // 121
```

Write a function `composeu` that takes two unary functions and returns a unary function that calls them both

`composeu(double, square)(3); // 36`

Solution:

```
function composeu(unary1, unary2) {
	return function(x) {
  	return unary2(unary1(x));
  };
}
```

Write a function `composeb` that takes two binary functions and returns a function that calls them both

`composeb(add, mul)(2,3,5); // 25`

Solution:

```
function composeb(binary1, binary2) {
	return function(x, y, z) {
  	return binary2(binary1(x, y), z);
  };
}
```

## Problems 13-15

Write a function that allows another function to only be called once

`add_once = once(add);`
`add_once(3, 4); // 7`
`add_once(3, 4); // throw!`

Solution:

```
function once(func) {
	return function() {
    	var f = func;
        func = null;
        return f.apply(this, arguments);
    };
}
```

The most important line is `func = null`

---

Write a factory function that returns two functions that implement an up/down counter

`counter = counterf(10);`
`counter.inc() // 11`
`counter.dec() // 10`

Solution:

```
function counterf(value) {
	return {
  	inc: function() {
    	value += 1;
      return value;
    },
    dec: function() {
    	value -= 1;
      return value;
    }
  };
}
```

What's important here is that when you call `counter.inc()` and `counter.dec()`, you're invoking the inner function. The outer function is still operating on its copy of the variable `value`.

# Monads & Gonads

## Introduction to Monads

[Currently here]