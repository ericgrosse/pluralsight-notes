# Intro

## Innovations in React and Flux

- JSX
- Virtual DOM
- Isomorphic Rendering
- Unidirectional Flows

## Node.JS Overview

###### CommonJS Pattern

```
// 1. Get reference to dependency
var dependency = require('path/to/file');
// 2. Declare module
var MyModule = {
	// code here
};
// 3. Expose to others
module.exports = MyModule;
```

## Browserify Overview

Browserify
- Uses Node modules in the browser
- Bundles dependencies into a single file (minimizes http requests required to load your application)

## Gulp Overview

Gulp is a popular JavaScript based task runner (note that Grunt is a popular competitor). Gulp has recently gained a lot of popularity because it is easy to configure and very fast. Gulp has a rich plugin system that allows us to easily automate a lot of important tasks like minification, linting, bundling, moving files, running tests and watching files so your browser can automatically reload when you save your changes.

Supposedly Gulp is a lot faster than Grunt because of its stream-based approach.

## Versions

The tutorial uses the following versions:
- React 0.13.3
- React-Router 0.13.3
- Flux 2.0.3

## What About Testing

The tutorial doesn't cover automated tests.

Facebook created their own testing framework called Jest, which they use internally to test React components. Jest is just a wrapper over the Jasmine testing framework. It offers a really low friction way to test React applications.

## What About ES2015

ES2015 is also known as ES6 (or ECMAScript6)
ES5 has been around since 2009, whereas ES6 was only approved in June 2015

# Environment Setup

## Introduction

###### Our Goal
Just type "gulp", get all this...
- Compile React JSX
- Lint JSX and JS via ESLint
- Bundle JS and CSS files
- Migrate the built app to the dist folder
- Run a dev webserver
- Open the browser at your dev URL
- Relaod the browser upon save

## Gulp Install

By convention, we put all of our source code in the `src` folder and all of our associated generated files to the `dist` folder.

## Gulp Configuration

Create a file `gulpfile.js` with the following contents:

```
"use strict";

var gulp = require('gulp');
var connect = require('gulp-connect'); //Runs a local dev server
var open = require('gulp-open'); //Open a URL in a web browser
var browserify = require('browserify'); // Bundles JS
var reactify = require('reactify');  // Transforms React JSX to JS
var source = require('vinyl-source-stream'); // Use conventional text streams with Gulp
var concat = require('gulp-concat'); //Concatenates files
var lint = require('gulp-eslint'); //Lint JS files, including JSX

var config = {
	port: 9005,
	devBaseUrl: 'http://localhost',
	paths: {
		html: './src/*.html',
		js: './src/**/*.js',
		css: [
      		'node_modules/bootstrap/dist/css/bootstrap.min.css',
      		'node_modules/bootstrap/dist/css/bootstrap-theme.min.css'
    	],
		dist: './dist',
		mainJs: './src/main.js'
	}
}

//Start a local development server
gulp.task('connect', function() {
	connect.server({
		root: ['dist'],
		port: config.port,
		base: config.devBaseUrl,
		livereload: true
	});
});

gulp.task('open', ['connect'], function() {
	gulp.src('dist/index.html')
		.pipe(open({ uri: config.devBaseUrl + ':' + config.port + '/'}));
});

gulp.task('html', function() {
	gulp.src(config.paths.html)
		.pipe(gulp.dest(config.paths.dist))
		.pipe(connect.reload());
});

gulp.task('js', function() {
	browserify(config.paths.mainJs)
		.transform(reactify)
		.bundle()
		.on('error', console.error.bind(console))
		.pipe(source('bundle.js'))
		.pipe(gulp.dest(config.paths.dist + '/scripts'))
		.pipe(connect.reload());
});

gulp.task('css', function() {
	gulp.src(config.paths.css)
		.pipe(concat('bundle.css'))
		.pipe(gulp.dest(config.paths.dist + '/css'));
});

gulp.task('lint', function() {
	return gulp.src(config.paths.js)
		.pipe(lint({config: 'eslint.config.json'}))
		.pipe(lint.format());
});

gulp.task('watch', function() {
	gulp.watch(config.paths.html, ['html']);
	gulp.watch(config.paths.js, ['js', 'lint']);
});

gulp.task('default', ['html', 'js', 'css', 'lint', 'open', 'watch']);
```

The basic pattern is to write `gulp.task(name, dependencies, callback)`, where the dependencies are run before the current task. The default task is what is executed when simply typing `gulp` into the terminal.

Gulp also heavily uses the concept of piping
`<output>.pipe(<input>)`
The output of one pipe becomes the input of the next pipe, and so on. This is very similar to jQuery chaining
ex/ `$('#element').text('hello').addClass('myClass').fadeIn();`

I was amazed at how incredibly fast the browser autoreload executes. It was basically instantaneous!

## Browserify Configuration

`npm install --save browserify@11.0.1 reactify@1.1.1 vinyl-source-stream@1.1.0`

In `index.html`, add a reference to `bundle.js`.
`<script src="scripts/bundle.js"></script>`

## Bootstrap Install

`npm install --save bootstrap@3.3.5 jquery@2.1.4 gulp-concat@2.6.0`

In `main.js`, write
`$ = jQuery = require('jquery')`
This is because Bootstrap expects jQuery to be in the global namespace

In `index.html`, add a reference to `bundle.css`
`<link rel="stylesheet" href="css/bundle.css" />`

## ESLint Configuration

`npm install --save gulp-eslint@0.15.0`

Create an `eslint.config.json` file in the project's root directory and get the contents for this file from [here](github.com/coryhouse/react-flux-starter-kit/blob/master/eslint.config.json)

The author's setup has a watch on JavaScript files that calls both the `js` and `lint` task for any JS changes.

## React, React Router and Flux Install

`npm install --save react@0.13.3 react-router@0.13.3 flux@2.0.3`

## Summary

If we ever want to update our dependencies to the latest version, use `npm update`

# React Core Concepts

## Two-way Binding Risks

Two-way data binding can be unpredictable - One UI change would trigger a corresponding model change, which would trigger another UI and corresponding model change, and so on (cascading updates).

## JSX Introduction

- "HTML" in JavaScript
- Differences: className, htmlFor
- Compiles to JavaScript
- Optional

###### JSX Compiles to JS

`<h1 color="red">Heading here</h1>`
becomes
`React.createElement('h1', {color: 'red}. 'heading here')`

## HTML in JS: A Justification

Front-end frameworks like Angular and ember put "JS" in HTML, so they're doing the exact opposite of JSX.

Angular
`<div ng-repeat="user in users">`
React equivalent
`users.map(createUserRow)}`

Instead of trying to enhance HTML to support simple logic, data binding and semantics, why not use JavaScript to define our markup? This has some clear advantages since JavaScript is a far more powerful technology than HTML.

When you make a typo in HTML, things just silently fail.
In React, JSX tells you what line the error occurred.

Putting HTML and JS in separate files is a separation of technologies, not concerns.

## JSX Friendly Editors

- Sublime
- WebStorm
- Brackets
- Atom Editor
- Visual Studio 2015

## Virtual DOM

Analogy - Imagine having two movie setups with a certain setup, and you make a change to one of the sets. It is far easier to compare the two sets and make a minor change than it is to tear down the other set and recreate it from scratch.

Updating the DOM is expensive

- Without Virtual DOM: Blindly update DOM using new state
- With Virtual DOM: Compare DOM's current state to desired new state and update the DOM in the most efficient way

Being efficient is increasingly important in a world where more people are using mobile devices. Mobile devices vary wildly in their CPU power and conserving battery life is a concern as well.

# React Lifecycle

## Props and State

Props - Look like HTML attributes, but **immutable**
`this.props.username`

State - Holds mutable state
`this.state.username`

Since state is mutable, you should strive to only utilize state on your controller-views. In other words, only use state on the top-level component and pass data down to your child components via `props`.

`getInitialState` should only typically be used in your top-level component as well.

Can use `getDefaultProps` to pass default prop values for any props that were not passed down.

## Lifecycle Methods

###### componentWillMount
++When++
Before initial render, both client and server

++Why++
Good spot to set initial state

###### componentDidMount
++When++
After render

++Why++
Access DOM, integrate with frameworks, set timers, AJAX requests

###### componentWillReceiveProps

++When++
When receiving new props. Not called on initial render.

++Why++
Set state before a render

###### shouldComponentUpdate

++When++
Before render when new props or state are being received. Not called on initial render.

++Why++
Performance. Return false to void unnecessary re-renders. This is the case when a prop changes but you don't want to re-render the DOM.

###### componentWillUpdate

++When++
Immediately before rendering when new props or state are being received. Not called on initial render.

++Why++
Prepare for an update. Note that you cannot call `setState` in this function.

###### componentDidUpdate

++When++
After component's updates are flushed to the DOM. Not called for the initial render.

++Why++
Work with the DOM after an update

###### componentWillUnmount

++When++
Immediately before component is removed from the DOM

++Why++
Cleanup

## Mock API Setup

`npm install --save lodash`

Have a close look at `authorAPI.js` and `authorData.js`

# React Composition

## Controller Views

It's recommended to have a single top level controller-view that interacts with the store and passes all necessary information down to the children (through props).

It's possible to nest your controller-views, but it's not recommended to do so since it can cause multiple data updates and it can cause React's render method to be called multiple times.

## Prop Validation

```
propTypes: {
	author: React.PropTypes.object.isRequired,
    onSave: React.PropTypes.func.isRequires,
    errors: React.PropTypes.object,
    ...
}
```

Note that prop validation isn't run in the production (minified) version of React

# React Router

## Route Configuration

Make a file `routes.js` in the root folder of the project

```
"use strict";

var React = require('react');

var Router = require('react-router');
var DefaultRoute = Router.DefaultRoute;
var Route = Router.Route;
var NotFoundRoute = Router.NotFoundRoute;
var Redirect = Router.Redirect;

var routes = (
  <Route name="app" path="/" handler={require('./components/app')}>
    <DefaultRoute handler={require('./components/homePage')} />
    <Route name="authors" handler={require('./components/authors/authorPage')} />
    <Route name="about" handler={require('./components/about/aboutPage')} />
    <NotFoundRoute handler={require('./components/notFoundPage')} />
    <Redirect from="about-us" to="about" />
    <Redirect from="awthurs" to="authors" />
    <Redirect from="about/*" to="about" />
  </Route>
);

module.exports = routes;
```

Everything is pretty self-explanatory. You can add an optional path parameter to any route to make it differ from the name parameter.

Refactor `app.js` as follows

```
/*eslint-disable strict */ //Disabling check because we can't run strict mode. Need global vars.

var React = require('react');
var Header = require('./common/header');
var RouteHandler = require('react-router').RouteHandler;
$ = jQuery = require('jquery');

var App = React.createClass({
	render: function() {
		return (
			<div>
				<Header/>
				<div className="container-fluid">
					<RouteHandler/>
				</div>
			</div>
		);
	}
});

module.exports = App;
```

And refactor `main.js` as follows

```
"use strict";

var React = require('react');
var Router = require('react-router');
var routes = require('./routes');

Router.run(routes, function(Handler) {
	React.render(<Handler/>, document.getElementById('app'));
});

```

## Params and Querystrings

Given a route like this
`<route path="/course/:courseId" handler={Course} />`

And a URL like this
`/course/clean-code?module=3`

The component's props will be populated
```
var Course = React.createClass({
	render: function() {
    	this.props.params.courseId; // 'clean-code'
        this.props.query.module; // '3'
        this.props.path; // '/course/clean-code/?module=3'
    }
});
```

## Links

In `header.js`, we have a lot of hardcoded links

```
<ul className="nav navbar-nav">
    <li><a href="/">Home</a></li>
    <li><a href="/#authors">Authors</a></li>
    <li><a href="/#about">About</a></li>
</ul>
```

If you were ever to change your routs, your hardcoded links would get invalidated, and changing them all in a big application would be a maintainability nightmare.

Instead, use the `Link` component that comes with react-router.

```
<ul className="nav navbar-nav">
    <li><Link to="app">Home</Link></li>
    <li><Link to="authors">Authors</Link></li>
    <li><Link to="about">About</Link></li>
</ul>
```

## Transitions

###### Handling Transitions

```
var Settings = React.createClass({
    statics: {
        willTransitionTo: function(transition, params, query, callback) {
            if(!isLoggedIn) {
                transition.abort();
                callback();
            }
        },
        willTransitionFrom: function(transition, component) {
            if(component.formHasUnsavedData()) {
                if(!confirm('Are you sure you want to leave without saving?')) {
                    transition.abort();
                }
            }
        }
    }
    // ...
});
```

`willTransitionTo` and `willTransitionFrom` are special functions in react (similar to `getInitialState` and `render`)

## Hash vs History URLS

| HashLocation | History Location |
|--------|--------|
| yourUrl.com/#/courses | yourUrl.com/courses |
| Ugly URLs | Clean URLs |
| Works in all browsers | IE10+ |
| Not compatible with server-render | Works for server-render |

Removing the hash in the URL is incredibly simple

In `main.js`, add a new 2nd parameter to `Router.run` called `Router.HistroyLocation`

```
"use strict";

var React = require('react');
var Router = require('react-router');
var routes = require('./routes');

Router.run(routes, Router.HistoryLocation, function(Handler) {
	React.render(<Handler/>, document.getElementById('app'));
});
```

# React Forms

## Creating Reusable Inputs

Any portion of JSX code that is repeated more than once can be refactored into its own React component. Ex/

```
return (
	<form>
		<h1>Manage Author</h1>

		<label htmlFor="firstName">First Name</label>
		<input type="text"
			name="firstName"
			className="form-control"
			placeholder="First Name"
			ref="firstName"
			value={this.props.author.firstName} 
			/>
		<br/>
		
		<label htmlFor="firstName">First Name</label>
		<input type="text"
			name="firstName"
			className="form-control"
			placeholder="First Name"
			ref="firstName"
			value={this.props.author.firstName} 
			/>
		<br/>
		
		<input type="submit" value="Save" className="btn btn-default">
	</form>
);
```

Would be reduced to

```
return (
	<form>
		<h1>Manage Author</h1>

		<Input
			name="firstName"
			label="First Name"
			value={this.props.author.firstName}
			onChange={this.props.onChange} />

		<Input
			name="lastName"
			label="Last Name"
			value={this.props.author.lastName}
			onChange={this.props.onChange} />
		
		<input type="submit" value="Save" className="btn btn-default">
	</form>
);
```

## Notifications

`npm install --save toastr@2.1.0`

In `gulpfile.js` need to add `node_modules/toastr/toastr.css` to the css array in `paths`

The toastr API is incredibly simple to use
`toastr.success('Author saved')`

## Validation

Validation is used in `manageAuthorPage.js`

```
authorFormIsValid: function() {
    var formIsValid = true;
    this.state.errors = {}; //clear any previous errors.

    if (this.state.author.firstName.length < 3) {
        this.state.errors.firstName = 'First name must be at least 3 characters.';
        formIsValid = false;
    }

    if (this.state.author.lastName.length < 3) {
        this.state.errors.lastName = 'Last name must be at least 3 characters.';
        formIsValid = false;
    }

    this.setState({errors: this.state.errors});
    return formIsValid;
},

saveAuthor: function(event) {
    event.preventDefault();

    if (!this.authorFormIsValid()) {
        return;
    }

    AuthorApi.saveAuthor(this.state.author);
    this.setState({dirty: false});
    toastr.success('Author saved.');
    this.transitionTo('authors');
}
```

Notice that the `errors` object is part of the page's state. It is then passed into the page's render method as a prop

```
render: function() {
    return (
        <AuthorForm
            author={this.state.author}
            onChange={this.setAuthorState}
            onSave={this.saveAuthor}
            errors={this.state.errors} />
    );
}
```

What's interesting is that `authorForm.js` then forwards the error to its child

```
<Input
    name="firstName"
    label="First Name"
    value={this.props.author.firstName}
    onChange={this.props.onChange}
    error={this.props.errors.firstName} />

<Input
    name="lastName"
    label="Last Name"
    value={this.props.author.lastName}
    onChange={this.props.onChange}
    error={this.props.errors.lastName} />
```

Then in `textInput.js`, the error is finally displayed
`<div className="input">{this.props.error}</div>`

On another note, if you look back at the render method of `manageAuthorPage.js`, it passes either state variables or functions into its props.

```
render: function() {
    return (
        <AuthorForm
            author={this.state.author}
            onChange={this.setAuthorState}
            onSave={this.saveAuthor}
            errors={this.state.errors} />
    );
}
```

The child component `AuthorForm` has callbacks to functions defined on the parent, namely `setAuthorState` and `saveAuthor`

## PropTypes

propTypes can be used to simultaneously declare the expected type of the prop and whether or not it's required

```
propTypes: {
	author: React.PropTypes.object.isRequired,
    onSave: Rect.PropTypes.func.isRequired,
    onChange: React.PropTypes.func.isRequired,
    errors: React.PropTypes.object
}
```

## Transitions

When a user begins filling out a form and attempts to navigate to another page, we would like to ask the user to confirm that they would like to leave without saving. This can be done as follows:

```
statics: {
    willTransitionFrom: function(transition, component) {
        if (component.state.dirty && !confirm('Leave without saving?')) {
            transition.abort();
        }
    }
},

getInitialState: function() {
    return {
        author: { id: '', firstName: '', lastName: '' },
        errors: {},
        dirty: false
    };
}
```

In the `onChange` handler for the form, add

`this.setState({dirty: true});`

as the first line. Also need to set the state as clean in the `saveAuthor` method.

## Form Hydration via URL

The author does something interesting in his routing

```
<Route name="addAuthor" path="author" handler={require('./components/authors/manageAuthorPage')} />
<Route name="manageAuthor" path="author/:id" handler={require('./components/authors/manageAuthorPage')} />
```

You have two routs with different names and urls but that map to the same source file. This is clever reusability (same template to add and edit authors).

In `manageAuthorPage.js`, add a `componentWillMount` method that fetches the author's ID from the URL params and sets the state of the authorby calling the AuthorAPI.

```
componentWillMount: function() {
    var authorId = this.props.params.id; //from the path '/author:id'

    if (authorId) {
        this.setState({author: AuthorApi.getAuthorById(authorId) });
    }
}
```

`componentWillMount` is ideal for this procedure since `setState` won't trigger a re-render.

# Flux

## Intro

- Flux is not a framework, it is a pattern for unidirectional data flows.
- All updates to your application state occur via a centralized dispatcher. This dispatcher dispatches data to your data stores.
- There are a lot of Flux implementations out there: Facebook's Flux, Alt, Reflux, Flummox, etc

## Three Core Flux Concepts

- Action (Delete User)
- Dispatcher (Notify everyone who cares)
- Store (Hold app state)
- React View (reacts to changes in the Store)

## Stores

Stores use Node's EventEmitter

Every store has these common traits (aka interface)
1. Extend EventEmitter
2. addChangeListener and removeChangeListener
3. emitChange

## Flux API

- `register(function callback)` - "Hey dispatcher, run me when actions happen. - Store"
- `unregister(string id)` - "Hey dispatcher, stop worrying about this action. - Store"
- `waitFor(array<string> ids)` - "Update this store first. - Store"
- `dispatch(object payload)` - "Hey dispatcher, tell the stores about this action. - Action"
- `isDispatching()` - "Im busy dispatching callbacks right now."

# Flux Demos

## Dispatcher

Create a folder `dispatcher` containing a file `appDispatcher.js` and write the following

```
var Dispatcher = require('flux').Dispatcher;
module.exports = new Dispatcher();
```

## Actions

Create a folder `actions` containing a file `authorActions.js` with the following content

```
"use strict";

var Dispatcher = require('../dispatcher/appDispatcher');
var AuthorApi = require('../api/authorApi');
var ActionTypes = require('../constants/actionTypes');

var AuthorActions = {
	createAuthor: function(author) {
		var newAuthor = AuthorApi.saveAuthor(author);

		//Hey dispatcher, go tell all the stores that an author was just created.
		Dispatcher.dispatch({
			actionType: ActionTypes.CREATE_AUTHOR,
			author: newAuthor
		});
	},

	updateAuthor: function(author) {
		var updatedAuthor = AuthorApi.saveAuthor(author);

		Dispatcher.dispatch({
			actionType: ActionTypes.UPDATE_AUTHOR,
			author: updatedAuthor
		});
	},

	deleteAuthor: function(id) {
		AuthorApi.deleteAuthor(id);

		Dispatcher.dispatch({
			actionType: ActionTypes.DELETE_AUTHOR,
			id: id
		});
	}
};

module.exports = AuthorActions;
```

This relies on using a separate file `actionTypes.js` for constants (similar to Java enums).

```
"use strict";

var keyMirror = require('react/lib/keyMirror');

module.exports = keyMirror({
	INITIALIZE: null,
	CREATE_AUTHOR: null,
	UPDATE_AUTHOR: null,
	DELETE_AUTHOR: null
});
```

The `keyMirror` library sets the value equal to its key.

## Stores: Change Listeners

`npm install --save object-assign`

Create a folder `store` containing `authorStore.js` with the following contents

```
"use strict";

var Dispatcher = require('../dispatcher/appDispatcher');
var ActionTypes = require('../constants/actionTypes');
var EventEmitter = require('events').EventEmitter;
var assign = require('object-assign');
var _ = require('lodash');
var CHANGE_EVENT = 'change';

var _authors = [];

var AuthorStore = assign({}, EventEmitter.prototype, {
	addChangeListener: function(callback) {
		this.on(CHANGE_EVENT, callback);
	},

	removeChangeListener: function(callback) {
		this.removeListener(CHANGE_EVENT, callback);
	},

	emitChange: function() {
		this.emit(CHANGE_EVENT);
	},

	getAllAuthors: function() {
		return _authors;
	},

	getAuthorById: function(id) {
		return _.find(_authors, {id: id});
	}
});

Dispatcher.register(function(action) {
	switch(action.actionType) {
		case ActionTypes.INITIALIZE:
			_authors = action.initialData.authors;
			AuthorStore.emitChange();
			break;
		case ActionTypes.CREATE_AUTHOR:
			_authors.push(action.author);
			AuthorStore.emitChange();
			break;
		case ActionTypes.UPDATE_AUTHOR:
			var existingAuthor = _.find(_authors, {id: action.author.id});
			var existingAuthorIndex = _.indexOf(_authors, existingAuthor); 
			_authors.splice(existingAuthorIndex, 1, action.author);
			AuthorStore.emitChange();
			break;	
		case ActionTypes.DELETE_AUTHOR:
			_.remove(_authors, function(author) {
				return action.id === author.id;
			});
			AuthorStore.emitChange();
			break;
		default:
			// no op
	}
});

module.exports = AuthorStore;
```

`var AuthorStore` is combining a base object `{}`, `EventEmitter.prototype` and an object containing `addChangeListener`, `removeChangeListener`, etc as properties.

The variables prefixed with `_` are meant to be private (not included in  `module.exports`)

## Stores: Private Storage

In `authorAction.js`, any `Dispatcher.dispatch` event is caught by `Dispatcher.register` as the `action` parameter in the callback.

```
Dispatcher.dispatch({
    actionType: ActionTypes.CREATE_AUTHOR,
    author: newAuthor
});
```

```
Dispatcher.register(function(action) {
	switch(action.actionType) {
		case ActionTypes.INITIALIZE:
			_authors = action.initialData.authors;
			AuthorStore.emitChange();
			break;
    }
});
```

`AuthorStore.emitChange();` will notify any React component that has registered with this store

`getAllAuthors` method used to get our private `_authors` data

`getAuthorById` uses lodash to search through the array of objects `authors` and find the object matching the `id` passed in. I'd imagine `_.find` is a method worth using on a regular basis.

On a separate note, lodash is an improvement over underscore, see [here](http://stackoverflow.com/questions/13789618/differences-between-lodash-and-underscore) for details.

## Stores: Interactions

###### manageAuthorPage.js

`manageAuthorPage.js` gets refactored by replacing

```
var AuthorAPI = require('../../api/authorAPI');
```

with

```
var AuthorActions = require('../../actions/authorActions'); 
var AuthorStore = require('../../actions/authorStore'); 
```

and in `componentWillMount`, replace

```
{author: AuthorAPI.getAuthorById(authorId)}
```

by

```
{author: AuthorStore.getAuthorById(authorId)}
```

In addition, in `saveAuthor`, change

```
AuthorApi.saveAuthor(this.state.author);
```

to

```
AuthorActions.createAuthor(this.state.author);
```

`manageAuthorPage` now uses Flux instead of calling the API directly

###### authorPage.js

In `getInitialState`, `authors` should be set to `AuthorStore.getAllAuthors()` instead of `[]`. Because of this change, no longer need `componentDidMount`.

## Stores: Initialization

Create a file `initializeActions.js` in the `actions` folder

```
"use strict";

var Dispatcher = require('../dispatcher/appDispatcher');
var ActionTypes = require('../constants/actionTypes');
var AuthorApi = require('../api/authorApi');

var InitializeActions = {
	initApp: function() {
		Dispatcher.dispatch({
			actionType: ActionTypes.INITIALIZE,
			initialData: {
				authors: AuthorApi.getAllAuthors()
			}
		});
	}
};

module.exports = InitializeActions;
```

Then in `main.js`, add

```
var InitializeActions = require('./actions/initializeActions');

InitializeActions.initApp();
```

In `authorStore.js`'s `Dispatcher.register`, need to add a case `ActionTypes.INITIALIZE`

## Update Author Flow with Flux

In `manageAuthorPage.js` in the `saveAuthor` method, you can differentiate between updating or creating an author as follows

```
if (this.state.author.id) {
    AuthorActions.updateAuthor(this.state.author);
} else {
    AuthorActions.createAuthor(this.state.author);
}
```

## Adding Store Listeners

In `authorPage.js`, add the following

```
componentWillMount: function() {
    AuthorStore.addChangeListener(this._onChange);
},

//Clean up when this component is unmounted
componentWillUnmount: function() {
    AuthorStore.removeChangeListener(this._onChange);
},

_onChange: function() {
    this.setState({ authors: AuthorStore.getAllAuthors() });
}
```

In this setup, any change to `AuthorStore` triggers the `_onChange` method, and since we're setting state inside of `onChange`, this will make the UI re-render. This basically serves to keep the UI in sync with the store.

## Summary: Stepping Through Flux

Place `debugger` statements in the following places to follow the entire sequence of events for a `deleteAuthor` command.

- authorList.js deleteAuthor
- authorActions.js deleteAuthor
- authorStore.js case ActionTypes.DELETE_AUTHOR
- authorPage.js _onChange