# Random Observations

The exercise files include a neat feature where errors are overlayed in the browser so you don't even need to open the console in order to see them.

# Inline Styles

## Current Origins of Inline Styles

In CSS everything is global by default. This makes it hard to encapsulate any styles that should be local to some specific UI.

Facebook compiled a bunch of hacks to solve most of these problems in CSS. This lead the proponent of React inline styles to believe there might be a good reason to defy the conventional wisdom around writing CSS.

## Inline Styles Demo - Styling

Note that when coding inline styles, all our styles are actually written in Javascript.

Really not a fan of inline styles.

## Evaluate Inline Styles

Drawbacks
- No access to media queries
- No native support for pseudo-selectors
- No keyframe animations

# Radium

## Radium Intro

Radium is a third-party library that is essentially inlineStyles++.

`radium = inlineStyles + mediaQueries + pseudoSelectors + keyframes`

Radium provides a React component that wraps all your Radium styles components.

```
Radium(<MyComponent/>)
```

## Radium Demo

Currently here