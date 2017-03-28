# Lecture 1

Re-rendering on every change makes things simple

Virtual DOM elements don't render html, they render a JSON representation of html.

```
{
  element: 'div',
  attrs: {
    class: 'title'
  },
  children: [
    'Hello World!'
  ]
}
```

On every update:

- Render a new virtual DOM tree
- Calculate the diffs with the previous render of the virtual DOM tree
- Compute the minimum set of DOM mutations and put them in a queue
- Batch execute all DOM operations

DOM Library

```
React.createElement(<element name>, <attributes>, <children>);
```

Example:

```
React.createElement('h1', null, 'Hello World');
React.createElement('a',
{
  href: '#',
  className: 'link'
},
'Hello World!');
React.createElement('div', null, [
  React.createElement('h1', null, 'Hello'),
  React.createElement('h1', null, 'World')
]);
```

JSX converts our "HTML in JS" to the `React.createElement` calls of the DOM library.

In ES6, can rename your imports

```
import {value2 as val2} from './mod1';
```

Can also use aliases when destructuring:

```
const {first: f, last: l} = obj;
```

## Fat Arrow Functions

```
(arg1, arg2) => {
  return arg1 + arg2;
}
```

You don't need to use curly braces if the output is inlined.

```
(arg1, arg2) => arg1 + arg2
```

Also proper use of the `this` keyword.

ES5

```
function(arg1, arg2) {
  return arg1 + arg2;
}.bind(this);
// Or
var self = this;
function(arg1, arg2) {
  console.log(self);
  return arg1 + arg2;
}
```

ES6

```
(arg1, arg2) => {
  console.log(this);
  return arg1 + arg2;
}
```

In practice, the majority of event handlers are called through anonymous functions, which means `this` would be `undefined` without proper binding.

