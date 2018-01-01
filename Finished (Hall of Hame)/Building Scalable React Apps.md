# Getting Started

## Getting the Stack up and Running

```
git clone https://github.com/hendrikswan/react-boilerplate
cd react-boilerplate
npm run setup
```

## Inspecting Different Types of Errors

The boilerplate has linting already built in. If you try to improperly indent an HTML element, the error will show up in the editor itself. In the case of Atom, it shows a red dot you can hover over for the error details.

To better deal with runtime errors, author changes `devTool` from  `cheap-module-eval-source-map` to `source-map` inside `webpack.dev.babel.js`. This actually works, and you should use this setting from now on in your dev builds.

## Ramping up to Start Coding

`npm run clean` will remove a bunch of boilerplate from the project, such as the starting container components. I've had problems with this command failing, so if any issue pops up later, it might be due to this.

# An Introduction to Building Components with react-boilerplate

## Using Scaffolding to Generate Components

`npm run generate container` and `npm run generate component` will generate a container and component, respectively.

## Getting Data from the Store on to Your Component

In the `NavigationContainer` that we built earlier, it includes a reducer which contains

`const initialState = fromJS({})`

Change this to:

```
const initialState = fromJS({
  topics: [
    {
      name: 'libraries',
      description: 'links to useful open souce libraries',
    },
    {
      name: 'apps',
      description: 'links to new and exciting apps',
    },
    {
      name: 'news',
      description: 'links to programming related news articles',
    },
  ],
});
```

Basically just passing an in an array of objects.

You can expect the state from the Navigation reducer to be passed into the state of the Navigation component.

In the Navigation component, you can de-reference `topics` as a prop:

`function Navigation({topics}) {...}`

# Loading Data from the Server with Redux-sage

## Defining Action Creators

The NavigationContainer has a `constants.js` file. Place the following:

```
export const REQUEST_TOPICS = 'app/NavigationContainer/REQUEST_TOPICS';
export const REQUEST_TOPICS_SUCCEEDED = 'app/NavigationContainer/REQUEST_TOPICS_SUCCEEDED';
export const REQUEST_TOPICS_FAILED = 'app/NavigationContainer/REQUEST_TOPICS_FAILED';
```

The author chooses to namespace each constant with `app/NavigationContainer`. Also notice we're creating three separate actions: Some action itself, its success, and its failure.

# Handling Events with Redux-saga

## Storing User Input on the Redux Store

You can define an action and dispatch it without having a reducer handle the action. In fact, you can go into Redux dev tools to ensure the action is being fired when you expect it to (ex/ clicking some element with an onClick handler).

Workflow:

- Define an action constant (MyComponent/constants.js)
- Import the action constant and write a function for it (MyComponent/actions.js)
- Import the action creator and add a mapDispatchToProps entry for it (MyComponentContainer/index.js)
- Add whatever you put into mapDispatchToProps as a prop, and hook it up to an event handler (MyComponent/index.js)

Example for the last point:

```
function Navigation({ topics, selectTopic }) {
  const topicNodes = topics.map(topic => (
    <div
      key={topic.name}
      onClick={() => selectTopic(topic)}
    >
      {topic.name}
    </div>
  ));

  return (
    <div className={styles.navigation}>
      {topicNodes}
    </div>
  );
}
```

The key line is `onClick={() => selectTopic(topic)}`, this is what dispatches the `selectTopic` action creator.

As for the reducer, all you need to do is import a new constant and write a case for it in the reducer's switch statement. Example:

```
import { fromJS } from 'immutable';
import {
  REQUEST_TOPICS_SUCCEEDED,
  SELECT_TOPIC,
} from './constants';

const initialState = fromJS({
  topics: [],
});

function navigationContainerReducer(state = initialState, action) {
  switch (action.type) {
    case REQUEST_TOPICS_SUCCEEDED:
      return state.set('topics', action.topics);
    default:
      return state;
    case SELECT_TOPIC:
      return state.set('selectedTopic', action.topic);
  }
}

export default navigationContainerReducer;

```

## Adding an Additional Container

Workflow

- Generate the component, then generate the container
- Have the container import the component. Ex: `import LinkedList from '../../components/LinkedList'`
- The container should also return the component. Ex: `return (<LinkList {...this.props} />)`
- In `routes.js`, import the container's reducer and saga. Example:

```
System.import('containers/LinkListContainer/reducer'),
System.import('containers/LinkListContainer/sagas'),
```

- Inject both the reducer and saga:

```
injectReducer('linkListContainer', linkListReducer.default);
injectSagas('linkListReducer', linkListSagas.default);
```

- Import and render the container component wherever you want to use it

(To be continued)

## Storing Data from the Server on the Redux Store

Sagas intercept actions in order to perform API calls that retrieve data. Example:

```
export function* defaultSaga() {
  yield* takeLatest(SELECT_TOPIC, fetchLinks);
}
```

When the `SELECT_TOPIC` action is fired, `fetchLinks` fetches data from an API.

What's interesting is `SELECT_TOPIC` is defined in the NavigationContainer, but used in the LinkListContainer through the saga.

## Summary

> Hopefully you're starting to see how you can combine redux, redux-saga and reselect to do cross component logic while still keeping your components decoupled.

This is essential: **Cross component logic while still keeping your components decoupled**

# Styling Your Components with CSS

## Styling the AppBar component

In the app's global stylesheet, you can import vendor stylesheets using `@import`:

```
@import url('https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css');
@import url('https://fonts.googleapis.com/css?family=Roboto:400,300,500,700');
```

## Creating an Action to Toggle the Drawer Component's Visibility

The AppBar is shown or hidden based on a new state property, `toggleDrawer`. The author goes through the trouble of passing this through Redux instead of handling with local state.

Workflow:

- Define action constant
- Define action creator
- Add the action to mapDispatchToProps
- Use the action in the desired component
- Test that the action fires in the browser using redux dev tools
- Update the reducer to handle the action so that it updates state

## Adding Conditional Styling Based on State

Want to use the `classnames` library

```
import classNames from 'classNames';
```

Usage example:

```
<div
  className={classNames(styles.drawer, { [styles.drawerOpen]: isDrawerOpen })}
>
```

## Styling the Link Component

The React boilerplate we used has post CSS setup, meaning we don't need to use SASS.

# Adding Routes to your Application

## Getting a Component to Display on a Child Route

## Introduction

Routing will be one the Redux way, meaning that all our route changes will go through the dispatcher. This has some benefits in testimg bacause you can time travel. You can undo actions and go back to the previous route.

## Getting a Component to Display on a Child Route

`npm run generate route` generates a route for you

## Preventing Code Injection through Route Parameters

Look into selectors, seems to be a new concept in the world of React

## Summary

> To do this (routing) we intercepted an action in our saga, and dispatched another action using the PUT side effect to go and instruct our router bindings to go and change the route.

# Building Forms to Gather User Input

## Introduction

Give Redux forms a try. Supposedly it allows you to handle user input without having to resort to local state.

## Wiring a Form with Validation

Author uses an npm package `email-validator` to validate emails.
Also suprised to see the author use a `ref` to target input elements instead of `evt.target.name`

## Presenting a Validation Message

Remember to use the `classNames` library and the computed property syntax for conditional class names.
