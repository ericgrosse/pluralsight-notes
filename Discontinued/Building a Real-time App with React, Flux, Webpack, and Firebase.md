# Introduction

## Welcome

> Why would you want to learn all this stuff though? Well React is a library, it's not a full comprehensive framework where you color between the lines. React developers need to understand that they find themselves in an ever changing ecosystem where certain libraries lose flavor to others as the community support reaches a sort of transient equilibrium.

## The Demo App

The course walks you through building a real-time chat application

## A Breakdown of the Stack

### Webpack

Pete Hunt, one of the original engineers that started React at Facebook, created a `webpack-howto` tutorial on github.

Author also mentions the Reactiflux and webpack Slack groups  that have sizeable memberships (>800 members). Maybe you should join?

### Material-UI

> A set of React components that implement Google's Material Design

> I'm so impressed by this library. The components look amazing and are so easy to work with. And we've got a whole bunch of them, each one with a lot of flexibility and styling options. You can also customize it in the form of specifying theme rules and color palettes.

Check out their beautiful website

### Firebase

> They go through painstaking effort to relentlessly document the service. The end result is the best documentation that I've ever experienced for anything... These guys set a new standard with their docs and guides.

### Flux

Using a library called `Alt` to implement the Flux architecture.

> Alt is semi-popular on Github. It's not the most popular library for implementing the flux architecture anymore, but it's super user friendly and easy to learn and it's got a decent community around it with solid documentation and patterns. It gets rid of so much boilerplate, it's totally worth it. It uses conventions instead of explicitly configuring everything with loads of code, which is something that we first appreciated in Ruby on Rails.

***

**Make sure you use the package.json file on the author's github page**

The link can be found at [github.com/hendrikswan/react-stack](github.com/hendrikswan/react-stack)

# Environment and Tools

## Using React with Webpack

According to author, `test: /\.jsx?$/` matches both `jsx` and `js` extensions. It's not explained how this works.

To enable ES7 features, create a `.babelrc` file that contains:

```
{
  "stage": 0
}
```

## Debugging and Reloading

One way to add sourcemaps is to add the following entry to `webpack.config.js`:

```
devtool: 'eval-source-map'
```

[Discontinuing the course because the author doesn't explain anything he's doing in even remotely enough detail. Might as well just read blog posts instead.]