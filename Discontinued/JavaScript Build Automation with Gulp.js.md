Note that the course uses `Gulp v3`, although there is an easy migration to `v4` covered at the end of the course.

# The Value of Gulp as a JavaScript Runner

## Choosing Between Grunt and Gulp

The main difference between Gulp and Grunt is in their philosophy. Grunt is configuration over code, while Gulp is code over configuration.

Gulp works by using streams.

File system -> read files -> modify -> modify -> modify -> write files -> dest

The files stay in memory in the stream, and we can process and modify them as much as we want.

Uglifying is the same as minifying.

# 4 Things You Need to Know About Gulp

## Overview of Gulp APIs

Amazingly, gulp only has four APIs: `gulp.task`, `gulp.src`, `gulp.dest` and `gulp.watch`.

## `gulp.task`

```
gulp.task('js', ['jscs', 'jshint'], function() {
	return gulp
    	.src('./src/**/*.js')
        .pipe(concat('all.js'))
        .pipe(uglify())
        .pipe(gulp.dest('./build/'));
});
```

Note that the dependencies `jscs` and `jshint` are run **in parallel** before the `js` task runs.

There are three general cases for when you would want to create a new gulp task:

- Tests and Linting code
- Optimizing the files (ex/ minify, concatenate)
- Serve the application

## `gulp.src`

`gulp.src(glob [, options])`

`gulp.src` accepts two parameters. The first is a glob, which is a file pattern match for the source files that you want to enter into the stream.

The second parameter are optional `options` such as `options.base`. Example:

```
gulp.src('./src/**/*.js', {base: './src/})
.pipe(uglify())
.pipe(gulp.dest('./build/'));
```

This omits the `./src/` base from the dest. For instance:

Input: `./src/app/admin/admin.js`
Output: `./build/app/admin/admin.js` instead of `./build/src/app/admin/admin.js`

By default, if you do not pass in the base, the base is set to everything before the glob. In the above example, we could've ommitted the base and gotten the same result.

# Getting Started With Gulp

Author made a Github repo called pluralsight-gulp with the exercise material.

## Getting Node.js

Homebrew is one of the best ways to get and install things for a Mac.

In the terminal, simply enter `brew install node`

There's a Windows equivalent called chocolatey, whose terminal command is `choco`.

Remember that when you install Node on a mac, you need to alleviate the need to run `sudo` on npm global installs.

## Installing Gulp and Bower Globally

Bower is more commonly used just to get client JavaScript packages (ex/ jQuery, Angular).

Author chooses to use NPM to get server packages and Bower to get client packages.

## Creating a Local Package for a Project

Differences between dependencies and devDependencies:

dependencies
- Needed at run-time
- Examples:
 - Express, Angular, Bootstrap
- Code
 - `npm install --save`
 - `bower install --save`

devDependencies
- Needed during development
- Examples:
 - Concat, JSHint, Uglify

# Code Analysis With JSHint and JSCS

JSHint is a linting tool
JSCS is a JavaScript code style checker

Need to define `.jshintrc` and `.jscsrc` files.

A lot of popular code editors such as Sublime, WebStorm, Brackets and Atom will look for those `.rc` files in the root of your project and apply those rules in code style checking right to your code so we can see them right inside the editor.

Instructions for setting up `jscs` linting in Sublime can be found at the `SublimeLinter-jscs` repository on Github.

Any `jscs` errors found in Sublime will be highlighted in a 1 character wide yellow rectangle. If you place the cursor over a given rectangle, you can see a detailed error response at the bottom of the screen.

There is also a `SublimeLinter-jshint` package with virtually identical installation instructions.

## Coding the JSHint and JSCS Task

```
var gulp = require('gulp');
var jshint = require('gulp-jshint');
var jscs = require('gulp-jscs');

gulp.task('vet', function() {
  return gulp
    .src([
    './src/**/*.js',
    './*.js'
    ])
    .pipe(jscs())
    .pipe(jshint())
    .pipe(jshint.reporter('jshint-stylish', {verbose: true}));
});
```

**VERY IMPORTANT**: You cannot have `return` and `gulp` on separate lines, otherwise the linting task silently does nothing.

Note that the special reporter option `'jshint-stylish'` requires its own plugin that you have to `npm install`.

## Reusable Functions

The `gulp-util` package allows us to have a log function which will log out using different colors.

Combine the use of `util` with our own logging function to come up with a really effective logger:

```
var gulp = require('gulp');
var jshint = require('gulp-jshint');
var jscs = require('gulp-jscs');
var util = require('gulp-util');

gulp.task('vet', function() {
  log('Analyzing source with JSHint and JSCS');

  return gulp
    .src([
    './src/**/*.js',
    './*.js'
    ])
    .pipe(jscs())
    .pipe(jshint())
    .pipe(jshint.reporter('jshint-stylish', {verbose: true}));
});

/********
Functions
*********/

function log(msg) {
  if (typeof(msg) === 'object') {
    for (var item in msg) {
      if (msg.hasOwnProperty(item)) {
        util.log(util.colors.blue(msg[item]));
      }
    }
  } else {
    util.log(util.colors.blue(msg));
  }
}
```

## Failing the Task

Add the following to the end of your `vet` task:

```
.pipe(jshint.reporter('fail'));
```

## Conditionally Displaying the Source Files

Add

`var gulpPrint = require('gulp-print');`

and

`.pipe(gulpPrint())`

whenever you want to see all the files that a gulp pipeline is processing.

***

Can conditionally trigger `gulpPrint` using a command-line flag by doing the following:

```
var gulpif = require('gulp-if');
var args = require('yargs').argv;

...

gulp.src(...)
.pipe(gulpif(args.verbose, gulpPrint()))
```

Then in the terminal, enter `gulp vet --verbose`

## Reusable Configuration Module

Author likes to extract magic strings from his `gulpfile.js` and place it into a `gulp.config.js` file.

`gulpfile.js`

```
var config = require('./gulp.config')();
```

`gulp.config.js`

```
module.exports = function() {
  var config = {
    // all js to vet
    alljs: [
      './src/**/*.js',
      './*.js'
    ]
  };

  return config;
};
```

# CSS Compilation

## CSS Pre-Compilers and Vendor Prefixes

You can use `gulp` to add vendor prefixes to your CSS (useful for stuff like CSS animations where vendor prefixes are widely used)

A library called AutoPrefixer does this for us. What's neat is that it stays up to date by using the Can I Use site.

## Creating a Less and AutoPrefixer Gulp Task

```
npm install --save-dev gulp-less gulp-autoprefixer
```

`gulpfile.js`

```
var less = require('gulp-less');
var autoprefixer = require('gulp-autoprefixer');

...

gulp.task('styles', function() {
  log('Compiling Less --> CSS');

  return gulp
    .src(config.less)
    .pipe(less())
    .pipe(autoprefixer({browsers: ['last 2 version', '> 5%']}))
    .pipe(gulp.dest(config.temp));
});
```

`gulp.config.js`

```
module.exports = function() {
  var client = './src/client/';

  var config = {
    temp: './.tmp/',

    /**
     * File paths
     */
    alljs: [
      './src/**/*.js',
      './*.js'
    ],
    less: client + '/styles/styles.less'
  };

  return config;
};
```

If you run `gulp styles`, it successfully compiles the `less` stylesheet into `css`, including the autoPrefixer adjustments.

## Deleting Files in a Dependency Task

`npm install del --save-dev`

```
var del = require('del');

...

gulp.task('clean-styles', function() {
  var files = config.temp + '**/*.css';
  clean(files);
});

...

function clean(path) {
  log('Cleaning ' + util.colors.blue(path));
  del(path);
}
```

But now there's an issue because the `styles` task depends on `clean-styles`, which doesn't use streams. The solution to this is to use a callback, which we'll call `done`.

```
gulp.task('clean-styles', function(done) {
  var files = config.temp + '**/*.css';
  clean(files, done);
});

...

function clean(path, done) {
  log('Cleaning ' + util.colors.blue(path));
  del(path, done);
}
```

It's very important to return the callback when you're not actually returning the stream.

Note: I'm not even sure what the issue is, perhaps `del` is an asynchronous call and adding the callback forces it to wait until completion before we run the `styles` task.

**Important Note:** The `del` library has a change in version 2, now it uses promises instead of callbacks. Need to change the code as follows:

```
gulp.task('clean-styles', function() {
  var files = config.temp + '**/*.css';
  return clean(files);
});

...

function clean(path) {
  log('Cleaning ' + util.colors.blue(path));
  return del(path);
}
```

## Creating a Watch Task to Compile CSS

```
gulp.task('less-watcher', function() {
  gulp.watch([config.less], ['styles'])
});
```

Now any change to our `.less` file will trigger the `styles` task.

Note that you can use an array as the first parameter (the one that passes a glob).

## Handling Errors and Using Gulp Plumber

One approach to handling errors:

```
gulp.task('styles', ['clean-styles'], function() {
  log('Compiling Less --> CSS');

  return gulp
    .src(config.less)
    .pipe(less())
    .on('error', errorLogger)
    .pipe(autoprefixer({browsers: ['last 2 version', '> 5%']}))
    .pipe(gulp.dest(config.temp));
});

...

function errorLogger(error) {
  log('*** Start of Error ***');
  log(error);
  log('*** End of Error ***');
  this.emit('end');
}
```

Note that `this.emit('end')` terminates the piping process.

This does work, but you get overly detailed error logging in the console.

An alternative approach is to use `plumber`:

`npm install gulp-plumber --save-dev`

```
var plumber = require('gulp-plumber');

gulp.task('styles', ['clean-styles'], function() {
  log('Compiling Less --> CSS');

  return gulp
    .src(config.less)
    .pipe(plumber())
    .pipe(less())
    //.on('error', errorLogger)
    .pipe(autoprefixer({browsers: ['last 2 version', '> 5%']}))
    .pipe(gulp.dest(config.temp));
});

// No more need for our custom errorLogger function
```

# HTML Injection

## Exploring `wiredep` and `gulp-inject`

The HTML injection works by using a special commenting syntax inside our HTML file:

```
<html ng-app="app">
  <head>
    <!-- bower:css -->
    <!-- endbower -->

    <!-- inject:css -->
    <!-- endinject -->
  </head>
  <body>
    <div ng-include="'app/layout/shell.html'"></div>
    <!-- bower:js -->
    <!-- endbower -->

    <!-- inject:js -->
    <!-- endinject -->
  </body>
</html>
```

## Adding Bower Files and Your JavaScript to the HTML

```
npm install wiredep gulp-inject --save-dev
```

`gulpfile.js`

```
var wiredep = require('wiredep').stream;
var inject = require('gulp-inject');

...

gulp.task('wiredep', function() {
  var options = config.getWiredepDefaultOptions();

  return gulp
    .src(config.index)
    .pipe(wiredep(options))
    .pipe(inject(gulp.src(config.js)))
    .pipe(gulp.dest(config.client));
});
```

`gulp.config.js`

```
module.exports = function() {
  var client = './src/client/';
  var clientApp = client + 'app/';

  var config = {
    temp: './.tmp/',

    /**
     * File paths
     */
    alljs: [
      './src/**/*.js',
      './*.js'
    ],
    client: client,
    index: client + 'index.html',
    js: [
      clientApp + '**/*.module.js',
      clientApp + '**/*.js',
      '!' + clientApp + '**/*.spec.js'
    ],
    less: client + '/styles/styles.less',

    /**
     * Bower and NPM locations
     */
    bower: {
      json: require('./bower.json'),
      directory: './bower_components',
      ignorePath: '../..'
    }
  };

  config.getWiredepDefaultOptions = function() {
    var options = {
      bowerJson: config.bower.json,
      directory: config.bower.directory,
      ignorePath: config.bower.ignorePath
    };
    return options;
  };

  return config;
};
```

And that's all you need for the script injection to work in your `index.html` file!

**Important Note**: `wiredep` fails to inject `bootstrap.css` into the `index.html` page. To fix this, add `"dist/css/bootstrap.css"` into the `"main"` array inside of the `bower_components/bootstrap/bower.json` file.


## Adding Bower Files Automatically on Install

In your `.bowerrc` file, add:

```
"scripts": {
	"postinstall": "gulp wiredep"
}
```

This instructs any `bower install` to run `gulp wiredep` after the installation.

# Saving Your Dev Build

Author is taking the approach of restarting his entire node server on file changes.

## Prepare, Serve, and Restart the Code

`npm install --save-dev gulp-nodemon`

`gulpfile.js`

```
var nodemon = require('gulp-nodemon');
var port = process.env.PORT || config.defaultPort;

...

gulp.task('serve-dev', ['inject'], function() {
  var isDev = true;

  var nodeOptions = {
    script: config.nodeServer,
    delayTime: 1,
    env: {
      'PORT': port,
      'NODE_ENV': isDev ? 'dev': 'build'
    },
    watch: [config.server]
  };

  return nodemon(nodeOptions);
});
```

`gulp.config.js`

```
module.exports = function() {
    var client = './src/client/';
    var clientApp = client + 'app/';
    var temp = './.tmp/';
    var server = './src/server/';

    var config = {
        /**
         * Files paths
         */
        alljs: [
            './src/**/*.js',
            './*.js'
        ],
        client: client,
        css: temp + 'styles.css',
        index: client + 'index.html',
        js: [
            clientApp + '**/*.module.js',
            clientApp + '**/*.js',
            '!' + clientApp + '**/*.spec.js'
        ],
        less: client + 'styles/styles.less',
        server: server,
        temp: temp,

        /**
         * Bower and NPM locations
         */
        bower: {
            json: require('./bower.json'),
            directory: './bower_components/',
            ignorePath: '../..'
        },

        /**
         * Node settings
         **/
        defaultPort: 7203,
        nodeServer: './src/server/app.js'
    };

    config.getWiredepDefaultOptions = function() {
        var options = {
            bowerJson: config.bower.json,
            directory: config.bower.directory,
            ignorePath: config.bower.ignorePath
        };
        return options;
    };

    return config;
};

```

What's cool is that in this setup, any changes to your server code automatically causes the server to reload. So for example, you could go into `app.js` and change the port number, and the server would restart automatically using the new port.

## Run Tasks on Node Restart

You can add event handlers to the `nodemon` function call:

```
return nodemon(nodeOptions)
  .on('restart', function(evt) {
    log('*** nodemon restarted ***');
    log('files changed on restart:\n' + evt);
  })
  .on('start', function() {
    log('*** nodemon started ***');
  })
  .on('crash', function() {
    log('*** nodemon crashed: script crashed for some reason ***');
  })
  .on('exit', function() {
    log('*** nodemon exited cleanly ***');
  });
```

One awesome effect of the `.on('crash')` event is that your code is still running, meaning you can fix the error and your server will restart automatically!

You can also make the event handlers run gulp tasks every time they are fired. Example:

```
.on('restart', ['vet'], function() {...})
```

# Keeping Your Browser in Sync

## Exploring `browser-sync`

```
browserSync({
  proxy: 'localhost:8181',
  port: 3000,
  files: ['./src/client/**/*'],
  ghostMode: {
    clicks: true,
    location: false,
    forms: true,
    scroll: true
  },
  injectChanges: true,
  reloadDelay: 1000
});
```

- We can proxy the port where the app is being served into another port, like 3000.
- We can tell it which files to watch
- The ghostMode is a way to synchronize between the different browsers
- InjectChanges is a setting that defaults to true and if it can, it will try to inject just the file that had to change
- reloadDelay is how long you want to wait before you actually do the reload

## Configuring `browser-sync`

**Note:** For the purposes of the tutorial, make sure you're using browser-sync version 1.8.2.

`npm install browser-sync --save-dev`

```
var browserSync = require('browser-sync');

...

// Inside the gulp task 'serve-dev'
.on('start', function() {
  log('*** nodemon started ***');
  startBrowserSync();
})

...

function startBrowserSync() {
  if(browserSync.active) {
    return;
  }

  log('Starting browser-sync on port ' + port);

  var options = {
    proxy: 'localhost:' + port,
    port: 3000,
    files: [config.client + '**/*.*'],
    ghostMode: {
      click: true,
      location: false,
      forms: true,
      scroll: true
    },
    injectChanges: true,
    logFileChanges: true,
    logLevel: 'debug',
    logPrefix: 'gulp-patterns',
    notify: true,
    reloadDelay: 1000
  };

  browserSync(options);
}

```

## Injecting CSS from LESS

