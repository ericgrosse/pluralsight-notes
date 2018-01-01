# Simplifying the Arrow Functions

When a function does nothing more than return an object, there's a shorthand for that:

```
const mapStateToProps = (state) => ({
  todos: getVisibleTodos(state.todos, state.visibilityFilter)
});
```

The `()` brackets omit the need for a return statement.

When a function is defined within an object, can use concide method notation instead of arrow functions:

```
onClick() {

}
```

# Persisting the State to the Local Storage

Write a `loadState` and `saveState` function that gets and sets `state` to `localStorage`:

```
export const loadState = () => {
  try {
    const serializedState = localStorage.getItem('state');
    if (serializedState === null) {
      return undefined;
    }
    return JSON.parse(serializedState);
  } catch (err) {
    return undefined;
  }
};

export const saveState = (state) => {
  try {
    const serializedState = JSON.stringify(state);
    localStorage.setItem('state', serializedState);
  } catch (err) {
    // Ignore write errors.
  }
};
```

Next, call `loadState()` as the 2nd argument to the `createStore` call:

```
const persistedState = loadState();
const store = createStore(
  todoApp,
  persistedState
);
```

Also subscribe to changes to the store's state, with a throttle call from `lodash` limiting the number of saves to once per second:

```
store.subscribe(throttle(() => {
  saveState({
    todos: store.getState().todos
  });
}, 1000));
```

## Aside

Use `node-uuid` to generate unique IDs:

```
import { v4 } from 'node-uuid';
v4();
```

# Using mapDispatchToProps Shorthand Notation

Before:

```
const mapDispatchToProps = (dispatch) => ({
  onTodoClick(id) {
    dispatch(toggleTodo(id));
  },
});

const VisibleTodoList = withRouter(connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList));
```

After:

```
const VisibleTodoList = withRouter(connect(
  mapStateToProps,
  { onTodoClick: toggleTodo }
)(TodoList));
```

# Wrapping Dispatch to Recognize Promises

Can add functionality to dispatch by overriding it with a wrapper function.

```
// Inside configureStore.js

if (process.env.NODE_ENV !== 'production') {
  store.dispatch = addLoggingToDispatch(store);
}

store.dispatch = addPromiseSupportToDispatch(store);
```

This can be continued indefinitely, leading to the concept of middleware.

# Applying Redux Middleware

Useful middlewares:

```
redux-promise
redux-logger
```

# Normalizing API Responses with Normalizr

Consider using the `normalizr` package to normalize your API responses

