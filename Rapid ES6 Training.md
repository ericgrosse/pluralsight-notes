# Course Overview

The formal name for `ES6` is `ES2015` but much of the developer community has stuck with the original name.

Google's latest Chrome browser supports 96% of `ES6` features, and Firefox & Edge are close behind (90% and 86%, respectively).

# New ES6 Syntax

Note: Assume `use strict` is always being used in the following examples.

What happens in the following?

```
console.log(productId); // undefined
var productId = 12;
```

`console.log(productId)` is undefined because the variable `productId` gets hoisted.

If instead we use `let`:

```
console.log(productId); // ReferenceError: productId is not defined
let productId = 12;
```

We get a `ReferenceError` because `productId` is no longer being hoisted.

Note: When I tried this in a React / Babel environment, both cases console logged `undefined`.

***

The `let` keyword also applies block level scoping:

```
let productId = 12;
{
  let productId = 2000;
}
console.log(productId); // 12
```

```
{
  let productId = 200;
}
console.log(productId); // ReferenceError: productId is not defined
```

```
function updateProductId() {
  productId = 12
}

let productId = null;
updateProductId();
console.log(productId); // 12
```

Even though the function `updateProductId` is being defined before the variable `productId`, it is being declared after `productId`.

```
let productId = 42;
for (let productId = 0; productId < 10; ++productId) {}
console.log(productId); // 42
```

The above works because `let productId = 0;` is scoped to the `for` loop.

```
let updateFunctions = [];
for (let i = 0; i < 2; ++i) {
  updateFunctions.push(function() { return i; });
}
console.log(updateFunctions[0]()); // 0
```

###### `const`

When you declare a variable using `const`, you must initialize it.

```
const MARKUP_PCT;
console.log(MARKUP_PCT); // Syntax Error: Unexpected token ;
```

Obviously you can't change a `const` once it's set.

```
const MARKUP_PCT = 100;
MARKUP_PCT = 10; // Syntax Error: "MARKUP_PCT" is read-only
```

`const` still respects block level scoping, so the following is allowed:

```
const MARKUP_PCT = 100;
if (MARKUP_PCT > 0) {
  const MARKUP_PCT = 10; // Declared in its own inner scope
}
console.log(MARKUP_PCT); // 100
```

## Arrow Functions

```
let getPrice = () => 5.99;
console.log(typeof getPrice); // function
```

The arrow function can be viewed as a mapping of input to output. In this case, the input is nothing `()` and the output is `5.99`.

The arrow function serves as a function shorthand, allowing you to leave out the word `function` and `return`.

Here's an example where the arrow input takes exactly argument. In this case, you can leave off the enclosing parentheses:

```
let getPrice = count => count * 4.00;
console.log(getPrice(2)) // 8
```

If you use more than one argument, you're once again forced to use parentheses on the input:

```
let getPrice = (count, tax) => count * 4.00 * (1 + tax);
console.log(getPrice(2, .07)); // 8.56
```

If the logic of the output is complex enough to warrant using a block, you end up having to use `return` explicitly.

```
let getPrice = (count, tax) => {
  let price = count * 4.00;
  price *= (1 + tax);
  return price;
}
console.log(getPrice(2, .07)); // 8.56
```

The real purpose of arrow functions is to handle the `this` keyword within functions.

In this example, we're going to handle a `click` event on the document. Whenever we have an event handler in JavaScript ES5, `this` gets set to the element that receives the event. This causes problems because we don't get access to the context of the function, we would only get passed in the element that receives the event.

```
document.addEventListener('click', function () {
  console.log(this); // #document
});
```

If instead we use an arrow function:

```
document.addEventListener('click', () => console.log(this)); // # Window {...}
```

Since we're in the global area rather than the context of a function, so `this` refers to the context of the actual code we're running, which is `window`, the global object.

Here's another example of ES5 `this`:

```
let invoice = {
  number: 123,
  process: function () {
    console.log(this);
  }
};
invoice.process(); // Object {number: 123}
```

This is showing that `this` is being set to the object on which the function is called, in this case `invoice`.

If instead you use the arrow function:

```
let invoice = {
  number: 123,
  process: () => console.log(this)
};
invoice.process(); // Window {...}
```

Again, we get the context of the code we're running, a global window object. If we were inside a function, we would get that function's context.

Here's another example:

```
let invoice = {
  number: 123,
  process: function() {
    return () => console.log(this.number);
  }
};
invoice.process()(); // 123
```

As far as I understand, the context of the arrow function is the enclosing `process` function, whose context itself is the `invoice` object. I can't possibly see how it could be a good idea to mix function and arrow notation together.

Now here's an important aspect of arrow functions:

```
let invoice = {
  number: 123,
  process: function() {
    return () => console.log(this.number);
  }
};
let newInvoice = {
  number: 456
};
invoice.process().bind(newInvoice)(); // 123
```

- `invoice.process()` returns the arrow function
- We attempt to bind the arrow function to a new object, but this doesn't do anything (doesn't throw an error, just gets ignored)
- The same thing happens if you attempt to use `call` or `apply` instead of `bind`
- The arrow function is invoked, returning `123` as before

A fun little gotcha:

```
let getPrice = ()
  => 5.99;
console.log(typeof getPrice); // SyntaxError: unexpected token =>
```

Note: This worked perfectly fine in a React / Babel environment.

Another gotcha:

```
let getPrice = () => 5.99;
console.log(getPrice.hasOwnProperty('prototype')); // false
```

Again, in my React / Babel environment this returned `true`, not sure why there are so many differences.

## Default Function Parameters

Default function parameters work the same way as they do in Python:

```
let getProduct = (productId = 1000) => console.log(productId);
getProduct(); // 1000
```

When a default parameter is explicitly set to `undefined`, the default is still used:

```
let getProduct = (productId = 1000, type = 'software') => console.log(productId + ', ' + type);
getProduct(undefined, 'hardware'); // 1000, hardware
```

You can do things like perform operations on defaults, as follows:

```
let getTotal = (price, tax = price * 0.07) => console.log(price + tax);
getTotal(5.00); // 5.35
```

Can even have the default access variables in the outer scope:

```
let baseTax = 0.07;
let getTotal = (price, tax = price * baseTax) => console.log(price + tax);
getTotal(5.00); // 5.35
```

```
let generateBaseTax = () => 0.07;
let getTotal = (price, tax = price * generateBaseTax()) => console.log(price + tax);
getTotal(5.00); // 5.35
```

A problem arises if you try to assign your second paramater to the first default parameter:

```
let getTotal = (price = adjustment, adjustment = 1.00) => console.log(price + adjustment);
getTotal(); // NaN
```

According to the author, this should actually result in `SyntaxError: Use before declaration`.

This problem only appears when the default value for price is used:

```
let getTotal = (price = adjustment, adjustment = 1.00) => console.log(price + adjustment);
getTotal(5.00); // 6
```

## Rest and Spread

Rest refers to gathering up parameters and putting them all into a single array, and Spread refers to spreading out the elements of an array (or even a string).

Rest example:

```
let showCategories = (productId, ...categories) => console.log(categories instanceof Array);
showCategories(123, 'search', 'advertising'); // true
```

```
let showCategories = (productId, ...categories) => console.log(categories);
showCategories(123, 'search', 'advertising'); // ["search", "advertising"]
```

```
let showCategories = (productId, ...categories) => console.log(categories);
showCategories(123); // []
```

Spread is pretty much the opposite of Rest.

```
let prices = [12, 20, 18]
let maxPrice = Math.max(...prices);
console.log(maxPrice); // 20
```

The spread operator takes an array and converts it into a list of parameters for the function (in this case `Math.max`).

More examples:

```
let maxCode = Math.max(...'43210');
console.log(maxCode); // 4
```

```
let codeArray = ['A', ...'BCD', 'E'];
console.log(codeArray); // ["A", "B", "C", "D", "E"]
```

## Object Literal Extensions

There is no longer a need to specify a key-value pair where the key and value name match. Can instead use the following shorthand:

```
let price = 5.99, quantity = 30;
let productView = {
  price,
  quantity
};
console.log(productView); // Object {price: 5.99, quantity: 30}
```

A similar shorthand applies to functions as well:

```
let price = 6.00, quantity = 10;
let productView = {
  price,
  quantity,
  calculateValue() {
    return this.price * this.quantity
  }
};
console.log(productView.calculateValue()); // 60
```

What's cool is that this shorthand behaves like the arrow function, meaning the context of `this` is being set correctly... Except that it didn't behave this way for me

```
let price = 6.00, quantity = 10;
let productView = {
  price: 3.00,
  quantity: 15,
  calculateValue() {
    return this.price * this.quantity
  }
};
console.log(productView.calculateValue()); // 45 (???)
```

Another neat feature is dynamic fields:

```
let field = 'dynamicField';
let price = 5.99;
let productView = {
  [field]: price
};
console.log(productView); // Object {dynamicField: 5.99}
```

Can even perform operations inside of the `[]` delimiters:

```
let field = 'dynamicField';
let price = 5.99;
let productView = {
  [field + '-001']: price
};
console.log(productView); // Object {dynamicField-001: 5.99}
```

# `for ... of` Loops

`for ... of` loops work similarly to Python `for ... in` loops.

```
let categories = ['hardware', 'software', 'vaporware'];
for (let item of categories) {
  console.log(item); // hardware software vaporware
}
```

Thankfully, they can be used as a replacement for `Array.prototype.forEach()`.

`for ... of` is also more flexible in that you can iterate through other data types such as strings:

```
let codes = 'ABCDF';
let count = 0;
for (let code of codes) {
  ++count;
}
console.log(count); // 5
```

## Octal and Binary Literals

Octals were confusing in `ES5`. If you were in strict mode, octals wouldn't even work, and if you weren't in strict mode, a lot of people didn't even realize that prepending a number with a `0` would create an octal value. Fortunately, this is all straightened out in `ES6`.

```
let value = 0o10;
console.log(value); // 8
```

The octal value would also work with an uppercase `O`, but this is confusing to read since `O` and `0` look very similar.

As for binary values:

```
let value = 0b10;
console.log(value); // 2
```

## Template Literals

There's now a way to embed variables into a string!

```
let invoiceNum = '1350';
console.log(`Invoice Number: ${invoiceNum}`); // Invoice Number: 1350
```

Note the use of the tilde characters over single quotes and the `${}` markup for the variable.

When using tildes as quotes, whitespace such as newlines and tabs are being maintained:

```
let message = `A
B
C`;
console.log(message); // A \n B \n C
```

You can interpolate an entire expression, not just a variable:

```
let invoiceNum = '1350';
console.log(`Invoice Number: ${'INV-' + invoiceNum}`); // Invoice Number: INV-1350
```

Now to make things confusing:

```
function showMessage(message) {
  let invoiceNum = '99';
  console.log(message);
}

let invoiceNum = '1350';
showMessage(`Invoice Number: ${invoiceNum}`); // Invoice Number: 1350
```

This happens because the interpolation happens before the function call.

## Destructuring

Example of destructuring an array:

```
let salary = ['32000', '50000', '75000'];
let [ low, average, high ] = salary;
console.log(average); // 50000
```

If you destructure using too many variables, the remaining variables get set to `undefined`:

```
let salary = ['32000', '50000'];
let [ low, average, high ] = salary;
console.log(high); // undefined
```

Can use whitespace to skip destructuring a particular element, as follows:

```
let salary = ['32000', '50000', '75000'];
let [ low, , high ] = salary;
console.log(high); // 75000
```

If you want to be really clever:

```
let salary = ['32000', '50000', '75000'];
let [ low, ...remaining ] = salary;
console.log(remaining); // ["50000", "75000"]
```

Can use defaults:

```
let salary = ['32000', '50000'];
let [ low, average, high = '88000' ] = salary;
console.log(high); // 88000
```

Can do nested destructuring:

```
let salary = ['32000', '50000', ['88000', '99000']];
let [ low, average, [actualLow, actualHigh] ] = salary;
console.log(actualLow); // 88000
```

Example of destructuring inside a function parameter:

```
function reviewSalary([low, average], high = '88000') {
  console.log(average);
}
reviewSalary(['32000', '50000']); // 50000
```

Destructuring works for objects as well!

```
let salary = {
  low: '32000',
  average: '50000',
  high: '75000'
};
let { low, average, high } = salary;
console.log(high); // 75000
```

Note that the destructuring variable names have to match associated keys in the `salary` object, otherwise the variable will be set to undefined.

A more verbose way to destructure:

```
let salary = {
  low: '32000',
  average: '50000',
  high: '75000'
};
let { low: newLow, average: newAverage, high: newHigh } = salary;
console.log(newHigh); // 75000
```

Destructuring a string:

```
let [minCode, maxCode] = 'AZ';
console.log(`min: ${minCode}, max: ${maxCode}`); // min: A, max: Z
```

## Advanced Destructuring

Destructuring with a trailing comma is ok:

```
let [high, low, ] = [500, 200];
console.log(`high: ${high}, low: ${low}`); // high: 500, low: 200
```

# ES6 Modules and Classes

## Module Basics

Simply by loading a module, the `ES6` module loader will put it in strict mode.


File `base.js`:

```
import {projectId} from './module1';
console.log(projectId);
```

File `module1.js`:

```
export let projectId = 99;
```

Console output is `99`.

***

You can export as many values or functions as you wish.

File `base.js`:

```
import {projectId, projectName} from './module1';
console.log(`${projectName} has id:  ${projectId}`);
```

File `module1.js`:

```
export let projectId = 99;
export let projectName = 'BuildIt';
```

Console output is `BuildIt has id:  99`.

***

You can alias what you import:

```
import {projectId as id} from './module1';
```

***

Import statements actually get hoisted to the top of the file.

***

If you leave off the curly braces `{}` in the `import` statement, the module loader is going to look for the default export:

File `base.js`:

```
import someValue from './module1';
console.log(someValue);
```

File `module1.js`:

```
export let projectId = 99;
let projectName = 'BuildIt';
export default projectName;
```

Console output is `BuildIt`.

Note that `someValue` serves as the alias for the default export of `projectName`.

***

When there's no default specified, it's ambiguous what should be imported:

File `base.js`:

```
import someValue from './module1';
console.log(someValue);
```

File `module1.js`:

```
let projectId = 99;
let projectName = 'BuildIt';
export {projectId, projectName};
```

Console output is `undefined`.

***

To import everything, use `*`:

File `base.js`:

```
import * as values from './module1';
console.log(values);
```


File `module1.js`:

```
let projectId = 99;
let projectName = 'BuildIt';
export {projectId, projectName};
```

Console output: `Object {projectId: 99, projectName: "BuildIt", __esModule: true}`

## Named Exports in Modules

File `base.js`:

```
import {projectId} from './module1';
projectId = 8000;
console.log(projectId);
```

File `module1.js`:

```
export let projectId = 99;
```

Console output is `Runtime error: projectId is read-only`.

As the error suggests, the named export is `read-only`. Like a `const`, we can access it but not change its value.

***

Here's an example of exporting an object:

File `base.js`:

```
import {project} from './module1';
project.projectId = 8000;
console.log(project.projectId);
```

File `module1.js`:

```
export let project = {
  projectId: 99
};
```

Console output is `8000`.

While `project` is read-only, as an object we're free to modify its properties.

***

If you export two functions, one which modifies the other function when invoked, that change carries over after the import. This is better explained with an example:

File `base.js`:

```
import {showProject, updateFunction} from './module1';
showProject();
updateFunction();
showProject();

```

File `module1.js`:

```
export function showProject() { console.log('in original'); };
export function updateFunction() {
  showProject = function() { console.log('in updated'); }
};
```

Console output is:

```
in original
in updated
```

Clearly when you import a variable or function into a file, you can think of it as though it's been defined in that file all along.

## Class Fundamentals

[Currently here]