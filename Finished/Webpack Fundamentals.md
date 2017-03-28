# Introduction

## The Need for a Build

Most projects avoid a build step for JavaScript because JavaScript doesn't have to be compiled, it gets interpreted by the browser, so a functional web application doesn't require a build step. Back when web applications first came on the scene, the idea of a build step was mostly ridiculous. But as time went by, web applications began to get larger and larger. As projects began to grow in size, they began to suffer from several different problems related to performance, maintenance and security.

###### Multiple Web Requests

It's not uncommon to have hundreds of JavaScript files in an application, which translates to hundreds of server requests from the browser. This can be avoided by concatenating all the JavaScript files into one (can also do the same for HTML, CSS and image files).

###### Code Size

Minification

###### File Order Dependencies

If you have hundreds of JavaScript files, some depending on others, handling file order dependencies can become untenable.  Supposedly Angular addresses this, as does the module system (the commonJS pattern of `require` & `module.exports`)

###### Transpilation

Transpilation allows you to run ECMAScript 6 features and TypeScript support on older browsers

###### Linting

Having a linter saves you from depending on your IDE for correcting errors in your code

## Other Solutions

- Server-side tools (ASP.NET, Rails, etc)
- Task runners (Grunt, Gulp, etc)

Server-side tools generally address minification & concatenation, but not all the other issues with large web applications

Task runners can be used to:
- Run automated tests whenever code changes are detected
- Help a new developer install & run the project
- Perform static analysis on your code
- With their plugin system, they are pretty unlimited in what they can accomplish

## The Webpack Solution

Can think of webpack as a specialized task runner that has been optimized to do one specific task very well: processing input files into output files.

Webpack utilizes components called loaders. You can send one or more source files into a loader, and one or more files will come out.

One feature that webpack has that is uncommon in task runners is the ability to combine CSS into JavaScript.

Webpack conventions:
- Use NPM, not Bower
- Use a module system

## Module Systems

`var members = require('./Members');`

When you are requiring the `Members` file, you are getting all the dependencies it requires as well.

# Basic Builds with Webpack

## CLI Basics

`npm install webpack -g`
`webpack ./app.js bundle.js`

## Adding a Config File

Create a file `webpack.config.js`
Once we have a config file in place, all we have to do is enter `webpack` in the terminal, and it will re-run the build with all the configuration settings that we want.

In the webpack config file, add the following:

```
module.exports = {
	entry: "./app.js",
	output: {
		filename: "bundle.js"
	}
}
```

## Watch Mode and the Webpack Dev Server

With watch mode, webpack will watch your files and whenever one of them changes, it will immediately re-run the build and recreate your output file.

Webpack has its own web server called the dev server.

`npm install webpack-dev-server -g`

Then in the terminal, run `webpack-dev-server`

**Important Note:** Both `webpack` and `webpack-dev-server` make use of the `webpack.config.js` file. In addition, `webpack-dev-server` doesn't actually use the bundle created by `webpack` but rather uses its own virtual bundle. Also, it seems both commands use a lot of the same command-line-flags (ex/ `--config`).

What's cool is that the webpack dev server has page auto-reload on changes setup by default!

To get autoreload functionality without the `app ready` status bar on your page, run:

`webpack-dev-server --inline`

It's a good idea to save these commands in the `package.json` file under `"scripts"` so you can re-use them with `npm run <command>` whenever needed.

## Building Multiple Files

Two ways to build with multiple files
- Use the usual `require` and `module.exports` pattern
- add an entry to the `entry` array in `webpack.config.js`. This is usually done for globally used JS files like a `utils`

So for example, if you have `entry: ["./utils", "./app.js"]`, then webpack will build a bundle using those two files (and their associated imports) as entrypoints.

## Using Loaders

In `webpack.config.js`, you add loaders in the following way:

```
module: {
    loaders: [
        {
            test: /\.es6$/,
            exclude: /node_modules/,
            loader: "babel-loader"
        }
    ]
}
```

Can think of loaders as being similar to functions performed in a `gulp` task pipeline (I'm not sure if this analogy is 100% right).

The `test` key's value is a regular expression that tests what type of files to run through this loader. The other properties are pretty self-explanatory.

To configure the extensions that `require` statements accept:

```
resolve: {
    extensions: ['', '.js', '.es6']
}

```

## Using Preloaders

The difference between loaders and preloaders isn't really explained (other than that preloaders are run first). My best guess is that preloaders aren't meant to transform the entrypoint files, so this is a good place to perform file checking such as linting for example.

The author's `webpack.config.js` file has errors in it, it should look like the following:

```
module.exports = {
	entry: ["./utils", "./app.js"],
	output: {
		filename: "bundle.js"
	},
	module: {
		preLoaders: [
			{
				test: /\.js$/,
				exclude: /node_modules/,
				loader: 'jshint-loader'
			}
		],
		loaders: [
			{
				test: /\.es6$/,
				exclude: /node_modules/,
				loader: 'babel-loader'
			}
		]
	},
	resolve: {
		extensions: ['', '.js', '.es6']
	}
}
```

`jshint-loader` expects there to be a `.jshintrc` file with at least an empty object `{}`.

## Creating a Start Script

In the `package.json` file, there's a `scripts.test` parameter by default. Change it so it says:

```
"scripts": {
	"start": "webpack-dev-server"
}
```

Then in the terminal, can use `npm start` instead of typing `webpack-dev-server` each time

## Production vs Dev Builds

`webpack -p` will create a `bundle.js` file that is minified.

`npm install strip-loader --save-dev`

Create a file `webpack-production.config.js` with the following contents:

```
var WebpackStrip = require('strip-loader');
var devConfig = require('./webpack.config.js');

var stripLoader = {
	test: [/\.js$/, /\.es6$/],
	exclude: /node_modules/,
	loader: WebpackStrip.loader('console.log')
};

devConfig.module.loaders.push(stripLoader);

module.exports = devConfig;
```

The file is essentially importing `webpack.config.js` and adding extra functionality onto it, in this case adding a strip loader that removes all `console.log` statements from `bundle.js`.

To run webpack with the production config file, enter:

`webpack --config webpack-production-config.js -p`

Can also use the same flags for `webpack-dev-server`:

`webpack-dev-server --config webpack-production-config.js -p`

Note that you can strip away more than `console.log` statements using `WebpackStrip.loader` (ex/ perfLogs). It takes an arbitrary number of comma delimited arguments.

# Advanced Builds with Webpack

## Organizing Files and Folders

You want to generate your `bundle.js` file in a folder that you ignore in source control.

Updated `webpack.config.js` file:

```
var path = require('path');

module.exports = {
  context: path.resolve('js'),
  entry: ["./utils", "./app.js"],
  output: {
    path: path.resolve('build/js/'),
    publicPath: '/public/assets/js/',
    filename: "bundle.js"
  },

  devServer: {
    contentBase: 'public'
  },

  module: {
    loaders: [
      {
        test: /\.es6$/,
        exclude: /node_modules/,
        loader: "babel-loader"
      }
    ]
  },

  resolve: {
    extensions: ['', '.js', '.es6']
  }
}
```

Updated `index.html` file:

```
<html>
  <body>
    <script src="/public/assets/js/bundle.js"></script>
  </body>
</html>
```

### The use of `path.resolve`

`var path = require('path')` is a built-in Node module, no need to `npm install` it. It is used to help us figure out paths.

### context

```
context: path.resolve('js'),
entry: ["./utils", "./app.js"]
```

The `context` sets a relative root directory for the `entry` key, meaning webpack will look for `utils.js` and `app.js` inside of the `js` directory.

If we didn't use a context, we could instead just do:

```
entry: ["./js/utils", "./js/app.js"]
```

So essentially the context is saving us from repeating the same base directory over and over.

### output

```
output: {
  path: path.resolve('build/js/'),
  publicPath: '/public/assets/js/',
  filename: "bundle.js"
}
```

The `output` key specifies where to put the created bundle.

The `path` is the path to the bundle in your source code.
The `publicPath` is the path to the bundle on the web server (you can see this when looking at the Sources tab in the chrome dev tools)
The `filename` is pretty self-explanatory

Note that the `publicPath` is what is used in the `script src` in our `index.html` page. This is why we have:

```
<html>
  <body>
    <script src="/public/assets/js/bundle.js"></script>
  </body>
</html>

```

### devServer

```
devServer: {
  contentBase: 'public'
}
```

This tells the web server that when somebody requests `index.html` from the root, it needs to look in the `public` directory.

Any requests for the root will come out of `public`, and any requests for `public/assets/js` will be served by `build/js`.

Think of it this way:
Source code: `public`
Web server: `/`

## Working with ES6 Modules

Author changes `js` files to use an `es6` extension, which is kind of silly.

## Adding Source Maps

To generate sourcemaps for your files, all you need to do is:

```
webpack -d
# or
webpack-dev-server -d
```

Motivation for using sourcemaps: Makes debugging much easier. You get to see `console.log` statements from their source files instead of some random line in the `bundle.js` file, for example.

What's even cooler is you can also minify your code and sourcemaps still allows you to interact with the source files exactly the same as before.

```
webpack-dev-server -d -p
```

## Creating Multiple Bundles

Creating multiple bundles is useful if you want to apply lazy loading. For example, suppose you want your `html` files to follow this pattern:

```
<html>
  <body>
    <script src="/public/assets/js/shared.js"></script>
    <script src="/public/assets/js/about.js"></script>
  </body>
</html>
```

The idea is to import a bundled `shared.js` file that is shared across all your HTML pages, and a page-specific bundle (in this case `about.js`).

To achieve this, make the following changes to `webpack.config.js`:

```
var path = require('path');
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('shared.js');

module.exports = {
  context: path.resolve('js'),

  entry: {
    about: './about_page.js',
    contact: './contact_page.js',
    home: './home_page.js'
  },

  output: {
    path: path.resolve('build/js/'),
    publicPath: '/public/assets/js/',
    filename: "[name].js"
  },

  plugins: [commonsPlugin],

  ...
}
```

First, we include `webpack` so that it can be used in the `commonsPlugin`. The `commonsPlugin` handles the creation of our `shared.js` bundle file.

Next, we change the entry values so it is now an object instead of an array.

Finally, the output filename is changed to `"[name].js"`. This uses the keys in the `entry` object as names.

# Adding CSS to Your Build

`npm install css-loader style-loader --save-dev`

We're using a new loader:

```
loaders: [
  {
    test: /\.css$/,
    exclude: /node_modules/,
    loader: 'style-loader!css-loader'
  }
]
```

The exclamation mark syntax indicates that the loaders are run from right to left. In this case, `css-loader` is run before `style-loader`. These two loaders actually work together to do their job.

Once that is setup, all you have to do to include CSS in your bundle is to use `require('/path/to/css/file')` and it's treated just like any other JavaScript import!

## Internal Implementation of CSS and Style Loaders

The Style and CSS loader create style tags in the `<head>` of the HTML page and embed all the style information inside.

If you run `webpack-dev-server` in production mode (using the `-p` flag for minification), and look inside the style tags of the HTML page, you'll see that even the CSS is being minified!

## Using SCSS and SASS

`npm install node-sass sass-loader --save-dev`

Need to add a new loader:

```
{
  test: /\.scss$/,
  exclude: /node_modules/,
  loader: 'style-loader!css-loader!sass-loader'
}
```

That's literally all you have to do. Incredible.

## Using LESS

`npm install less less-loader --save-dev`

Then just use the `less-loader` instead of the `sass-loader`

## Creating a Separate CSS Bundle

`npm install extract-text-webpack-plugin --save-dev`

Changes to `webpack.config.js`:

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');

...

plugins: [
  new ExtractTextPlugin('styles.css')
],

loaders: [
  {
    test: /\.css$/,
    exclude: /node_modules/,
    loader: ExtractTextPlugin.extract('style-loader', 'css-loader')
  },
  {
    test: /\.scss$/,
    exclude: /node_modules/,
    loader: ExtractTextPlugin.extract('style-loader', 'css-loader!sass-loader')
  }
]
```

Then in `index.html`, use the following imports:

```
<link rel="stylesheet" href="/public/assets/styles.css">
<script src="/public/assets/bundle.js"></script>
```

And now you have a separate JS and CSS bundle!

## Auto Prefixer

`npm install autoprefixer-loader --save-dev`

Then simply add the loader into the exclamation mark list, as follows:

```
{
  test: /\.scss$/,
  exclude: /node_modules/,
  loader: 'style-loader!css-loader!autoprefixer-loader!sass-loader'
}
```

I tested this with `transform: translateX(2px);` and the output was:

```
-webkit-transform: translateX(2px);
transform: translateX(2px);
```

# Adding Images & Fonts to Your Build

## Adding Images to a Build

`npm install file-loader url-loader --save-dev`

Add a new loader:

{
  test: /\.(png|jpg)$/,
  exclude: /node_modules/,
  loader: 'url-loader?limit=10000'
}

We can give loaders parameters by adding a question mark. In this case we want to set a size limit of roughly 10 kilobytes. What this setting does is it will look at any image that's getting processed and look at the size of it. Any image under that size is going to be inlined and is going to be turned into base64 encoded data. Any image bigger than that limit is going to be created as a separate image and will be a separate request.

This is nice because you can take any small images, inline them, and they'll come down with your `bundle.js` (meaning less requests are sent to the web server).

Two examples are provided, one for

```
<span id="img_container"></span>
```

and another for

```
<img class="bg_header_img" />
```

In the case of the `span`, inlining the image is actually not that trivial. Need to add the following to `app.js`:

```
var img = document.createElement('img');
img.style.height = '25%';
img.style.width = '25%';
img.src = require('../images/webpack_logo.png');

document.getElementById('img_container').appendChild(img);
```

In the case of the `img`, nothing has to be done because the css already provides the necessary url.

If everything worked correctly, you should see something like:

```
<img src="data:image/png;base64,iVBOR..." />
```

## Adding Fonts to a Build

`app.css` is changed as follows:

```
@font-face {
    font-family: 'Lora';

    /* These can be loaded thanks to the url-loader */
    src: url('../fonts/Lora-Regular.ttf');
    src: url('../fonts/Lora-Italic.ttf');
    src: url('../fonts/Lora-Bold.ttf');
    src: url('../fonts/Lora-BoldItalic.ttf');
}

body {
    font-family: 'Lora, Helvetica, Arial' ;
    padding-top: 80px;
}
```

Then simply change the loader we were using for images as follows:

```
test: /\.(png|jpg|ttf|eot)$/
```

As far as I can tell, no request is sent to the web server to load the fonts!

# Webpack Tools

## Introduction

Connect Middleware is middleware you use with a Node web server, either with `connect` or with `express`, that will automatically watch your files and serve them up. It's very similar to the `webpack-dev-server`, except it's a lot more fully featured, but also more complex. It's useful when you need to have a more complex server serving up your files, but you're already using node.

## Using the Connect Middleware

Once you add API endpoints to your project, you'll probably want to transition from using `webpack-dev-server` to one of the following options:

If you're using a Node server, you can go to the webpack dev middleware and run that with your Node server.

Otherwise, you can go to `webpack` itself, running it in watch mode, and then run whatever server you're developing against.

```
npm install ejs express morgan --save
npm install webpack-dev-middleware --save-dev
```

The author set up a barebones Express server. In the `server.js` file at the root of the project, here's the only webpack specific code:

```
// Only load this middleware in dev mode (important).
if (app.get('env') === 'development') {
  var webpackMiddleware = require("webpack-dev-middleware");
  var webpack = require('webpack');

  var config = require('./webpack.config');

  app.use(webpackMiddleware(webpack(config), {
    publicPath: "/build",

    headers: { "X-Custom-Webpack-Header": "yes" },

    stats: {
      colors: true
    }
  }));

}
```

## Demo: Using the Connect Middleware

If you run `node server.js`, you will see all the usual webpack logging in the terminal.

## Creating a Custom Loader

Create a custom loader that strips comments out of JSON

```
npm install json-loader strip-json-comments --save-dev
```

Add the following code in `loaders/strip.js`:

```
var stripComments = require('strip-json-comments');

module.exports = function(source) {
  this.cacheable();

  console.log('source', source);
  console.log('strippedSource', stripComments(source));

  return stripComments(source);
}
```

`this.cacheable()` lets webpack know that the loader itself is cacheable, which is just another word for deterministic (?), meaning given the same inputs, it's going to produce the same outputs.

(Since when does caching = determinism?)

Also add the following loader:

```
loaders: [
  {
    test: /\.json$/,
    exclude: /node_modules/,
    loader: 'json-loader!' + path.resolve('loaders/strip')
  }
]
```

## Using Plugins

```
npm install timestamp-webpack-plugin --save-dev
npm install jquery --save
```

Here's a plugin for adding globals to your build:

```
new webpack.ProvidePlugin({
  $: "jquery",
  jQuery: "jquery",
  "window.jQuery": "jquery"
})
```

In this setup, the jQuery object is now visible inside every module we have that is processed by webpack. I wonder if we could use this same setup to save from having to `require('react')` all the time.

Author also demonstrated two other plugins:

```
new TimestampWebpackPlugin({
  path: __dirname,
  filename: 'timestamp.json'
}),

new webpack.BannerPlugin("**********\nGenerated by webpack\n***************\n")
```

Note that `__dirname` refers to the current directory.

The first plugin generates a `timestamp.json` file at the root of the project. The second plugin adds a comment to the top of the `bundle.js` file.

# Webpack and Front End Frameworks

## Webpack React Build

```
npm install react react-dom --save
npm install babel-preset-react --save-dev
```

Make sure to use the babel loader and create a `.babelrc` file that contains:

```
{
  "presets": ["es2015", "react"]
}
```

Otherwise the webpack setup is exactly the same as we've been doing all along.

## Starting an Angular Build with Webpack

`npm install angular --save`

```
var app = angular.module('app', []);
```

This uses the `angular` global object. This is in conflict with webpack because webpack uses modules and module systems help you avoid globals.

The other consideration is that once we have our angular module (in this case, `app`), everything that we do with angular starts with that module, so that module needs to be available everywhere we want.

Could do `var angular = require('angular')` at the top of every file. However, there's a better solution.

## Adding Objects to Your Angular Build

Here is a rundown of the final files:

`js/bands/band-info.js`

```
module.exports = function(app) {
  app.directive('bandInfo', function(bandList) {
    return {
      template: require('./band-info.html'),
      restrict: 'E',
      controller: function($scope) {
        $scope.bands = bandList;
      }
    }
  })
}
```

`js/bands/band-info.html`

```
<h1 ng-repeat="band in bands">{{band.name}} - {{band.formed}}</h1>
```

`js/bands/bandList.js`

```
module.exports = function(app) {
  app.factory('bandList', function() {
    return [
      {name: 'Cinderella', formed: 1983 },
      {name: 'Bon Jovi', formed: 1983 }
    ]
  })
}
```

`js/bands/index.js`

```
module.exports = function(app) {
  require('./band-info')(app);
  require('./bandList')(app);
}
```

`js/index.js`

```
var angular = require('angular');
var app = angular.module('app', []);

require('./bands')(app);

```

`public/index.html`

```
<!DOCTYPE html>
<html ng-app="app">
<head>
  <title></title>
</head>
<body>
  <band-info></band-info>
  <script type="text/javascript" src="/public/assets/js/bundle.js"></script>
</body>
</html>
```

`webpack.config.js`

```
var path = require('path');

module.exports = {
  context: path.resolve('js'), 

  entry: ["./index"],
  output: {
    path: path.resolve('build/js/'), 
    publicPath: '/public/assets/js/',
    filename: "bundle.js"
  },

  devServer: {
    contentBase: 'public'
  },

  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "babel-loader"
      },
      {
        test: /\.html$/,
        exclude: /node_modules/,
        loader: "raw-loader"
      }
    ]
  },

  resolve: {
    extensions: ['', '.js', '.es6']
  }
};
```

In my opinion, it is pretty awkward to use the `commonJS` pattern with Angular.

## Adding Templates to Your Angular Build

Author mentions pre-caching templates (partial html files), probably worth looking into

Author also keeps pointing out advantages of using the `commonJS` module with Angular with respect to moving files and folders around.