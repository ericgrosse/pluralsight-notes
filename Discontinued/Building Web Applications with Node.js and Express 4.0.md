# Getting Started

## Introduction and Installation

Node.js is an open source, server-side, runtime environment

Express is a web development framework for Node.js

All of our application is just one JavaScript file that is getting executed, `node app.js` (it pulls in other files through `require` statements)

## Configuring Brackets

Install the Delkos dark theme. You have to look it up on Github then install from URL. After that, go to `Themes` and select the newly installed theme.

Also install:
- Beautify
- Brackets File Tabs (then `View -> Show Tabs`)
- Brackets Icons
- Brackets CSS Color Preview
- Brackets Snippets

`Ctrl + Shift + L` does line re-indenting
`cl + Tab` snippet does `console.log`

The lightbulb icon on the right is the Snippets manager, which you can use to write your own snippets. It is braindead easy to do, simply hit the `New +` button and start typing!

## Versioning Packages

The caret `^` symbol in `package.json` dependencies tells npm to install a version of the package matching the same major version. Example:

`^4.13.4` will match `^4.xx.x`

The tilde will match the minor version, so `~4.13.4` will match `4.13.x`

You could always just put the package version directly. aka `4.13.3`. Using this is a very good idea for following Pluralsight courses since you'll avoid breaking changes as the course ages.

# Setting Up Express

## Our First App

```
var express = require('express');

var app = express();
var port = 5000;

app.listen(5000, function(err) {
    console.log('running server on port ' + port);
});
```

If you do `node app.js`, you will see `running server on port 5000`.

## Simple Routing

```
var express = require('express');

var app = express();
var port = 5000;

app.get('/', function(req, res) {
   res.send('Hello World');
});

app.get('/books', function(req, res) {
   res.send('Hello Books');
});

app.listen(5000, function(err) {
    console.log('running server on port ' + port);
});
```

The routes are defined in `app.get`. If you go to `localhost:5000` in the browser, you'll see `Hello World`, and if you go to `localhost:5000/books`, you'll see `Hello Books`.

## Bootstrap Templates

Go to bootstrapzero.com and download Storystrap. Extract its CSS and JS and paste it into a `public` directory, and its `index.html` file into `src/views`.

## Static Files

`app.use(express.static('public'));` tells Express to look for static files in the `public` directory.

If you go to `localhost:5000/css/styles.css`, you should see the raw code for the Storystrap stylesheet.

> Now in practice we're going to use things called templating engines and we're going to render HTML files. Honestly for right now, HTML is just another type of static file, we're not doing anything with it, we just need to serve that HTML file back up.

Set up another static directory with `app.use(express.static('src/views'))`

The Express router is going to first look in the Express static `public` directory, and if it doesn't find the file there, it's going to look in `src/views`. If it doesn't find the file there, it's going to look at our `app.get` for our routes.

Now if you go to `localhost:5000`, you will see the Storystrap demo page

> Now in theory I could put an entire static HTML website with links and everything in these two directories and they just work. I could put Angular code in my public JavaScript directory, I could build that whole single page app with static content in these two directories and be done, and just these 21 lines of code is all it takes to make that entire thing happen

## Bower

You can tell Bower to install packages in a directory other than `bower_components`. Create a `.bowerrc` file with the following contents:

```
{
  "directory": "public/lib"
}
```

Now execute `bower install bootstrap --save` and you should get both jQuery and Bootstrap in your `public/lib` directory.

In our `index.html` file, can now specify pathnames to vendor files with:

```
<link rel="stylesheet" href="lib/bootstrap/dist/css/bootstrap.min.css">
```

Author admits this pathname is long and painful and claims we'll use Gulp to address this.

# Setting up Gulp

## Coding Standards with JSHint and JSCS

The cool thing about Brackets is that is has `.jscsrc` and `.jshintrc` built in

Make sure to download:
- JSCS for Brackets (you actually need to do an additional `npm install` for this to work, instructions on the Github page)
- JSHint

Go to `Debug -> Open Preferences` and add the following to your `brackets.json` file:

```
"language": {
  "javascript": {
    "linting.prefer": [
      "JSHint",
      "JSCS"
    ],
    "linting.usePreferredOnly": true
  }
}
```

Currently, the `JSCS` extension seems to be broken so I removed it from my own config file.

`Ctrl + Shift + L` beautifies your entire code, not just indentation.

## JSCS in Gulp

`gulp-jshint` is working fine for me, `gulp-jscs` isn't doing anything however. The way to fix this is to downgrade `gulp-jscs` from `v3` to `v2`.

## Setting up Wiredep

There was a change with Bower Bootstrap from version 3.3.4 to 3.3.5 that messes up importing Bootstrap CSS via Wiredep. This is because in Bootstrap's own `bower.json` file, its `"main"` script points to `bootstrap.less` instead of `bootstrap.css`.

## Fixing Bootstrap and Wiredep

Add the following overrides to `bower.json`:

```
"overrides": {
  "bootstrap": {
    "main": [
      "dist/js/bootstrap.js",
      "dist/css/bootstrap.css",
      "less/bootstrap.less"
    ]
  },
  "font-awesome": {
    "main": [
      "less/font-awesome.less",
      "css/font-awesome.min.css",
      "scss/font-awesome.scss"
    ]
  }
}
```

Now the `gulp inject` task will output something like the following into your `index.html` (between the `<!-- bower: -->` lines):

```
<!-- bower:css -->
<link rel="stylesheet" href="../../public/lib/bootstrap/dist/css/bootstrap.css" />
<link rel="stylesheet" href="../../public/lib/font-awesome/css/font-awesome.min.css" />
<!-- endbower -->
<!-- bower:js -->
<script src="../../public/lib/jquery/dist/jquery.js"></script>
<script src="../../public/lib/bootstrap/dist/js/bootstrap.js"></script>
<!-- endbower -->
```

We don't want to see `../../public`, so to fix this, add `ignorePath: '../../public'` to wiredep options.

# Templating Engines

## Jade

Instead of

```
app.use(express.static('src/views'));
```

Use

```
app.set('views', './src/views');
app.set('view engine', 'jade');

app.get('/', function (req, res) {
  res.render('index');
});
```

And then `npm install jade --save`

## Jade and JavaScript

Can use variables and loops in Jade, so you can do some level of programming in your HTML through it

## Handlebars

Handlebars is a great minimalist templating engine that lets you write HTML and then plug some variables and some other functionality in as needed.

`npm install express-handlebars --save`

A drawback of handlebars is that its `{{}}` syntax conflicts with Angular's

## EJS

`npm install --save ejs`

# Routing

Didn't take notes for this section because I can't stand EJS syntax. Express routing is not that hard to figure out on your own, just a matter of smart refactoring as your application scales.

# Databases

## Introduction

> Typically in a Node.js course, you're not going to deal with a SQL server. A lot of the time, a lot of these blogs and tutorials deal with MongoDB.

> MongoDB seems to be the standard

## MS SQL

Skipped because SQL Server sucks

***

The node package `mssql` has built-in JSON support, which is huge. Your results coming back from the database are already going to be JSON.

The package has good documentation and is fairly straightforward to use. After some setup in `app.js`, you're basically just doing the following within a route's `get`:

```
request.query('select * from books', callback);
```

## Prepared Statements

Skipped because SQL Server sucks

## Middleware

Skipped because SQL Server sucks

## Adding Books

The following route inserts an array of book objects into a mongodb collection called `books`:

```
var express = require('express');
var adminRouter = express.Router();
var mongodb = require('mongodb').MongoClient;

var books = [
        {
            title: 'War and Peace',
            genre: 'Historical Fiction',
            author: 'Lev Nikolayevich Tolstoy',
            read: false
        },
        {
            title: 'Les Misérables',
            genre: 'Historical Fiction',
            author: 'Victor Hugo',
            read: false
        },
		...
    ];

var router = function (nav) {
    adminRouter.route('/addBooks')
        .get(function (req, res) {
            var url =
                'mongodb://localhost:27017/libraryApp';

            mongodb.connect(url, function (err, db) {
                var collection = db.collection('books');
                collection.insertMany(books,
                    function (err, results) {
                        res.send(results);
                        db.close();
                    }
                );

            });

            //res.send('inserting books');
        });

    return adminRouter;
};

module.exports = router;
```

Thus, whenever you hit the endpoint `localhost:3000/admin/addBooks`, the book objects get added to the books collection. This of course only works once; If you hit refresh, you'll get an error because you're trying to insert a bunch of duplicate entries.

All to say, this is a decent way of adding initial data a mongo database.

In the mongo shell itself, you can check that the insertions were successful:

```
mongo libraryApp
show collections
db.books.find().pretty()
```

Adding `.pretty()` to `db.books.find()` makes the entries a lot more readable.

# Authentication

[Currently here]
[Abandoning course, authentication topic already covered by Joe Eames in his course on the MEAN stack, don't care about 3rd party APIs at the moment]