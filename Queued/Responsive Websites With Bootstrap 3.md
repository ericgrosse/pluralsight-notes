# Responsive Patterns Using Bootstrap

In the Chrome dev tools, you can toggle device mode (the little phone icon or `ctrl + shift + m`) to experiment with different viewport sizes. In fact, you can select which device to emulate. Always need to refresh when using this emulation view.

## Introduction

Lukew's website covers some useful multi-device layout patterns

## Bootstrap's Grid and Visual Breakpoints

`.container` vs `.container-fluid`: The difference is `container-fluid` always takes up the full width of the browser.

Bootstrap uses 4 visual breakpoints:
- Extra small devices (xs): `<768px`
- Small devices (sm): `>= 768px`
- Medium devices (md): `>= 992px`
- Large devices (lg): `>= 1200px`

These values are not completely arbitrary. For example, `768px` is the width of an iPad in portrait mode

The `.col` rules cascade from lowest viewport size to highest, and if no rule is set, it will default to the full width of the screen.

Ex/ `.col-sm-4 .col-lg-2` This will take up the full width of the screen for small devices (since it isn't set), will take up 4 grid columns in the small and medium devices (because of cascade) and 2 grid columns for large devices (because of the override).

## Setting Up a Multi-Column Page

Using `container-fluid` on the header and footer looks pretty nice with the following CSS addition:

```
header, footer {
	background-color: #333; // gray-ish black color
    color: #ddd; // gray-ish white color
    padding: 20px;
    text-align: center;
}
```

Author uses `aside`, `section` and `aside` elements to structure the 3-column layout of his page.

---

Even if you setup your column behavior the way you want for mobile devices, the page won't display properly on a mobile device without one of the following `meta` tags:

`<meta name="viewport" content="width=device-width, initial-scale=1">`

Or to disable zooming as well:

`<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">`

## Markup and Styling for Our Website

Lazy way to have responsive images:

```
img {
	width: 100%;
    height: 100%;
    max-width: <width of image>;
    max-height: <height of image>;
}
```

The author has a very nice `box-shadow` for his article images:

```
article img {
	width: 100%;
    height: 100%;
    text-align: center;
    box-shadow: 6px 6px 8px #777;
    margin-bottom: 20px;
}
```

## The Mostly Fluid Pattern

When emulating an iPad, you can change from portrait to landscape mode by pressing a button that swaps dimensions. Can also manually type in viewport sizes.

Use `col-sm-push` and `col-sm-pull` can be used to re-arrange columns, which is important if you make major layout changes when changing viewport size.

Ex/

`<section class="col-sm-8 col-sm-push-2">...</section>`
`<section class="col-sm-2 col-sm-pull-8">...</section>`

This essentially swaps the two columns when hitting a viewport size of `sm` or larger.

Push and pulls are always done from the left side of the page.

## The Layout Shifter Pattern

Use `visible-md` and `hidden-md` classes to toggle visibility of a row. This is useful for shifting a horizontal layout to a vertical one and vice versa.

Benefits: Flexibility of significantly changing the layout without using JavaScript
Drawbacks: Duplicate code

Currently on: The Content Reflow Pattern (2:36/4:36)