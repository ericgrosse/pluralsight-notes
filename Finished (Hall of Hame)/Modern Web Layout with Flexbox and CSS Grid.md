# Introduction

## A Little History

In the early days of the web, there wasn't much to web layout. Basically, everything was a single column, and that was pretty horrible. Then someone discovered they could use HTML tables to get their content into columns and start doing some pretty cool stuff. Oftentimes, a lot of extra junk had to be added to the code to achieve the look.

The float based system won over the table system because of the need for fluid width to accomodate multiple devices and viewport sizes.

# Flexbox

## Introduction

The previous layout modes from CSS 2.1 -- block, inline, table and positioning -- do not provide enough flexibility and control for the web as we know it today. It's become hacky and complex to layout more complex web applications, which is why Flexbox has been created.

Flex layout provides the ability to arrange items in any direction: Left to right, right to left, top to bottom, or bottom to top.

## The Flex Container

Using flex is as simple as adding `display: flex;` to your flex container.

`display:flex` makes the container take up the full width of the page
`display: inline-flex` makes the container take up exactly the width of its inner items

## Flex Flow Direction

`flex-direction: row`
`flex-direction: row-reverse`
`flex-direction: column`
`flex-direction: column-reverse`

## Flex Line Wrapping

`flex-wrap: nowrap;`
`flex-wrap: wrap;`
`flex-wrap: wrap-reverse;`

Can combine the effects of `flex-direction` and `flex-wrap` by using `flex-flow`

Ex/ `flex-flow: column wrap`

## Display Order

It seems to be a good convention to use `<div class="flex-item">` for your items and `<div class="flex-container>` for your items container. Also add individual class names to each item (ex/ `flex-item-01`) so that you can differentiate their sytling from the general `flex-item`.

Every flex item has a default `order` (ordinal) value of 0, and are ordered by ordinal group. Therefore, if you set any flex item to have an order of 1, it would go to the end of the container. After ordering by ordinal number, the flex items are then ordered by the order in which they are found in the HTML source code.

## Flexibility

```
.flex-item {
	flex-grow: 1;
}
```

This will make each flex item have an equal width and combine to take up the entire width of the container.

```
flex-item-02 {
	flex-grow: 4;
}
```

This will make `flex-item-02` four times larger than the other flex items.

`flex-shrink` property opposite of `flex-grow`

`flex-basis` sets the initial size of the items before the extra space is distributed.

Can use a shorthand property for convenience:

`flex: flex-grow flex-shrink flex-basis;`

Ex/ 
`flex: 0 1 auto;`

## Alignment

In CSS 2.1, vertically centering an item within a container required some very hacky CSS.

### Flex Container Alignment

`justify-content` aligns the content along the main axis, and can be set to `flex-start`,`flex-end`, `center`, `space-between` and `space-around`. To justify content vertically instead of horizontally, set `flex-direction` to `column`.

`align-content` is the cross axis equivalent of `justify-content`

`align-items` aligns the items along the cross-axis, and like `justify-content`, you can flip the axis by changing `flex-direction` to `column`.

`align-items` can be set to `flex-start`, `flex-end`, `center`, `baseline` (aligns the text of each item), `stretch` (default value).

### Flex Item Alignment

Can use `align-self` with basically the same properties as `align-items`

## Flexbox in the Real World

Oftentimes when using a grid system in a project, we'll use a bunch of div containers with widths, floats and clears. They generally work well but flexbox provides a much more elegant way, with more flexibility.

In flexbox, if you want two items to sit side by side with a width of 50%, youd simple give the item a value of `flex: 1`.

Use case: Content on one div is longer than the other and we want them to have the same height. Old solution would be to use min-height. In flexbox it will work right out of the box.

---

The holy grail layout: Header, 3 columns (navigation, main content, sidebar), footer

The example given in the video is phenomenal, highly recommend downloading the exercise files and using his setup as a baseline.

---

Flexbox was made for layout, float was not

---

Flexbox is really good at handling containers that will have an unknown number of items in them.

# CSS Grid Layout

## Enabling Grid in Various Browsers

CSS grid layout is a newer specification that is still in the process of being developed. It is so new, in fact, that browsers are still working on their implementation.

In Chrome, navigate to

`chrome://flags/#enable-experimental-web-platform-features`

and enable the flag

## Setting up a Grid

All you need to do is set `display: grid;` (or `inline-grid`) in your grid container.

###### Defining Columns & Rows

Need to repeat the `width` and `height` values as many times as we want columns and rows. For example:

```
grid-template-columns: width1 width2;
grid-template-rows: height1 height2 height3 height4;
```

would produce 2 columns and 3 rows.

---

To position an individual item in the grid container, use:

```
grid-column-start: <value>
grid-column-end: <value>
grid-row-start: <value>
grid-row-end: <value>
```

This gives some pretty amazing fine-tuned control in how you position your individual items.

## Auto Grid Features

On the container, there is a `grid-auto-flow` property with a default value of `row`, which orders the items horizontally. You can change this by setting the value to `column` to order the items vertically.

The grid items have an `order` property just like in Flexbox.

## Grid Shorthand

Can use `grid` asa shorthand for `grid-template-row` and `grid-template-column`:

`grid: column column / row row;`

There's a similar shorthand for `grid-row-start`, `grid-row-end`, `grid-column-start` and `grid-column-end`:

`grid-row: row-start / row-end;`
`grid-column: column-start / column-end`

There's even a shorthand for defining all 4 properties at once:

`grid-area: row-start / column-start / row-end / column-end`

---

Alternative syntax for `grid-column`, `grid-row`

`grid-column: 2 / span 2;`
`grid-row: 2 / span 1;`

---

To repeat grid lines, do

`grid-template-columns: repeat(4, 1em 6em);`

This will repeat `1em 6em` four times

## Naming Grid Lines & Grid Areas

Review this section later

## Auto, fr, Overlapping/Layering Items

Review this section later

## Grid Layout & Box Alignment

Review this section later

## Grid Layout in the Real World

Review this section later

# Gotchas, Browser Support & Resources

## Gotchas

If you re-order page elements visually, it can cause strange navigation flows (ex/ switching between images using arrow keys)

Sub grid was not covered in the course because it is at risk of being removed from the spec.

[https://github.com/philipwalton/flexbugs](https://github.com/philipwalton/flexbugs) covers bugs that have been found in Flexbox, as well as their workarounds.

## Browser Support

Fortunately, Flexbox is widely supported in modern browsers.

Browser support for Grid is not quite there yet. Can actually check on [caniuse.com/#search=grid](caniuse.com/#search=grid).

Can use a CSS grid polyfill (found on github) if you really want to use the Grid layout.

## Resources

Rachel Andrew
[http://csslayout.news](http://csslayout.news])
[http://gridbyexample.com](http://gridbyexample.com)
Google "A visual guide to flexbox properties"
Google "Solved by Flexbox"
[https://css-tricks.com](https://css-tricks.com)