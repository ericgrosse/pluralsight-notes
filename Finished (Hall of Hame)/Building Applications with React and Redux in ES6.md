# Intro

## Why Redux?

- Centralizes all your application state into one store
- Reduced boilerplate
- Isomorphic / Universal friendly
- Immutable store
- Hot reloading
- Time-travel debugging
- Small

Trade-off:

What would you like to complain about?
- Too much magic
- Too much boilerplate

# Environment Setup

## Intro

The goal of the module is to reduce JavaScript fatigue. The number of options in the JavaScript space are overwhelming, especially in the React community.

> The longer that I've worked in React, the more overwhelmed I've felt about all the things I need to keep track of in order to build a solid development environment from the ground up.

Dev environment goals:
- Automated testing
- Linting
- Minification
- Bundling
- JSX compilation
- ES6 transpilation
- All in one command

## Hot Reloading

There are currently multiple ways to handle hot reloading in React and Redux applications. This course chooses to use `babel-preset-react-hmre`. This is a babel preset that wraps up a number of other libraries and settings in a single preset that's pretty easy to set up. It works by wrapping your components in a custom proxy using babel. The proxies are classes that act just like your classes, but they provide hooks for injecting new implementations.

Hot reload warnings:
- Experimental
- Doesn't reload functional components
- Doesn't reload container functions like `mapStateToProps`
- Other options exist

## Install Node

The `6.x` branch loads modules 4x faster than the `4.x` version.

A few people on Windows have reported errors running Node 6. If you get errors later in this module, install Node 5 instead.

## Create `package.json`

Use the `package.json` file from  [https://github.com/coryhouse/pluralsight-redux-starter](https://github.com/coryhouse/pluralsight-redux-starter)

## Editors

Author claims that Webstorm has the best ES6 support
Author a large fan of Webstorm's built-in editor (`Alt + F12`)

Author also recommends using Atom with the `react` and `terminal-plus` plugins installed.

## Introduce `npm` Scripts

Why `npm` scripts?
- Easy to learn (arguably even easier than `gulp`)
- Simple
- No extra layer of abstraction
- No dependence on separate plugins
- Simpler debugging
- Better documentation

## Set Up `editorconfig`

An `.editorconfig` file ensures consistent editor styling (important for multi-person projects)

## Set Up Babel

The `.babelrc` file is as follows:

```
{
  "presets": ["react", "es2015"],
  "env": {
    "development": {
      "presets": ["react-hmre"]
    }
  }
}
```

## Create Start Script

In the `package.json` file, add a new script:

```
"start": "babel-node tools/srcServer.js"
```

Note the use of `babel-node` instead of `node`. This is to transpile our Node ES6 code into ES5.

## Create Start Message

Add a `prestart` script in the `package.json` file:

```
"prestart": "babel-node tools/startMessage.js"
```

Now when you run `npm start`, this script will run before the `start` script.

# React Component Approaches

## Four Ways to Create React Components

- ES5 createClass
- ES6 class
- ES5 stateless function
- ES6 stateless function
- Many more...

## ES6 Class Component

The main difference is no autobinding

```
// Works fine with ES5 createClass
<div onClick={this.handleClick}></div>

// Requires explicit bind with ES6 Class
<div onClick={this.handleClick.bind(this)}></div>
```

Author recommends doing the following instead (for performance reasons):

```
class Contacts extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
}
```

Other differences
- `PropTypes` declared separately
- Default props declared separately
- Set initial state in constructor

## ES5 Stateless Component

```
var HelloWorld = function(props) {
  return (
    <h1>Hello World</h1>
  );
};
```

## ES6 Stateless Component

```
const HelloWorld = (props) => {
  return (
    <h1>Hello World</h1>
  );
};
```

9 benefits to stateless functional components:
- No class needed
- Avoid `this` keyword
- Enforces best practice
- High signal-to-noise ratio
- Enhanced code completion / intellisense
- Bloated components are obvious
- Easy to understand
- Easy to test
- Improved performance (future releases only)

## When Should I Use Each Style?

Use stateless functional components when possible

Use class components if you need:
- State
- Refs
- Lifecycle methods
- Child functions (only a performance concern)

## Container vs Presentation Components

Most of your components should be presentation components

Comparison:

| Container | Presentation |
|--------|--------|
| Little to no markup | Nearly all markup |
| Pass data and actions down | Receive data and actions via props |
| Knows about Redux | Doesn't know about Redux |
| Often stateful | Typically functional components |

> When you notice that some components don't use props they receive but merely forward them down... It's a good time to introduce some container components - Dan Abramov

Alternative jargon for container vs presentation:
- Smart vs Dumb
- Stateful vs Stateless
- Controller View vs View

# Initial App Structure

## Create Initial Components

Here is `AboutPage.js`:

```
import React from 'react';

class AboutPage extends React.Component {
  render() {
    return (
      <div>
        <h1>About</h1>
        <p>This application uses React, Redux, React Router and a variety of other helpful libraries.</p>
      </div>
    );
  }
}

export default AboutPage;
```

Even though we could make this a stateless component, it's better to use a class. This has to do with current limitations in hot reloading. In particular, stateless functions are not hot reloaded unless they have some parent that is a class.

Take-away: All top-level page components should be classes (at least until the hot reloading limitation is addressed).

## Create App Layout

The `index.js` file becomes:

```
import 'babel-polyfill';
import React from 'react';
import { render } from 'react-dom';
import { Router, browserHistory } from 'react-router';
import routes from './routes';
import './styles/styles.css';
import '../node_modules/bootstrap/dist/css/bootstrap.min.css';

render(
  <Router history={browserHistory} routes={routes} />,
  document.getElementById('app')
);
```

Note that `babel-polyfill` is rather large (`50kb`) so you might want to use a polyfill for only certain features (author mentions `Object.assign`).

## Create Header

The top-level `index.html` file isn't part of our hot reloading life cycle, which means you need to refresh on any change to this file.

# Intro to Redux

## Three Core Redux Principles

- One immutable store
- Action trigger changes
- Reducers update state

## Flux Differences

There is no dispatcher in Redux

# Actions, Stores, and Reducers

## Actions

Action creator example:

```
rateCourse(rating) {
  return { type: RATE_COURSE, rating: rating }
}
```

Note the matching of the function name with the type (this isn't necessary, just a convention)

## Store

```
let store = createStore(reducer);

store.dispatch(action)
store.subscribe(listener)
store.getState()
replaceReducer(nextReducer)
```

## What Is Immutability?

To change state, return a new object

What's mutable in JS?
- Objects
- Arrays
- Functions

The following are already immutable:
- Number
- String
- Boolean
- Undefined
- Null

There are several approaches to creating a copy of an object. Author recommends using `Object.assign`:

```
// Signature
Object.assign(target, ...sources)

// Example
Object.assign({}, state, {role: 'admin'})
```

The above example creates a deep copy of the `state` object, but with the `role` property changed to the value `admin`.

## Why Immutability?

Immutability = clarity. You know that all state changes occur in your reducers.

Immutability also improves performance.

By far the most noteworthy benefit is amazing debugging through time travelling.

## Handling Immutability

ES6
- Object.assign
- Spread operator

ES5
- Loadash merge
- Lodash extend
- Object-assign

Libraries
- react-addons-update
- Immutable.js

***

How do I enforce immutability?
- Trust (can work reasonably well for small teams)
- `redux-immutable-state-invariant`
- `Immutable.js`

## Reducers

**All** reducers are called on each dispatch. The switch statement inside of each reducer looks at the action type to determine if it has anything to do. This is why it's important that all reducers return untouched state as the default (no switch case matched).

# Connecting React to Redux

## React-redux Introduction

Provider: Attaches app to store
Connect: Creates container components

The Provider component attaches your application to the Redux store, so you use the Provider component to wrap your application's top-level component.

```
<Provider store={this.props.store}>
  <App/>
</Provider>
```

Connect wraps our component so it's connected to the Redux store.

```
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(AuthorPage);
```

In Flux, you would have to do the following instead:

```
componentWillMount() {
  AuthorStore.addChangeListener(this.onChange);
}

componentWillUnmount() {
  AuthorStore.removeChangeListener(this.onChange);
}

onChange() {
  this.setState({ authors: AuthorStore.getAll() });
}
```

Clearly this is a lot more boilerplate code.

Benefits to the Redux approach:
- No manual unsubscribe
- No lifecycle methods required
- Declare what subset of state you want
- Enhanced performance for free

## `mapStateToProps`

```
connect(mapStateToProps, mapDispatchToProps)
```

`mapStateoProps` determines what state should be exposed as props.

Example:

```
function mapStateToProps(state) {
  return {
    appState: state
  };
}
```

Then in your component, you can access state through `this.props.appstate`.

***

Consider using the Reselect library for the performance benefits of memoization (basically caching for function calls).

## `mapDispatchToProps`

This function determines which actions we want to expose to our component (instead of which state).

Example:

```
function mapDispatchToProps(dispatch) {
  return {
    actions: bindActionCreators(actions, dispatch)
  };
}
```

`bindActionCreators` is a Redux specific function.

# Redux Flow

## Binding in ES6

Get in the habit of doing your binding within your component's constructor.

Example:

```
class CoursesPage extends React.Component {
  constructor(props, context) {
    super(props, context);

    this.state = {
      course: { title: '' }
    };

    this.onTitleChange = this.onTitleChange.bind(this);
    this.onClickSave = this.onClickSave.bind(this);
  }

  onTitleChange(event) {
    const course = this.state.course;
    course.title = event.target.value;
    this.setState({course: course});
  }

  onClickSave() {
    alert(`Saving ${this.state.course.title}`);
  }

  render() { ... }
}
```

## Reducers

Create a `reducers` folder containing the file `courseReducer.js`:

```
export default function courseReducer(state = [], action) {
  switch (action.type) {
    case 'CREATE_COURSE':
      return [...state,
        Object.assign({}, action.course)
      ];

    default:
      return state;
  }
}
```

## Root Reducer

Create an `index.js` file inside the `reducers` folder. This corresponds to our root reducer.

```
import {combineReducers} from 'redux';
import courses from './courseReducer';

const rootReducer = combineReducers({
  courses
});

export default rootReducer;
```

Note that we are aliasing `courseReducer` as `courses`, since it is preferable to call `state.courses(...)` than `state.courseReducer(...)` in our container components.

## Store

Create a folder `store` with file `configureStore.js` containing the following:

```
import {createStore, applyMiddleware} from 'redux';
import rootReducer from '../reducers';
import reduxImmutableStateInvariant from 'redux-immutable-state-invariant';

export default function configureStore(initialState) {
  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(reduxImmutableStateInvariant())
  );
}
```

## Instantiate Store and Provider

Need to wrap our Router component with the Provider component so that our app has access to the Redux store.

```
const store = configureStore();

render(
  <Provider store={store}>
    <Router history={browserHistory} routes={routes} />
  </Provider>,
  document.getElementById('app')
);
```

## Connect Container

If you don't use `mapDispatchToProps`, you have a very verbose statement for dispatching actions:

```
onClickSave(event) {
  this.props.dispatch(courseActions.createCourse(this.state.course));
}
```

## Step Through Redux Flow

You can actually analyze what Redux is doing through breakpoints: One for the action, reducer, `mapStateToProps` and `render()`.

## `mapDispatchToProps` Manual Mapping

This is the 2nd approach for dispatching an action.

This time include `mapDispatchToProps`:

```
function mapDispatchToProps(dispatch) {
  return {
    createCourse: (course) => dispatch(courseActions.createCourse(course))
  };
}
```

This greatly simplifies dispatching an action:

```
onClickSave(event) {
  this.props.createCourse(this.state.course);
}
```

## `bindActionCreators`

An issue with the 2nd approach is that our `mapDispatchToProps` function is quite verbose. A solution is to use redux's built-in `bindActionCreators` function:

```
function mapDispatchToProps(dispatch) {
  return {
    actions: bindActionCreators(courseActions, dispatch)
  };
}
```

Note that this binds ALL the actions found in our `courseActions.js` file. This changes the call to `createCourse` slightly:

```
onClickSave(event) {
  this.props.actions.createCourse(this.state.course);
}
```

## Container Structure Review

Let's review the 5 major pieces of a container component:

###### constructor

In the constructor we initialize state, and call our bind functions.

```
constructor(props, context) {
  super(props, context);

  this.state = {
    course: { title: "" }
  };

  this.onTitleChange = this.onTitleChange.bind(this);
  this.onClickSave = this.onClickSave.bind(this);
}
```

###### child functions

Functions used by `render()`

```
onTitleChange(event) {
  const course = this.state.course;
  course.title = event.target.value;
  this.setState({course: course});
}

onClickSave(event) {
  this.props.actions.createCourse(this.state.course);
}

courseRow(course, index) {
  return <div key={index}>{course.title}</div>;
}
```

###### `render()` function

Typically we would call a child component (although for now we're inlining the markup)

```
render() {
  return (
    <div>
      <h1>Courses</h1>
      {this.props.courses.map(this.courseRow)}
      <h2>Add Course</h2>
      <input
        type="text"
        onChange={this.onTitleChange}
        value={this.state.course.title} />

      <input 
        type="submit"
        value="Save"
        onClick={this.onClickSave} />
    </div>
  );
}
```

###### `propTypes`

Used for propType validation

```
CoursesPage.propTypes = {
  courses: PropTypes.array.isRequired,
  actions: PropTypes.object.isRequired
};
```

###### Redux `connect` and related functions

```
function mapStateToProps(state, ownProps) {
  return {
    courses: state.courses
  };
}

function mapDispatchToProps(dispatch) {
  return {
    actions: bindActionCreators(courseActions, dispatch)
  };
}

export default connect(mapStateToProps, mapDispatchToProps)(CoursesPage);
```

# Async in Redux

## Why a Mock API?

- Start before the API exists
- Independence
- Backup plan (if the API is down or broken)
- Ultra-fast responses
- Test slowness
- Aids testing
- Can easily point to the real API later

## Async Library Options

3 major players:
- `redux-thunk`: Returns function from action creators
- `redux-promise`: Flux standard actions and promises
- `redux-saga`: Uses ES6 generators and rich domain-specific language

Author recommends starting with `redux-thunk` and moving onto `redux-saga` once you get more comfortable with generators

## Thunk Overview

A thunk is a function that wraps an expression in order to delay its evaluation

Example:

```
export function deleteAuthor(authorId) {
  return dispatch => {
    return AuthorApi.deleteAuthor(authorId).then(() => {
      dispatch(deletedAuthor(authorId));
    }).catch(handleError);
  };
}
```

## Mock API Setup

Create a folder under `src` called `api` and copy-paste the files from the author's github repo. The author merely glances over the files but they're reasonably straightforward to understand.

## Add Thunk to Store

In `configureStore.js`, all you need to do is add

```
import thunk from 'redux-thunk';
```

And add `thunk` as one of the arguments passed to `applyMiddleware`.

## Create Load Courses Thunk

`courseActions.js` now becomes:

```
import * as types from './actionTypes';
import courseApi from '../api/mockCourseApi';

export function loadCoursesSuccess(courses) {
  return { type: types.LOAD_COURSES_SUCCESS, courses };
}

export function loadCourses() {
  return function(dispatch) {
    return courseApi.getAllCourses().then(courses => {
      dispatch(loadCoursesSuccess(courses));
    }).catch(error => {
      throw(error);
    });
  };
}
```

Basically, `loadCourses` makes an API call to get all courses. Upon success, these courses are fed into the `loadCoursesSuccess` action creator, which is then dispatched.

## Load Courses in Reducer

Very little you need to do in `courseReducer.js`. Just change the case in the switch statement to the following:

```
case types.LOAD_COURSES_SUCCESS:
  return action.courses;
```

## Dispatch action on page load

Add the following to `index.js`:

```
import {loadCourses} from './actions/courseActions';
...
store.dispatch(loadCourses());
```

Now the API call to our mock server will be executed on page load.

## Create Course List Component

Recall that `CoursesPage.js` is intended to be a container component. Reduce the render markup down to the following:

```
return (
  <div>
    <h1>Courses</h1>
    <CourseList courses={courses} />
  </div>
);
```

`CourseList` is then its own presentational component:

```
import React, {PropTypes} from 'react';
import CourseListRow from './CourseListRow';

const CourseList = ({courses}) => {
  return (
    <table>
      <thead>
        <tr>
          <th>&nbsp;</th>
          <th>Title</th>
          <th>Author</th>
          <th>Category</th>
          <th>Length</th>
        </tr>
      </thead>
      <tbody>
        {courses.map(course =>
          <CourseListRow key={course.id} course={course} />
        )}
      </tbody>
    </table>
  );
};

CourseList.propTypes = {
  courses: PropTypes.array.isRequired
};

export default CourseList;
```

Note in particular the following lines:

```
{courses.map(course =>
  <CourseListRow key={course.id} course={course} />
)}
```

Instead of inlining the mapped output, the author instead creates yet another component:

```
import React, {PropTypes} from 'react';
import {Link} from 'react-router';

const CourseListRow = ({course}) => {
  return (
    <tr>
      <td><a href={course.watchHref} target="_blank">Watch</a></td>
      <td><Link to={'/course/' + course.id}>{course.title}</Link></td>
      <td>{course.authorId}</td>
      <td>{course.category}</td>
      <td>{course.length}</td>
    </tr>
  );
};

CourseListRow.propTypes = {
  course: PropTypes.object.isRequired
};

export default CourseListRow;
```

This is definitely a good idea when the mapped output becomes reasonably complex.

# Async Writes in Redux

## Create Form Input Components

Author creates wrapper classes for even simple text inputs and select inputs.

Example:

```
import React, {PropTypes} from 'react';

const TextInput = ({name, label, onChange, placeholder, value, error}) => {
  let wrapperClass = 'form-group';
  if (error && error.length > 0) {
    wrapperClass += ' has-error';
  }

  return (
    <div className={wrapperClass}>
      <label htmlFor={name}>{label}</label>
      <div className="field">
        <input
          type="text"
          name={name}
          className="form-control"
          placeholder={placeholder}
          value={value}
          onChange={onChange} />
        {error && <div className="alert alert-danger">{error}</div>}
      </div>
    </div>
  );
};

TextInput.propTypes = {
  name: PropTypes.string.isRequired,
  label: PropTypes.string.isRequired,
  onChange: PropTypes.func.isRequired,
  placeholder: PropTypes.string,
  value: PropTypes.string,
  error: PropTypes.string
};

export default TextInput;
```

This has the advantage of abstracting away boilerplate such as bootstrap class names used for styling.

## Create Author Reducer

A common practice is to create a file that centralizes our initial state. Create a file `initialState.js` in the `reducers` folder:

```
export default {
  authors: [],
  courses: []
};
```

Now your course reducer would look as follows:

```
import * as types from '../actions/actionTypes';
import initialState from './initialState';

export default function courseReducer(state = initialState.courses, action) {
  switch (action.type) {
    case types.LOAD_COURSES_SUCCESS:
      return action.courses;
    default:
      return state;
  }
}
```

In this pattern, every reducer imports from the `initialState` object and derives its initial state from a property on that object.

## Map Author Data for Dropdown

Since the format of the data retrieved from the author API doesn't match the format for our `SelectInput` component, we need to create a function within `mapStateToProps` that maps the author data to the desired form:

```
const authorsFormattedForDropdown = state.authors.map(author => {
  return {
    value: author.id,
    text: author.firstName + ' ' + author.lastName
  };
});
```

***

> Redux is most useful for larger, complex apps. On trivial apps like this, the boilerplate may feel tedious. But the larger the app, and the more comfortable you get with this flow, the more this architecture is going to pay off in scalability, maintainability and clarity through consistency.

## Create Save Course Thunk

Three steps for adding a thunk:

Step 1 - Add action types in `actionTypes.js`:

```
export const CREATE_COURSE_SUCCESS = 'CREATE_COURSE_SUCCESS';
export const UPDATE_COURSE_SUCCESS = 'UPDATE_COURSE_SUCCESS';
```

Step 2- Add associated action creators for our new action types in `courseActions.js`:

```
export function createCourseSuccess(courses) {
  return { type: types.CREATE_COURSE_SUCCESS, courses };
}

export function updateCourseSuccess(courses) {
  return { type: types.UPDATE_COURSE_SUCCESS, courses };
}
```

Step 3 - In the same file, add our thunk:

```
export function saveCourse(course) {
  return function(dispatch) {
    return courseApi.saveCourse(course).then(course => {
      course.id ?
        dispatch(updateCourseSuccess(course)) :
        dispatch(createCourseSuccess(course));
    }).catch(error => {
      throw(error);
    });
  };
}
```

## Handle Creates and Updates in Reducer

Now that we've added our new actions, update the course reducer in `courseReducer.js` with two new switch statements:

```
case types.CREATE_COURSE_SUCCESS:
  return [
    ...state,
    Object.assign({}, action.course)
  ];
case types.UPDATE_COURSE_SUCCESS:
  return [
    ...state.filter(course => course.id !== action.course.id),
    Object.assign({}, action.course)
  ];
```

## Dispatch Create and Update

It seems React Router can be used to handle redirects:

```
import {browserHistory} from 'react-router';
...
redirectToAddCoursePage() {
  browserHistory.push('/course');
}
```

## Redirect via React Router Context

Setting up this redirect is a little convoluted. After `ManageCoursePage.propTypes`, add the following:

```
// Pull in the React Router context so router is available on this.context.router
ManageCoursePage.contextTypes = {
  router: PropTypes.object
};
```

Then in the `saveCourse` function, add the following:

```
this.context.router.push('/courses');
```

## Update State via `componentWillReceiveProps`

There is a flaw in `ManageCoursePage` that prevents the form data from populating when the page is refreshed directly. This has to do with the page's constructor receiving an empty `props.course`. Solution: Add a lifecycle method:

```
componentWillReceiveProps(nextProps) {
  if (this.props.course.id !== nextProps.course.id) {
    // Necessary to populate form when existing course is loaded directly.
    this.setState({course: Object.assign({}, nextProps.course)});
  }
}
```

[I must admit I'm confused by this, what's different when you navigate to the page through a link instead?]

# Async Status and Error Handling

## Create AJAX Status Reducer

Create a file `ajaxStatusReducer.js` in the `reducers` folder:

```
import * as types from '../actions/actionTypes';
import initialState from './initialState';

function actionTypeEndsInSuccess(type) {
  return type.substring(type.length - 8) === '_SUCCESS';
}

export default function ajaxStatusReducer(state = initialState.ajaxCallsInProgress, action) {
  if (action.type === types.BEGIN_AJAX_CALL) {
    return state + 1;
  } else if (actionTypeEndsInSuccess(action.type)) {
    return state - 1;
  }
  return state;
}
```

Note the use of `if/else` statements instead of a `switch` statement. This is preferable when a reducer is sufficiently simple.

Also note that this reducer decrements state by 1 whenever an action type ends with `'_SUCCESS'`.

## Use Promises to Wait for Thunks

When we manage a course and hit save, the page redirect to the courses page happens immediately, which is bad given the AJAX call to save course is likely still in progress. We can bypass this issue by using promises:

```
saveCourse(event) {
  event.preventDefault();
  this.props.actions.saveCourse(this.state.course)
    .then(() => this.redirect());

}

redirect() {
  this.context.router.push('/courses');
}
```

Notice all we had to do was attach `.then` to `this.propsa.actions.saveCourse`, with the callback function executiing the redirect.

## Display Save Notification

Adding toastr notifications is surprisingly easy.

Add an import statement to `index.js`:

```
import '../node_modules/toastr/build/toastr.min.css';
```

Import toastr in the file that uses it (in this case, `ManageCoursePage`):

```
import toastr from 'toastr';
```

Finally, add

```
toastr.success('Course saved');
```

In this case, we place it in the redirect function just before `this.context.router.push('/courses')`

All to say, you just need to do `toastr.<method>('message');` in order to get helpful notifications on your page.

## Error Handling

In our `saveCourse` thunk in `courseActions.js`, we can handle the error by dispatching a new AJAX call error action:

```
.catch(error => {
      dispatch(ajaxCallError(error));
      throw(error);
    });
```

You also have to:
- Add a new action to `actionTypes.js`
- Add a new action creater to `ajaxStatusActions.js`
- Modify the logic of `ajaxStatusReducer.js`

# Testing React

Author recommends using Mocha since it is proven, mature and has a huge ecosystem. However, there is a new framework called AVA that is gaining traction and definitely worth trying out.

Comparison:

| Mocha | AVA |
|--------|--------|
| Serial | Concurrent |
| No assertions built in | Assertions built in |
| Uses globals | No globals |
| No built-in ES6 supprt | Built-in ES6 support |
| Runs all tests on save | Runs only impacted tests |
| Long stack trace upon error | Short specific error with marker |
| Proven, mature, with huge ecosystem | New |

Given our choice of Mocha, we also need an assertion library. Chai is popular, but author opts for `expect`.

Comparison:

| Chai | Expect |
|--------|--------|
| to.equal | toBe |
| to.deep.equal | toEqual |
| to.exist | toExist |
| to.not.exist | toNotExist |
| to.be.above | ToBeGreaterThan |
| No spy | Spy built in |

## Helper Libraries

React Test Utils
- React testing library
- Built by Facebook
- Verbose API
- Two rendering options (shallow and deep rendering)

Why Enzyme?

| React Test Utils | Enzyme |
|--------|--------|
| findRenderedDOMComponentWithTag | find |
| scryRenderedDOMComponentsWithTag | find |
| scryRenderedDOMComponentsWithClass | find |

Enzyme's API is very well documented and largely mirrors jQuery selectors.

## Where to Test

- Browser (Karma)
- Headless Browser (PhantomJS)
- In-memory DOM (JSDOM)

Where do test files belong?

- Mocha default: `/test`
- We'll place tests alongside the file under test
 - Easy imports. Always `./filenameUnderTest`
 - Clear visibility
 - Convenient to open
 - Move files and tests together

## Testing React with React Test Utils

In `package.json`, change the `npm` test script so that mocha uses the `spec` reporter instead of the `progress` reporter.

***

Create a file `CourseForm.ReactTestUtils.test.js` with the following contents:

```
import expect from 'expect';
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import CourseForm from './CourseForm';

function setup(saving) {
  let props = {
    course: {},
    saving: saving,
    errors: {},
    onSave: () => {},
    onChange: () => {}
  };
  let renderer = TestUtils.createRenderer();
  renderer.render(<CourseForm {...props} />);
  let output = renderer.getRenderOutput();

  return {
    props,
    output,
    renderer
  };
}

describe('CourseForm via React Test Utils', () => {
  it('renders form and h1', () => {
    const { output } = setup();
    expect(output.type).toBe('form');
    let [ h1 ] = output.props.children;
    expect(h1.type).toBe('h1');
  });

  it('save button is labeled "Save" when not saving', () => {
    const { output } = setup(false);
    const submitButton = output.props.children[5];
    expect(submitButton.props.value).toBe('Save');
  });

  it('save button is labeled "Saving..." when saving', () => {
    const { output } = setup(true);
    const submitButton = output.props.children[5];
    expect(submitButton.props.value).toBe('Saving...');
  });
});

```

I really like the author's use of a setup function since this saves us from having to do

```
let renderer = TestUtils.createRenderer();
renderer.render(<CourseForm {...props} />);
let output = renderer.getRenderOutput();
```

for each test. It also saves us from having to create mock values for the component's props each time.

## Testing React with Enzyme

Create a file `CourseForm.Enzyme.test.js` with the following contents:

```
import expect from 'expect';
import React from 'react';
import {mount, shallow} from 'enzyme';
import CourseForm from './CourseForm';

function setup(saving) {
  const props = {
    course: {}, saving: saving, errors: {},
    onSave: () => {},
    onChange: () => {}
  };

  return shallow(<CourseForm {...props} />);
}

describe('CourseForm via Enzyme', () => {
  it('renders form and h1', () => {
    const wrapper = setup(false);
    expect(wrapper.find('form').length).toBe(1);
    expect(wrapper.find('h1').text()).toEqual('Manage Course');
  });

  it('save button is labeled "Save" when not saving', () => {
    const wrapper = setup(false);
    expect(wrapper.find('input').props().value).toBe('Save');
  });

  it('save button is labeled "Saving..." when saving', () => {
    const wrapper = setup(true);
    expect(wrapper.find('input').props().value).toBe('Saving...');
  });
});

```

Clearly Enzyme tests are much more concise and easy to interpret.

# Testing Redux

## Testing Connected React Components

One complication of having to test container components is having to deal with the issue of our component being wrapped in a call to `connect`.

One solution:

```
const wrapper = mount(<Provider store={store}><ManageCoursePage/></Provider>);
```

Basically you wrap the component you're testing with the Provider.

Another solution (used by the author): Use two exports for your original container component (not the test).

```
export class ManageCoursePage extends React.Component {...}
...
export default connect(mapStateToProps, mapDispatchToProps)(ManageCoursePage);
```

Then in the associated test, just need to change the import to not use the default:

```
import {ManageCoursePage} from './ManageCoursePage';
```

## Testing `mapStateToProps`

Recall the use of dual exports in `ManageCoursePage.js`. This causes the following linting warning:

```
Using exported name 'ManageCoursePage' as identifier for default export  import/no-named-as-default
```

Can bypass this with a special comment:

```
import ManageCoursePage from './components/course/ManageCoursePage'; // eslint-disable-line import/no-named-as-default
```

## Testing Action Creators

Testing action creators is so simple that it's basically pointless.

## Testing Reducers

Making tests for reducers is so simple that you can actually automate the creation of reducer tests with a github library  called `redux-test-recorder`.

***

Create a file `courseReducer.test.js` with the following contents:

```
import expect from 'expect';
import courseReducer from './courseReducer';
import * as actions from '../actions/courseActions';

describe('Course Reducer', () => {
  it('should add course when passed CREATE_COURSE_SUCCESS', () => {
    const initialState = [
      {title: 'A'},
      {title: 'B'}
    ];

    const newCourse = {title: 'C'};

    const action = actions.createCourseSuccess(newCourse);

    // act
    const newState = courseReducer(initialState, action);

    // assert
    expect(newState.length).toEqual(3);
    expect(newState[0].title).toEqual('A');
    expect(newState[1].title).toEqual('B');
    expect(newState[2].title).toEqual('C');
  });
});
```

Can also test updating a course:

```
it('should update course when passed UPDATE_COURSE_SUCCESS', () => {
  // arrange
  const initialState = [
    {id: 'A', title: 'A'},
    {id: 'B', title: 'B'},
    {id: 'C', title: 'C'}
  ];

  const course = {id: 'B', title: 'New Title'};
  const action = actions.updateCourseSuccess(course);

  // act
  const newState = courseReducer(initialState, action);
  const updatedCourse = newState.find(a => a.id === course.id);
  const untouchedCourse = newState.find(a => a.id === 'A');
  expect(untouchedCourse.title).toEqual('A');
  expect(newState.length).toEqual(3);
});
```

Note that we are passing minimal objects as courses into our initial state such as `{title: 'A'}`. Normally a course contains an `id`, `watchHref`, `title`, and so on, but we only need to use the properties that are absolutely required for the test.

## Testing Thunks

Create a file `courseActions.test.js` with the following contents:

```
import expect from 'expect';
import * as courseActions from './courseActions';
import * as types from './actionTypes';
import thunk from 'redux-thunk';
import nock from 'nock';
import configureMockStore from 'redux-mock-store';

const middleware = [thunk];
const mockStore = configureMockStore(middleware);

describe('Async Actions', () => {
  afterEach(() => {
    nock.cleanAll();
  });

  it('should create BEGIN_AJAX_CALL and LOAD_COURSES_SUCCESS when loading courses', (done) => {
    // Here's an example call to nock. Not necessary since we already have a mock API.
    // nock('http://example.com/')
    // .get('/courses')
    // .reply(200, { body: { course: [{ id: 1, firstName: 'Cory', lastName: 'House' }] } });

    const expectedActions = [
      {type: types.BEGIN_AJAX_CALL},
      {type: types.LOAD_COURSES_SUCCESS, body: {courses: [{id: 'clean-code', title: 'Clean Code'}]}}
    ];

    const store = mockStore({courses: []}, expectedActions);
    store.dispatch(courseActions.loadCourses()).then(() => {
      const actions = store.getActions();
      expect(actions[0].type).toEqual(types.BEGIN_AJAX_CALL);
      expect(actions[1].type).toEqual(types.LOAD_COURSES_SUCCESS);
      done();
    });
  });
});
```

## Testing the Store

Testing the store is really an integration test, not a unit test. We want to ensure that our actions, the store, and our reducers are interacting together as expected.

Create a file `store.test.js` with the following contents:

```
import expect from 'expect';
import {createStore} from 'redux';
import rootReducer from '../reducers';
import initialState from '../reducers/initialState';
import * as courseActions from '../actions/courseActions';

describe('Store', () => {
  it('Should handle creating courses', () => {
    // arrange
    const store = createStore(rootReducer, initialState);
    const course = {
      title: "Clean Code"
    };

    // act
    const action = courseActions.createCourseSuccess(course);
    store.dispatch(action);

    // assert
    const actual = store.getState().courses[0];
    const expected = {
      title: "Clean Code"
    };

    expect(actual).toEqual(expected);
  });
});
```

# Production Builds

## Intro

The goal of our production build is to have a `dist` folder containing `index.html`, `bundle.js` and `styles.css`.

Production build process
- Lint and run tests
- Bundle and minify JS and CSS
- Generate JS and CSS sourcemaps
- Exclude dev-specific concerns
- Build React in production mode
- Open prod build in browser

## Setup Production Redux Store

`configureStore.js` gets split into three files: itself, `configureStore.dev.js` and `configureStore.prod.js`.

`configureStore.js` becomes:

```
if(process.env.NODE_ENV === 'production') {
  // Dynamic imports aren't supported by ES6, so need to use require instead of import
  module.exports = require('./configureStore.prod');
} else {
  module.exports = require('./configureStore.dev');
}

```

The only difference between the dev and prod files is removing the `reduxImmutableStateInvariant` middleware, which is only useful for development.

## Setup HTML Build

Production needs a modified `index.html` file since we want the stylesheet to be separate from our `bundle.js` file. Doing this fixes the issue of seeing a flash of unstyled content (the styles don't apply until the javascript is parsed).

## Review Results

- Dev built: 4.8MB
- Prod build: 121KB
- Essentials: 64KB (Drop babel-polyfill, toastr, jQuery, Bootstrap)

For perspective, 64KB is about the size of a single JPEG image. This kind of file size just isn't possible using Angular1, Angular2 or Ember.

## Final Challenge

1. Author administration (make sure you can't delete an author who already has a course)
2. Delete course
3. Hide empty course list (when all courses are deleted)
4. Unsaved changes message
5. Client-side validation
6. Handle 404's
7. Show # courses in Header
8. Pagination
9. Sort course table alphabetically
10. Revert abandoned changes