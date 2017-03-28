# WebStorm Settings

Author turns off the following Inspection settings:
- Unresolved JavaScript function
- Unresolved JavaScript variable
- Signature mismatch problem

# Course Introduction

## Introduction

MongoDB stores its data in a format called BSON, which is just a binary representation of JSON.

## Prerequisites

The author recommends using `nodist` (instead of `nvm`) for Node version management

## Versions

- Angular 1.2.16
- Node 0.10.25
- Express 4.2.0
- MongoDB 2.4.1

## Course Updates

Check [https://github.com/joeeames/MEANAppsFiles](https://github.com/joeeames/MEANAppsFiles) as it provides workarounds for aspects of the course that are out of date.

# Creating a Walking Skeleton

## Introduction

> A Walking Skeleton is a tiny implementation of the system that performs a small end-to-end function. It need not use the final architecture, but it should link together the main architectural components. The architecture and the functionality can then evolve in parallel.

A walking skeleton is end-to-end. For us that means database to client.

## Creating the `package.json` file

```
npm install --save express jade
```

## Installing Client-side Dependencies with Bower

One of the common ways to organize a project in Node is to create two directories: One typically called `app` and another one called `public`. `app` would be the directory where you put your Node source files and `public` would be the directory where you put all of your client-side assets such as CSS, images, and JavaScript files.

Author opting to call our server-side folder `server` instead of `app`. In this case, `app` will hold our Angular files.

In the `server` directory, we're going to put all of our Node source code as well as our Jade views and client-side partials. This is a variation from the typical layout you'll see for a MEAN stack application.

> The reason I'm gonna put my client-side partials inside of my server directory is because since we're using Angular, we're not gonna have very many server-side views, and I want to be able to author my client-side views in Jade, which means that Node is actually going to have to process them as views and can't just deliver them untouched the way it would with HTML files.

Create a `.bowerrc` file:

```
{
	"directory": "public/vendor"
}
```

Then:

```
bower init
bower install jquery toastr angular angular-resource angular-route --save
```

## Creating the Node Application

Create a file `server.js` at the root of the project:

```
var express = require('express');

var env = process.env.NODE_ENV = process.env.NODE_ENV || 'development';

var app = express();

app.set('views', __dirname + '/server/views');
app.set('view engine', 'jade');

app.get('*', function(req, res) {
  res.render('index');
});

var port = 3000;
app.listen(port);
console.log('Listening on port ' + port);
```

The most interesting thing here is the catch-all route `app.get(*)`. This setup ensures that the `index` view is served regardless of what url you type into the browser. It is then the client-side router's responsibility to show the correct partial view.

In the `server` directory, add a folder `views` containing `index.jade`:

```
doctype
html
  head
    //link(href="/favicon.ico" rel="shortcut icon", type="image/x-icon")
  body
    h1 Hello World
```

## Creating a Layout

A cool advantage of using Jade is that it supports templating with `include`, `extends` and `block`

## Creating the Angular Application

Even though we're using Jade for our server views, we could still use plain HTML for our client-side partials.

Author opts to use Jade for the partials.

In `server.js`, partials are now handled as follows:

```
app.get('/partials/:partialPath', function(req, res) {
  res.render('partials/' + req.params.partialPath);
});
```

This co-ordinates with the client-side routing defined in `app/app.js`:

```
app.config(function($routeProvider, $locationProvider) {
  $locationProvider.html5Mode(true);
  $routeProvider
    .when('/', {
      templateUrl: '/partials/main',
      controller: 'mainCtrl'
    });
});
```

The request for the template URL `/partials/main` is handled by the server-side code `app.get('/partials/:partialPath)`, which then renders the correct partial file from the `partials` folder.

## Adding MongoDB

`npm install mongoose --save`

Add the following to `server.js`:

```
mongoose.connect('mongodb://localhost/multivision');
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error...'));
db.once('open', function() {
  console.log('multivision db opened');
});
```

Remember to run `mongod` in a separate terminal tab. Note that if the database you're connecting to doesn't already exist, it will be created for you automatically (supposedly).

## Displaying Data in a View

Add the following to `server.js`:

```
var messageSchema = mongoose.Schema({
  message: String
});
var Message = mongoose.model('Message', messageSchema);
var mongoMessage;
Message.findOne().exec(function(err, messageDoc) {
  mongoMessage = messageDoc.message;
});
```

Also change `app.get('*')` to include our `mongoMessage`:

```
app.get('*', function(req, res) {
  res.render('index', {
    mongoMessage: mongoMessage
  });
});
```

Finally, modify `index.jade` to display our `mongoMessage`:

```
extends ../includes/layout

block main-content
  section.content
    div(ng-view)
  h2= mongoMessage
```

Note that this won't initially work since our database is empty (in fact it doesn't exist!), meaning there's nothing to retrieve.

In RoboMongo, create a database named `multivision`, then create a collection called `messages` and right click `Insert Document...` to add the mongo message to the DB. This is all surprisingly easy to do! Definitely better than using the Mongo shell directly.

# Deploying to Heroku

## Preparing for Heroku

Inside `package.json`, add:

```
"engines": {
	"node": "<your node version>",
	"npm": "<your npm version>"
}
```

Also create `Procfile` (no extension) in the root of the project. Add the following:

```
web: node server.js
```

In `server.js`, change:

```
var port = 3030;
```

to

```
var port = process.env.PORT || 3030;
```

## Heroku and MongoDB

Unfortunately, once you push to Heroku, Heroku is not going to have access to the MongoDB instance running on your local environment. Also, Heroku itself does not offer MongoDB hosting, but what Heroku does offer is add-ons that allow you to use MongoDB with their service.

A drawback to using Heroku `MongoHQ` and `MongoLab` add-ons is that you have to verify your Heroku account, which means
using a credit card.

Another option is to sign up to `MongoHQ` or `MongoLab` directly and point your connection string to your hosting there.

Set up a database in mongolab:
- Cloud provider: Google
- Plan: Single-node Sandbox
- Database name: multivision
- Create a user `demo` with password `multivision`
- In RoboMongo, create a new connection using `ds011872.mlab.com` as the address and `11872` as the port
- In the `Authentication` tab, input the database name, username and password
- Insert a mongo message the same way you did for your local DB

Make sure your version of RoboMongo is up-to-date or the authentication step will fail

We can then switch between our local and hosted mongo database using Node's `env` variable:

```
if(env === "development") {
   mongoose.connect("mongodb://localhost/multivision");
}
else {
	mongoose.connect("<connection string from mongolabs>");
}
```

To switch to production mode, run:

```
NODE_ENV=production nodemon server.js
```

## Pushing to Heroku

```
heroku login
heroku create
heroku config:set NODE_ENV=production
git push heroku master
heroku ps:scale web=1
heroku open
```

## General Heroku Troubleshooting

```
heroku logs # for troubleshooting (USE THIS)
heroku restart # you occasionally need to do this
```

## Managing and Troubleshooting Heorku Keys

```
heroku keys:add
```

If you don't have any SSH keys, also need to do the following:

```
ssh-keygen # creates a private and public key
```

# Authentication

## Adding the Login Display

The following routing fails to catch partials with more than one path segment:

```
app.get('/partials/:partialPath', function(req, res) {
  res.render('partials/' + req.params.partialPath);
});
```

Instead do:

```
app.get('/partials/*', function(req, res) {
  res.render('partials/' + req.params[0]);
});
```

Author opts to move all the partials into `public/app` (where all the Angular code lives). Why didn't he just do this to begin with? The server-side routing changes once again:

```
app.get('/partials/*', function(req, res) {
  res.render('../../public/app/' + req.params[0]);
});
```

## Refactoring `server.js`

Author refactored config, express, mongoose and routes into separate files in a new `config` directory. In `server.js`, you now have:

```
var config = require('./server/config/config')[env];
require('./server/config/express')(app, config);
require('./server/config/mongoose')(config);
require('./server/config/routes')(app);
```

In each separate file, you have something like:

```
module.exports = function(app, config) {}
```

as a wrapper around the code we want refactored into a new file.

## Preparing for Login

`npm install passport passport-local --save`

Passport local helps us implement authentication using a user's username and password stored locally in our own database, as opposed to something like OAuth, which authenticates through a third party.

Here's a cute way to initialize entries in your database:

```
var userSchema = mongoose.Schema({
  firstName: String,
  lastName: String,
  username: String
});
var User = mongoose.model('User', userSchema);

User.find({}).exec(function(err, collection) {
  if (collection.length === 0) {
    User.create({firstName: 'Joe', lastName: 'Eames', username: 'joe'});
    User.create({firstName: 'John', lastName: 'Papa', username: 'john'});
    User.create({firstName: 'Dan', lastName: 'Wahlin', username: 'dan'});
  }
});
```

The key here here is `if (collection.length === 0)`

## Creating the Server Login Code

`npm install cookie-parser express-session --save`

The setup for Passport is pretty involved, wish the author would explain it more. For now, I'm content with treating it like boilerplate code and worrying about it later if there's any problems.

## Creating the Client Login Code

Here is our naive login:

`MyNavbarLoginCtrl.js`

```
angular.module('app').controller('MyNavbarLoginCtrl', function($scope, $http) {
  $scope.signin = function(username, password) {
    $http.post('/login', {username: username, password: password})
      .then(function(response) {
        if (response.data.success) {
          console.log('logged in!');
        }
        else {
          console.log('failed to login!');
        }
      });
  };
});
```

`routes.js`

```
app.post('/login', function(req, res, next) {
  var auth = passport.authenticate('local', function(err, user) {
    if (err) { return next(err); }
    if (!user) { res.send({success: false}); }
    req.logIn(user, function(err) {
      if (err) { return next(err); }
      res.send({success: true, user: user});
    });
  });
  auth(req, res, next);
});
```

So far this setup only checks the username against our MongoDB users, meaning the password is being ignored.

## Improving the Client Login Code

Author opting to use `app/common` to place Angular services (I rather like this approach).

We end up creating three separate services. Here is a summary of all the changes:

`navbar-login.jade`

```
.navbar-right(ng-controller="MyNavbarLoginCtrl")
  form.navbar-form(ng-hide="identity.isAuthenticated()")
    .form-group
      input.form-control(placeholder="Email", ng-model="username")
    .form-group
      input.form-control(type="password", placeholder="Password" ng-model="password")
    button.btn.btn-primary(ng-click="signin(username, password)") Sign In
```

The interesting change here is:

```
ng-hide="identity.isAuthenticated()"
```

This hides the login form when the user is authenticated, using our newly created Identity service.

`Identity.js`

```
angular.module('app').factory('Identity', function() {
  return {
    currentUser: undefined,
    isAuthenticated: function() {
      return !!this.currentUser;
    }
  };
});
```

`currentUser: undefined` acts as a default and `return !!this.currentUser` coerces `currentUser` into a boolean.

``

`Notifier.js`

```
angular.module('app').value('Toastr', toastr);

angular.module('app').factory('Notifier', function(Toastr) {
  return {
    notify: function(msg) {
      Toastr.success(msg);
      console.log(msg);
    }
  };
});
```

`angular.module('app').value('Toastr', toastr)` sets a global variable `Toastr` to the contents of the `toastr` script (if I understood correctly)

This service is essentially just a wrapper around Toastr

`Auth.js`

```
angular.module('app').factory('Auth', function($http, Identity, $q) {
  return {
    authenticateUser: function(username, password) {
      var deferred = $q.defer();
      $http.post('/login', {username: username, password: password})
        .then(function(response) {
          if (response.data.success) {
            Identity.currentUser = response.data.user;
            deferred.resolve(true);
          }
          else {
            deferred.resolve(false);
          }
      });
      return deferred.promise;
    }
  }
});
```

This service performs the HTTP POST request on `/login` and returns a promise that resolves to either `true` or `false` depending on whether the user was successfully authenticated or not.

`NavbarLoginCtrl`

```
angular.module('app').controller('NavbarLoginCtrl', function($scope, $http, Identity, Notifier, Auth) {
  $scope.identity = Identity;
  $scope.signin = function(username, password) {
    Auth.authenticateUser(username, password)
      .then(function(success) {
        if (success) {
          Notifier.notify('You have successfully logged in!');
        }
        else {
          Notifier.notify('Username/Password combination is incorrect');
        }
    });
  };
});
```

This puts everything together. First, you authenticate the user, then notify the user whether or not their login was successful.

# Security and Authorization

## Implementing Secure Passwords

> Storing passwords in clear text should never be done. Even if your website isn't important, and there's nothing anybody can steal by getting unauthorized access to your system, the usernames and passwords of your users are still very valuable. Since your users are likely to use the same passwords on other sites, they could be used to gain access to other, more important sites. Therefore, you should always store your users' passwords in an encrypted manner.

There are two types of encryption: One-way and two-way. When storing passwords, it's always best to store them in a one-way manner so that they can never be decrypted back into their original, clear text form. This one-way encryption is called hashing.

Very basically, you start out with a clear text password and a salt. A salt is just a randomly generated number. You take these two pieces of data, put them into a hashing algorithm, and then what comes out is a hashed password. This cannot be reversed back into its original form.

It's also always best to use a unique salt with each password. Using the same salt for all passwords can be a security vulnerability. Therefore, for each user, we're going to want to store both their salt and their hashed password.

Node comes with a built-in cryptography module named Crypto.

In `mongoose.js`, add the following functions:

```
function createSalt() {
  return crypto.randomBytes(128).toString('base64');
}

function hashPwd(salt, pwd) {
  var hmac = crypto.createHmac('sha1', salt);
  hmac.setEncoding('hex');
  hmac.write(pwd);
  hmac.end();
  return hmac.read();
}
```

We also need to modify our Schema to include a `salt` and `hashed_pwd` field, and to hash the password of our 3 dummy  users:

```
var userSchema = mongoose.Schema({
  firstName: String,
  lastName: String,
  username: String,
  salt: String,
  hashed_pwd: String
});
var User = mongoose.model('User', userSchema);

User.find({}).exec(function(err, collection) {
  if (collection.length === 0) {
    var salt, hash;

    salt = createSalt();
    hash = hashPwd(salt, 'joe');
    User.create({firstName: 'Joe', lastName: 'Eames', username: 'joe', salt: salt, hashed_pwd: hash});

    salt = createSalt();
    hash = hashPwd(salt, 'john');
    User.create({firstName: 'John', lastName: 'Papa', username: 'john', salt: salt, hashed_pwd: hash});

    salt = createSalt();
    hash = hashPwd(salt, 'dan');
    User.create({firstName: 'Dan', lastName: 'Wahlin', username: 'dan', salt: salt, hashed_pwd: hash});
  }
});
```

A minor security flaw is that we're sending the entire user object down to the client whenever somebody logs in. These hashed passwords are really quite secure, but there's really no need to be sending the salt and the hashed password down to the client.

In `server.js`, change 

```
if (user)
```

to

```
if (user && user.authenticate(password))
```

And in `mongoose.js`, add the `authenticate` method as follows:

```
userSchema.methods = {
  authenticate: function(passwordToMatch) {
    return hashPwd(this.salt, passwordToMatch) === this.hashed_pwd;
  }
};
```

## Persisting Login Between Page Refreshes

Once we sign in, if we refresh our page, the page no longer remembers that we're signed in again. The client-side code is asking us to sign in again because it doesn't know who the current user is, but the server does. We can prove that by adding some middleware.

An approach we could take is to simply have the server send down the current user (if there is one) alongside the index page. This is known as bootstrapping data.

Changes the catch-all route in `routes.js` to include the bootstrapped user:

```
app.get('*', function(req, res) {
  res.render('index', {
    bootstrappedUser: req.user
  });
});
```

In `layout.jade` add `include currentUser` before `block main-content` and create a new file `currentUser.jade`:

```
if !!bootstrappedUser
  script.
    window.bootstrappedUserObject = !{JSON.stringify(bootstrappedUser)}
```

If there is a bootstrapped user, set a global window property to that user.

Now we need to make Angular aware of the bootstrapped user. In `Identity.js`, modify as follows:

```
angular.module('app').factory('Identity', function($window) {
  var currentUser;
  if (!!$window.bootstrappedUserObject) {
    currentUser = $window.bootstrappedUserObject;
  }

  return {
    currentUser: currentUser,
    isAuthenticated: function() {
      return !!this.currentUser;
    }
  };
});
```

Notice the use of `$window` instead of directly calling `window`. Supposedly this is better for testing purposes.

The change to `Identity.js` causes the server to restart because Nodemon noticed a change to a JavaScript file. Unfortunately it didn't need to restart the server because we just changed client-side code. This is one of the drawbacks of Nodemon: It cannot distinguish between client-side and server-side JavaScript files.

## Implementing Clientside Authorization

Authentication tells us whether or not a user has been logged in successfully, but authorization tells us whether or not a user can access certain areas or perform certain functions. Passport only implements authentication and not authorization, so we'll have to do our authorization by hand.

Our authorization implementation is going to follow the standard method of using roles. In this case, we'll have standard users and admins.

### Code Changes

In `mongoose.js`, update the `userSchema` to include `roles: [String]`.

For the 3 dummy users, give the first `roles: ['admin']`, the second `roles: []` and don't add roles to the last user at all.

What's interesting is that Mongoose will automatically add a roles property whose value is an empty array for the last user (the one with no role property specified).

### Client-Side

> Now, there's a couple of ways that we could do this. We could just add in a function that checks the user's `roles` property and looks for an array containing the String `admin`, and just call that everywhere we need to determine whether ot not a user is an admin. But it would be much better if we actually added in a bit of modeling. The easiest way to do that in Angular is to implement resources.

In `public/app/account`, create `User.js` with the following contents:

```
angular.module('app').factory('User', function($resource) {
  var UserResource = $resource('/api/users/:id', {_id: "@id"});

  UserResource.prototype.isAdmin = function() {
    return this.roles && this.roles.indexOf('admin') > -1;
  }

  return UserResource;
});
```

In `Auth.js`, add in the following:

```
var user = new User();
angular.extend(user, response.data.user);
Identity.currentUser = user;
```

This has the effect that `user` has all the data from `response.data.user` while also having access to the `isAdmin` function.

Similarly, in `Identity.js`, add the following:

```
currentUser = new User();
angular.extend(currentUser, $window.bootstrappedUserObject);
```

All that remains is to add a dropdown entry in the UI that only admins can see:

```
li(ng-show="identity.currentUser.isAdmin()")
  a(href="/admin/user") User Admin
```

And add a script tag for the newly added `User` service.

## Implementing Server-Side Authorization

Add the following two functions to `server/config/auth.js`:

```
exports.requiresApiLogin = function(req, res, next) {
  if (!req.isAuthenticated()) {
    res.status(403);
    res.end();
  }
  else {
    next();
  }
};

exports.requiresRole = function(role) {
  return function(req, res, next) {
    if (!req.isAuthenticated() || req.user.roles.indexOf(role) === -1) {
      res.status(403);
      res.end();
    }
    else {
      next();
    }
  }
};
```

Note that `res.isAuthenticated()` is a function that Passport attaches to the request object.

`requiresApiLogin` is a middleware function that returns a 403 if the user isn't authenticated, and forwards to the next request otherwise.

`requiresRole` does the same thing but also authenticates that the user has a matching role (such as admin). Note that `requiresRole` returns a function so that we can invoke it at the top-level with a role passed in.

In our `routes file`, add the following route:

```
app.get('/api/users', auth.requiresApiLogin, function(req, res) {
  User.find({}).exec(function(err, collection) {
    res.send(collection);
  });
});
```

In the case where you're checking for user roles, change `auth.requiresApiLogin` to `auth.requiresRole('admin')`.

Side-note: It's interesting that you can pass middleware as a 2nd parameter to the route, right before the usual callback function.

## Protecting Admin Pages on the Client

If a user who isn't an admin navigates to `/admin/users`, they will see an empty list of users because the API route is already protected on the server side. However, it would still lead to a better user experience to redirect unauthorized users to a different page.

In the client-side route (defined in `app.js`), add the following route:

```
.when('/admin/users', {
  templateUrl: '/partials/admin/user-list',
  controller: 'UserListCtrl',
  resolve: {
    auth: function(Identity, $q) {
      if (Identity.currentUser && Identity.currentUser.roles.indexOf('admin') > -1) {
        return true;
      }
      else {
        return $q.reject('not authorized');
      }
    }
  }
})
```

The use of `$q.reject('not authorized')` might seem strange, but it is used in our module's `run` function, as follows:

```
angular.module('app').run(function($rootScope, $location) {
  $rootScope.$on('$routeChangeError', function(evt, current, previous, rejection) {
    if (rejection === 'not authorized') {
      $location.path('/');
    }
  });
});
```

On a route change error (caused by our `$q.reject`), if the rejection string matches `not authorized`, redirect to the homepage.

# Client-side Testing

## Installing Testing Tools

`karma` has a helpful CLI for generating a karma config file for you.

```
karma init
mocha
no (to Require.js plugin)
Chrome

public/app/**/*.js
tests/test/**/*.js

public/app/app.js
yes
```

In the generate `karma.conf.js` file, change the following:

```
frameworks: ['mocha', 'chai', 'sinon-chai'],
```

```
files: [
  'public/vendor/angular/angular.js',
  'public/vendor/angular-resource/angular-resource.js',
  'public/vendor/angular-mocks/angular-mocks.js',
  'test/test-app.js',
  'public/app/**/*.js',
  'tests/test/**/*.js'
],
```

Now add a folder `test` with the file `test-app.js` containing the following:

```
angular.module('app', ['ngResource']);
var toastr = {};
```

Now run `karma start`.

For some reason I got the error ` Module 'app' is not available`.

# Implementing Signup - Creating Entities

## Implementing Model Validation

In a mongoose schema, you can specify that a certain field must be unique using `unique: true`. Example:

```
var userSchema = mongoose.Schema({
  username: {
    type: String,
    required: '{PATH} is required!',
    unique: true
  });
```

To handle username case sensitivity, convert the username to lowercase when saving the user, and do the same when authenticating the user.

# User Profile - Updating Data

## Creating the Client-side Code

By default, Angular will use a POST when we call save, and it doesn't have any other methods for updating a resource (ex/ making a PUT request instead).

Author decides to add a new method to the `UserResource` that will make the PUT request.

New `User.js` file:

```
angular.module('app').factory('User', function($resource) {
  var UserResource = $resource('/api/users/:id', {_id: "@id"}, {
    update: {method: 'PUT', isArray: false}
  });

  UserResource.prototype.isAdmin = function() {
    return this.roles && this.roles.indexOf('admin') > -1;
  }

  return UserResource;
});
```

Then in `Auth.js`, add the following method:

```
updateCurrentUser: function(newUserData) {
  var dfd = $q.defer();

  var clone = angular.copy(Identity.currentUser);
  angular.extend(clone, newUserData);
  clone.$update().then(function() {
    Identity.currentUser = clone;
    dfd.resolve();
  }, function(response) {
    dfd.reject(response.data.reason);
  });
  return dfd.promise;
}
```

Note that `angular.copy` and `angular.extend` are just helper methods that aren't doing anything Angular specific. You could use a library like lodash instead to do the same things.

`clone.$update()` corresponds to the `update` object in:

```
var UserResource = $resource('/api/users/:id', {_id: "@id"}, {
    update: {method: 'PUT', isArray: false}
  });
```

## Creating the Server-side Code

Be careful when comparing mongoose `_id`s since they are a custom ObjectID type. You need to do `obj1._id.equals(obj2._id)` for a proper comparison. `obj1._id === obj2._id` will not necessarily work because one of the two might be of type String.

# Course List - Sorting, Filtering and Caching

## Caching Data

Author provides an extremely simple example of caching:

```
angular.module('app').factory('CachedCourses', function(Course) {
  var courseList;

  return {
    query: function() {
      if (!courseList) {
        courseList = Course.query();
      }

      return courseList;
    }
  };
});

```

If the course list is empty, query the backend for it. Otherwise, just return the course list.

# Course Details - Selecting and Caching Single Entities

[No notes taken]