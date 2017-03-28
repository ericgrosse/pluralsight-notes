# Responsive In-Browser Web Page Design with HTML and CSS

## Analyzing Oue Responsive Boilerplate Code

A client might send you a file called a design brief.

Good idea to include a file called `reset.css`, as this is important for normalizing styles across all browsers.

Also a good idea to apply a universal styling of `box-sizing: border-box`. The purpose of this is to create element spacing that ignores padding. When using `float:left` to create a grid system, only horizontal margins will potentially mess up the layout.

In addition to having a third-party `reset.css` file, can begin your `main.css` file with your own custom resets.

Other things to do at the beginning of your `main.css` file:
- Universal styles
- Helper classes such as `.float-left`, `.float-right`, `.font-small` (typography), `.vertical-center`, etc
- Classes for your custom grids and containers

Our bootstrap equivalent grid classes (`container-xs`, `container-sm`, etc) work by setting a `max-width` equal to the `min- width` used in the media queries at the end of the `main.css` file.

---

The `.grid-parent` class uses negative `15px` margins to counteract existing padding
The `.grid-child` class uses `15px` padding to add "gutters" between grid items

## Wireframing Our Responsive Site

For anchor tags, try using the CSS property `color: inherit` to maintain the same text color as the parent.

---

For placeholder images, try using

`<img src="http://placehold.it/300x300" />`

You can put whatever dimensions you want in the url

## Mocking up Our Team Page with Inline-block Grids

Currently here

