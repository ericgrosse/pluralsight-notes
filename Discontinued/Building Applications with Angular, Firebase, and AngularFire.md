# Introduction

## Welcome

> Firebase is just so well done it makes developing in Angular even easier and slicker than it already is

## Course Repository

[github.com/joeeames/AngularFireCourse](github.com/joeeames/AngularFireCourse)

(All it shows right now is a README that says the course is up to date)

## The Demo App

The demo app is a business expense tracker, and functions with logins.

# Introduction to AngularFire

## Installation

Create a new app in Firebase, and import the `data.json` file provided in the exercise files.

Set up a new project folder and run `npm init -y`

In the `package.json` file, add a `start` script that runs `live-server` (or something equivalent with live reloading)

If you `npm install angularfire`, you actually have to comment out the `module.exports` at the end of the `node_modules/firebase/lib/firebase-web.js` script in order to use it. It's really meant to be used in a module system.

Another option is to install using Bower.

Author shows the approach of copying the relevant folders from `node_modules` into a `vendor` folder. You might as well just download the source files directly off the website, it would take the same amount of time.

Author opts to just use a CDN for this project.

## Creating the App

`home/home.js` shows an example of the new Angular 1.5 component method.

```
angular.module('app').component('home', {
  templateUrl: '/home/home.html'
})
```

You can see this component being referenced in the router in `app.js`:

```
var app = angular.module('app', ['ngRoute', 'firebase']);

app.config(function($routeProvider) {
  $routeProvider
    .when('/home', {
      template: '<home></home>'
    })
    .otherwise('/home')
})
```

## Dealing with Refs

Create a new file `fbUrl.js` which is responsible for handling your root firebase url.

```
angular.module('app').constant('FirebaseUrl', 'http://je-expense-tracker.firebaseio.com')
  .service('rootRef', ['FirebaseUrl', Firebase]);
```

Make sure to change the firebase url from the course's url to the one you're using.

# Authentication

## Introduction

In the Firebase dashboard, go into `Login & Auth`. Click on the `Anonymous` tab and enable the checkbox `Enable Anonymous User Authentication`. Go to the Facebook tab and do the same, although you actually have to create a Facebook application and get an app ID and a secret key.

The following facebook link provides step-by-step instructions for this: [https://developers.facebook.com/docs/apps/register](https://developers.facebook.com/docs/apps/register)

## Anonymous Authentication

`security/login.html` includes the following:

```
<div class="alert alert-danger" ng-show="!!$ctrl.errorMessage">{{errorMessage}}</div>
```

The `!!` is to coerce `errorMessage` into a boolean. I find it amusing that this has to be done.

As for the use of `$ctrl`:

> In previous versions when people used `controller as`, oftentimes they would use something else. It was very common to use `vm` as the name of your controller, but in Angular 1.5, the default name is `$ctrl`.

[Currently here]