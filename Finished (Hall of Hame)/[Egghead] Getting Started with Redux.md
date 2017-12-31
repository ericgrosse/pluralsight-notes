# Reducer Composition with Arrays

When a Reducer becomes too complex, you can break it into several reducers and compose them.

```
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return { ... };
    case 'TOGGLE_TODO':
      return state.map(t => todo(t, action)); // Calls 'todo' reducer on each array element
    default:
      return state;
  }
}
```

In the above example, the `todos` reducer calls the `todo` reducer.

# Reducer Composition with Objects

Ideally, you want each of your reducers to manage separate parts of your state. This is done through the reducer composition pattern:

```
const todoApp = (state = {}, action) => {
  return {
    todos: todos(
      state.todos,
      action
    ),
    visibilityFilter: visibilityFilter(
      state.visibilityFilter,
      action
    )
  }
}
```

It's all JavaScript: Write a function that takes `state` and `actions` as props (like any other reducer), and return an object where each key's associated value is a reducer.

# Reducer Composition with combineReducers()

The reducer composition pattern is so common that Redux includes a `combineReducers` package to save you the hassle of having to write the implementation yourself.

```
const { combineReducers } = Redux;
const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```

As a useful convention, always name your reducers after the state keys they manage.

# Extracting Container Components (FilterLink)

Can use `const state = store.getState();` to avoid having to pass props through children elements! The downside is you have to subscribe and unsubscribe to the store in `componentDidMount` and `componentWillUnmount`, respectively.

# Passing the Store Down Implicitly via Context

The `Provider` component is just a wrapper component that adds the Redux store to React's context:

```
class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store
    };
  }

  render() {
    return this.props.children;
  }
}
```

Context should be used sparingly since it is the React equivalent of a global variable.

# Generating Containers with connect() from React Redux

By wrapping your component with a call to `connect`, there's no need for the store to be subscribed and unsubscribed because it's handled for you behind the scenes.

`mapStateToProps` takes the state from the Redux store and returns props for your presentational components
`mapDispatchToProps` takes the store's dispatch method and returns props that dispatch actions

If you pass `mapDispatchToProps` as `null` to `connect`, then `dispatch` will be available as a prop for your component (as a default behavior).

```
let AddTodo = ({ dispatch }) => {
 ...
}

AddTodo = connect()(AddTodo) // or connect(null, null)(AddTodo)
```

`mapStateToProps` has a 2nd argument, `ownProps`, this refers to the container component's own props.
