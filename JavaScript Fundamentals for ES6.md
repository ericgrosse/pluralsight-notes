# Introduction

## ES6 The Specification

What's taking so long to implement ES6 is that it introduces  an extensive amount of new syntax to the language. ** This is the biggest addition to JavaScript since JavaScript was created.**

# Variables and Parameters

## `let`

The `let` keyword fixes the issue of `var` getting implicitly hoisted to the top of a function. It therefore gives you proper block-level scoping.

```
if(condition) {
	let x = 5;
}
return x; // x is undefined
```

```
for(let i = 0; i < 10; ++i) {}
return i; // i is undefined
```

## `const`

`const` has block-level scoping, just like `let`. The purpose of `const` is to create a read-only variable, in other words a constant.

```
const MAX_SIZE = 10;
MAX_SIZE = 12; // syntax error
```

```
const MAX_SIZE = 10;
var MAX_SIZE = 12; // TypeError: Duplicate declaration "MAX_SIZE"
```

```
let MAX_SIZE = 10;
var MAX_SIZE = 12; // TypeError: Duplicate declaration "MAX_SIZE"
```

## Destructuring

Can use `[x,y] = [y,x]` to swap variables (no array is created in the process)

Can use destructuring on function parameters. Example:

Old approach:

```
let doWork = function(url, config) {
	return config.data;
}
```

New approach:

```
let doWork = function(url, {data, cache, headers}) {
	return data;
}
```

Can also do nested destructuring:

```
let obj = {
  a: 1,
  b: 2,
  c: {
    d: 3,
    e: 4
  }
};

let { a, b, c: {d, e} } = obj;
console.log(a, b, d, e); // 1 2 3 4
```

Note that you are not assigning anything to the variable `c` in the above example.

