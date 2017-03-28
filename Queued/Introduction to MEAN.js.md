# Mean.js Documentation

The site's documentation is actually pretty helpful.

- The overview section links you to resources for learning about MongoDB, Express, Angular and Node
- You can `git clone` or download a zip file of meanjs instead of using `yo meanjs` in the terminal
- Can use `gulp` instead of `grunt` as your task automater
- Can easily be deployed to Cloud Foundry (a platform-as-a-service, probably similar to Heroku)
- Gives troubleshooting instructions for Node and npm
- Sub-generators look amazing! Can generate your own angular modules, routes, controllers, directives, filters and views in the terminal with the right command. There are also sub-generators for Express.
- Provides info on a` mean-seo` npm package you can add to your project (check whether or not it's included by default)

Unfortunately, the rest is up to you to figure out since the documentation is pretty sparse. You probably don't have to worry about much anyway if you make proper use of sub-generators.

Make sure to try out the yeoman package `react-starter-kit`

## Thoughts on Mean.io

The boilerplate app contains an astounding 44,038 files and takes up 305 MB of space. Had a lot of issues with the installation, when I finally got it to work and ran `gulp`, the process hung on `Starting 'watch'...` for several minutes before I could finally browse to `localhost:3000`. The template is almost exactly the same as Mean.js, in fact I would claim it's slightly inferior. You should definitely stick with Mean.js.

# Introduction

## What is the MEAN Stack?

The MEAN stack is a collection of tools (MongoDB, Express, Angular, Node.js), which, when used together, form the acronym MEAN.

The MEAN stack has been gaining a lot of attention in the web community. One of the more compelling reasons developers are moving in this direction has to do with the stack's single technology platform, JavaScript: From the database all the way through to the UI.

MEAN.js is a boilerplate framework

## Why Use a Boilerplate Framework?

Author considers scaffolding a more appropriate adjective. Mean.js gives us the framing and basic plumbing.

Advantages of code generation:
- Consistency
- Fewer bugs
- Best practices
- Support (well documented, good community involvement, code examples)
- Saving time

## Comparison of MEAN Stacks

Mean.js was developed as a fork of Mean.io

Mean.io differences
- More of a framework than a boilerplate
- More CMS like in structure
- Opinionated

There are a lot of Yeoman generators out there
- generator-angular-fullstack
- generator-mean-stack
- generator-meanie

Keep in mind the level of community support when selecting a generator

See [https://npmjs.com/search?q=mean+stack](https://npmjs.com/search?q=mean+stack)
Full listing is at [yeoman.io/generators](yeoman.io/generators)

## Demo Project Preview

Website with free bootstrap themes: [http://www.bootstrapzero.com](http://www.bootstrapzero.com)
Also see [http://www.html5zero.com](http://www.html5zero.com)

# Getting Setup

## Prerequisites

`npm install -g bower`
`npm install -g grunt-cli`
`npm install -g yo`
`npm install -g generator-meanjs`

The project uses node.js version 0.10.36 and the author insists version 12 will give you compatibility issues.
MongoDB: 2.6.7
Mean.js: 0.3.3

Can use gulp instead of grunt if you'd prefer

## Scaffolding a Full-Stack Application

In the terminal, enter `yo meanjs` and go through the options. When the terminal says:

```
Running npm install for you...
This might take a couple minutes.
```

They are not kidding. Very annoying if `npm install` fails at any point. Make sure your Node setup in Windows handles the notorious `node-gyp` error and that `Node` and `npm` are up-to-date.

## Preview of Scaffold Site

Open another terminal and enter
`/C/mongodb/bin/mongod`

In your first terminal, enter
`grunt`

Then navigate to `localhost:3000` in the browser. Should see a nice Mean.js template.

Try to sign up a user, then Articles -> List Articles -> create one

This yeoman generator could be a really good setup for making a blog! The articles are already setup like adding blog posts, would simply need to add markup support and a couple other features.

# Tour the Scaffold Site

## Application Structure (Server-side)

Under the `modules` folder, you'll see 3 subfolders: `articles`, `core` and `users`. Each of these folders has subfolders `client`, `server` and `tests`.

###### articles.server.controller.js

Contains CRUD methods as well as a `list` method to get all the articles, and `articleByID` to get an individual article.

###### article.server.model.js

Defines an Article Mongoose schema

###### articles.server.routes.js

Defines `GET`, `PUT`, `POST` and `DELETE` routes for the articles. The handler for each route is found in the controllers imported by the `require` statements.

###### server views

Under `modules\core\server\views` you will find server-side views: A 404 page, a 500 page, an index view that acts as a shell for loading client-side Angular views, and layout view that acts as a template.

The vast majority of the views in the app are going to be Angular client-side views.

###### server.js

This is the main entrypoint for the application, although its responsibilities have been exported to `config\lib\app.js`. In that file, you load module dependencies (ie `require` statements), setup the mongoose db connection, and initiate Express.

`package.json` `devDependencies` are not included in a production build

## Application Structure (Client-Side)

###### core.client.routes.js

Provides a route to the home page (`/`) and a not-found page (`/not-found`).

###### home.client.view.html

Provides the view for the homepage

###### home.client.controller.js

The homepage's associated controller

###### menus.client.service.js

A core service for doing things like validation, adding a menu, etc

###### core.css

The css used for all views in our application

###### articles.client.config.js

Handles the menus for the articles

###### articles.client.routes.js

Defines all the routes related to articles

###### articles.client.service.js

Defines a factory `Articles`

###### articles.client.module.js

Has one responsibility:
`ApplicationConfiguration.registerModule('articles');`

###### config.js

Of interest is the `registerModule` function, which creates an angular module and adds it to the AngularJS configuration file.

###### init.js

This is the main Angular application file for Mean.js. Its job is to bootstrap the application by attaching the right modules.

## Configuration

Files & folders of interest

```
config\config.js
config\express.js
config\env
```

Note: In `config\env\default.js`, the property `keywords` is used for SEO purposes.

###### config\env\development.js

Here's where you would want to change your MongoDB connection string.

###### config\config.js

In the `validateEnvironmentVariable` function, if `process.env.NODE_ENV` is not set, it will default to development.

`process.env.NODE_ENV = 'development';`

# Code Generation

## Sub-Generators Provided

3 sub-generators provided:
- CRUD Modules
- AngularJS Modules
- Express Modules

The source for each sub-generator can be found on github.

To generate a CRUD module:
`yo meanjs:crud-module <module-name>`
Unfortunately this CRUD module generator isn't provided in Mean.js v4 (makes following the tutorial a pain)

# From Scaffold to Application

## Styling Your Application

[Currently here]