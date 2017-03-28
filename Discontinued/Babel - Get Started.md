# Basics of Using Babel

## Understanding Babel

On Babel's main website, there is a "Try it out" button button in the menu. This brings you to a page where you have 2 panes, one to type in "future" JavaScript and another that shows how it gets transpiled. Can use this to test how, for instance, arrow functions get transpiled.

Example:

ES2015

```
var values = [3, 1, 2];
var result = values.sort((a, b) => a - b);
console.log(result);
```

ES5

```
"use strict";

var values = [3, 1, 2];
var result = values.sort(function(a, b) {
	return a - b;
});
console.log(result);
```

## Comparisons

TypeScript and Babel are often compared because both involve transpiling JavaScript code and outputing ES5 code but they're really quite different. TypeScript is a typed superset of JavaScript, whereas Babel is not a language. There is, however, collaboration between the projects.

## Installation

Babel Packages

- `babel-core` (won't need unless you write a library that has `babel-core` as a dependency)
- `babel-cli` (command line interface)
- `babel-plugin-*`
- `babel-preset-*`

This feature of opting into specific granular language features is new in babel 6. Previously you got all the features in an all-inclusive package named `babel`, then you had to turn off specific transformers. With babel 6, you install the `babel-cli` package, and turn on specific plugins by installing additional packages.

Since specifying and maintaining dozens of plugins in a config file would be a lot of work, babel 6 introduced the concept of plugin presets, which group together similar plugins for easy consumption. For example, there's a babel preset `es2015` which includes all the `es2015` plugins.

## Command-line Interface

To have babel transpile the ES2015 code from `src` and output it to `dest`:

`babel src --presets es2015 --out-dir dest`

The `presets` flag sets what transpilation is used. Can also use `-d` instead of `--out-dir`.

Interestingly enough, babel can also bundle the code you're transpiling:

`babel src --presets es2015 --out-file dest/bundle.js`

## Configuration

When creating your bundle, can add a `-s` flag, which will also generate a sourcemap file `bundle.js.map`. This allows you to debug back into your main source files.

Can also place a watch flag, `-w`, which will modify the bundle whenever a source file is changed.

Since our command is getting pretty long in the terminal, now would be a good time to write it into a `.babelrc` file.

```
{
	"presets": ["es2015"],
	"sourceMaps": true,
}
```

## Understanding Module Formatters

In ES2015, modules have been standardized in JavaScript as a language feature. There's no module keyword, a JavaScript file is a module, and everything is private or local unless explicitly exposed using the `export` keyword. Then you can import that module elsewhere in the code using the `import` keyword and have that module available to you.

Prior to ES module syntax, developers desperately needed this language feature, so they created their own specifications for how modules should work.

## Module Formatters Demo

Here's a simple example of ES2015 imports and exports:

```
export var operations ={
    add: function (a, b) {
        return a + b;
    },
    subtract: function (a, b) {
        return a - b;
    }
};

```

```
import {operations} from './arithmetic.js';

let result = operations.add(1,1);
console.log(result);

result = operations.subtract(3,1);
console.log(result);
```

# Browser Support

## Shims and Polyfills

Currently here