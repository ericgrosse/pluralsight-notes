# Environment Setup - Build Process

## Project Files

The project is hosted at https://github.com/bstavroulakis/vue-spa

What's cool is that you can switch through the project's modules using `git checkout -m build-process` for example. This switches the branch to a different release, each release corresponding to a module of the course.

## Project Setup

In the command line, `code .` opens Visual Studio Code in the current directory.

## Editorconfig

Visual Studio Code needs an extension to support `.editorconfig` files. Downloaded `EditorConfig for VS Code`.

## .Vue Files

It is recommended to use `.vue` files for single file components. They conveniently hold all your HTML, CSS & JS in one place and are structured as follows:

```
<template></template>
<script></script>
<style></style>
```

## Styles

`<style lang="scss">` causes your CSS to be pre-processed with SASS (assuming you added the SASS loader to webpack).

## Scoped Styles

Using `<style scoped>`, your single file component's stylesheet will be scoped using a data attribute. This has the beautiful effect of scoping your CSS while keeping the CSS classname clean.

```
// HTML
<div data-v-e458c95e class="card">
// CSS
.card[data-v-e458c95e] {
  padding-bottom: 40px;
}
```

# Routing

## Router-link - Scroll-behavior

Router links are very similar to `react-router` `<Link>` tags.

```
<router-link to="/" exact>
  <img src="..." />
</router-link>
```

One unique feature in vue-router is the ability to manipulate scroll behavior:

```
scrollBehavior: (to, from, savedPosition) => ({ y: 0 }),
```

## Redirect

Redirects are incredibly simple:

```
{ path: '/', redirect: '/category/front-end' }
```

## Route Parameters

Vue.js exposes the router through the `$route` object.

```
id: this.$route.params.id
```

Can watch route changes:

```
watch: {
  '$route' (to, from) {
    this.id = to.params.id;
    this.loadPosts();
  }
}
```

## Route Query and Name

Can add a `name` alias to your routes:

```
{ path: '/category/:id', name: 'category', component: Category }
```

Can also pass in a JSON object to `router-link`:

```
<router-link :to="{ name: 'category', params: { id: 'mobile' } }">Mobile</router-link>
```

Without the the colon, the prop will be interpreted as a string instead of being parsed correctly.

## Lazy Loading

Replace

```
import Category from './theme/Category.vue';
```

with

```
const Category = () => System.import('./theme/Category.vue');
```

This instead defines our components as async components.

By doing this, the client only loads the component they're on instead of the entire application all at once. Very useful as an app increases in size.

# API Communication

## JWT

Simplest explanation for JWTs I've come across:

- Request `{ username: "bill", password: "vuejs" }`
- Response `{ token: "eyJhbGciOiJIU...", tokenExpiration: 1507361352 }
- Request (using same token)
- Server knows if the user is authenticated or not based on the token

## Authentication Call

Vue uses `v-on` for event handlers:

```
<button v-on:click="login()" class="button is-primary">Login</button>
```

API calls couldn't be simpler!

```
methods: {
  login() {
    appService.login({username: this.username, password: this.password}) // API call
    .then((data) => {
      window.localStorage.setItem('token', data.token);
      window.localStorage.setItem('tokenExpiration', data.expiration);
    })
    .catch(() => window.alert('Could not login!'));
  }
}
```

Where `appService` is your own API helper. Example:

```
// Defined within an appService object
login (credentials) {
  return new Promise((resolve, reject) => {
    axios.post('/services/auth.php', credentials)
  })
  .then(res => {
    resolve(response.data);
  })
  .catch(err => {
    reject(err.status);
  });
}
```

## Authentication Status

Check for token expiration on the `created` lifecycle method:

```
created() {
  let expiration = window.localStorage.getItem('tokenExpiration');
  let unixTimestamp = new Date().getTime() / 1000;
  if (expiration !== null && parseInt(expiration) - unixTimestamp > 0) {
    this.isAuthenticated = true;
  }
}
```

Then in your markup:

```
<div v-if="isAuthenticated">...</div>
<div v-else>...</div>
```

Author recommends

- Adding a CAPTCHA to prevent bots from trying to find your password
- Including a cross-site request forgery token

## Intercept Requests

Watches are so useful. Here's how you can watch the `isAuthenticated` value for changes. If it's true, retrieve a profile from an API call, otherwise clear the profile.

```
watch: {
  isAuthenticated: function(val) {
    if (val) {
      appService.getProfile()
      .then(profile => this.profile = profile);
    }
    else {
      this.profile = {};
    }
  }
}
```

Given the use of JWTs, it would be impractical to have to attach it to the header of every API request we make. This is where axios interceptors come into play.

```
axios.interceptors.request.use(function(config) {
  // Needed for server-side rendering
  if (typeof window === 'undefined') {
    return config;
  }
  const token = window.localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

# State Management

## Communication Through Events

Create an `eventBus` instance, and use it to emit an event that is listened to by another component.

**Login.vue**

```
watch: {
  isAuthenticated: function(val) {
    ...
    eventBus.$emit('authStatusUpdate', val);
  }
}
```

**AppHeader.vue**

```
created() {
  eventBus.$on('authStatusUpdate', isAuthenticated => this.isAuthenticated = isAuthenticated);
}
```

## Vuex Setup

Setting up the store is incredibly simple.

**index.js**

```
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const state = {
  isAuthenticated: false
};

const store = new Vuex.Store({
  state
});

export default store;
```

Amazingly, you can use the Vue.js devtools to inspect your Vue store, no second extension or extra setup needed!

## Getters

Can inspect the store in Chrome devtools:

```
console.log($vm.$store.state);
$vm.$store.state.isAuthenticated = true;
```

You read from the store by using the `computed` property:

```
computed: {
  isAuthenticated() {
    return this.$store.state.isAuthenticated
  }
}
```

In Vuex though, we don't want to access the state directly. This is done by adding a `getters` object to the Vuex store.

**index.js**

```
const store = new Vuex.Store({
  state,
  getters: {
    isAuthenticated: (state) => state.isAuthenticated
  }
})
```

**AppHeader.vue**

```
import { mapGetters } from 'vuex';
export default {
  computed: {
    ...mapGetters(['isAuthenticated])
  }
}
```

## Actions - Mutations

The Vuex store uses `actions` and `mutations` objects for updating state.

```
actions: {
  logout(context) {
    context.commit('logout') // Consider this a logout action
  }
},
mutations: {
  logout(state) {
    // In case of server-side rendering
    if (typeof window !== 'undefined') {
      window.localStorage.setItem('token', null);
      window.localStorage.setItem('tokenExpiration', null);
    }
    state.isAuthenticated = false;
  }
}
```

## Triggering Actions

TODO: Revisit

## Modules

Modules create separate segments in the Vuex store. Here's an example for creating a posts module.

**posts.js**

```
const state = {
  posts: [],
  categoryId: 0
};

const getters = {
  posts: state => state.posts
}

const actions = {

};

const mustations = {

};

export default {
  namespaced: true, // Very important
  state,
  getters,
  actions,
  mutations
}
```

**index.js**

```
import postsModule from './posts'
...
const store = new Vuex.Store({
  modules: {
    postsModule
  },
  ...
})
```

**Category.vue**

```
computed: {
  ...mapGetters('postsModule', ['posts']) // Namespaced by postsModule
},
```

# Server-side Rendering

## Introduction

Benefits

- No flashing content on page load (from asynchronous requests)
- SEO

## Summary

Server-side routing requires a lot of tedious setup, but it is doable.

# Testing

## Introduction

Three types of tests are common in software development:

- UI (least common)
- Integration
- Unit (most common)

## Testing Options

- Karma, Mocha, Chai, Sinon (used together, most popular)
- Jasmine
- Jest
- AVA

## Testing Setup

Configure Karma via `karma.config.js` and Webpack via `webpack.test.config.js`.

> We set the running environment to a headless browser (`PhantomJS`), the frameworks that will run our tests (`mocha`, `sinon-chai`), the location of the files that we have our test configuration in, the preprocessors to run on the fly to transpile our code (`webpack`), and the plugins necessary for Karma to work with other libraries.

Under `test/unit`, create an `index.js` file that instructs Karma of which files to test, in this case all files ending in `.spec`:

```
const testsContext = require.context('./specs', true, /\.spec$/);
testsContext.keys().forEach(testsContext);
```

## Testing Component

Setting up the npm `test` script is trivial:

```
"scripts": {
  ...
  "test": "karma start test/unit/karma.config.js --single-run"
}
```

Under `test/unit`, add an `.eslintrc` file to clear up some test setup related warnings:

```
{
  "env": {
    "mocha": true
  },
  "globals": {
    "expect": true,
    "sinon": true
  }
}
```

## Testing Component Changes

Basically, you create a component with props, update one of the props, and check that the changed prop matches what you'd expect.

Look into `Vue.nextTick` bug/quirkiness. Author had to wrap his `expect` statement with it after changing a prop.

```
comp.link = 'http://fullstackweekly.com';
Vue.nextTick(() => {
  expect(...).to.equal(...);
  done();
});
```

# Deploying

## Production Build Optimizations

In the webpack production config file, you can add a vendor entrypoint. This separates your app bundle into a vendor bundle and a custom bundle. This is useful because we may set a large cache header for our vendor file that will update very rarely. Can also take advantage of HTTP/2 parallel loading to load scripts faster.

VueJS's own documentation offers deployment tips.

Consider using PostCSS to strip away unused CSS.

## Continuous Integration

Commit Code => **Test on Different Environment** => Trigger Update => Deploy New Version

One of the most popular integration servers is Travis CI.

The process is remarkably simple:

- Pick a repository to track
- Add a `.travis.yml` file to the root of your app

That's it! The travis file is very simple to configure as well:

```
language: node_js
node_js:
  - "7.5"
```

When you setup Travis, your `README.md` file will show a `build passing` message

## Deploy Script

Author wrote a `deploy.cmd` deploy script to be run on his Azure hosting setup:

```
call npm install
call npm run build

call cd %DEPLOYMENT_TARGET%
...
```

## Summary

Ideas for continuation

- `propdoc` to generate documentation
- `i18n` (Internationalization), Logging
- Nightwatch for end-to-end testing
- Increase test coverage
- Write pre-commit tests so your code won't commit if the tests don't pass
- Typescript
- Vue-cli
- Ext.js
