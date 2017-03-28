# Introduction to SVG

Inkscape mentioned as an alternative to Illustrator and Sketch.

## How to Create an SVG

You can create an SVG by:

- Hand coding
- Using popular vector image editors

When saving an SVG in illustrator, you're presented with an SVG Options window. For the field `Type` the option `Convert to outline` creates vector paths of the characters so that they no longer require fonts. This is acceptable if accessibility of the text isn't a concern.

In the `Image Location` field, the difference between the `Embed` and `Link` options is a bit confusing

When we're working with SVG code, we can simply hit the `SVG Code` button and copy/paste the resulting code.

Creating SVGs by hand can prove to be a significant challenge for more complex graphics. But for simpler ones, it can be a more efficient method.

Example:

```
<svg height="100" width="100">
    <circle cx="50" cy="50" r="50" fill="#ff0000"></circle>
</svg>
```

This creates a red circle whose center-x, center-y and radius are all 50.

## How to Use SVGs

- Inline svg
- `<img src="images/svg.svg" alt="svg" />`
- `<embed src="images/svg.svg type="image/svg+xml" />`
- `<object data="images/svg.svg" type="image/svg+xml"></object>`
- `<iframe src="images/svg.svg" height="200" width="200" scrolling="no" style="border: none"></iframe>`

Advantage of `<object>` tag: Still able to manipulate the SVG with CSS and JavaScript, but the code for the graphic remains separated out into its own location. Also, a fallback image can be set directly within the object tag itself, so browsers that don't support it will automatically render that image.

```
<object data="images/svg.svg" type="image/svg+xml">
	<img src="images/fallback.png" alt="Fallback image" />
</object>
```

Can also use fallbacks in the `iframe`.

# Workin with SVG Code

## Introduction to SVG Layout

SVG layout is much different than the CSS layout and box model that we're used to. Everything is positioned based off a coordinate system.

## The viewBox

The viewBox consists of four values in a specific order, x coordinate, y coordinate, width and height. Example:

```
<svg width="300" height="100" viewbox="0, 0, 600, 200" style="border: 1px solid red">
    <circle cx="150" cy="50" r="25" fill="#ff0000"></circle>
</svg>
```

Notice the viewbox width and height are double the svg width and height. This has the effect of cutting the pixel dimensions in half. For example, can think of the circle `cx` and `cy` coordinates as being a fraction of the viewbox dimensions. In this case, 150/600 = 50/200 = 1/4, meaning the circle is positioned at `(x_center/4, y_center/4)`.

Another way to think of it: svg width and height give absolute dimensions while viewbox gives relative dimensions, which affect the dimensions used by svg components such as circle.

## preserveAspectRatio

Suppose you use:

```
<svg width="300" height="100" viewbox="0, 0, 600, 100" style="border: 1px solid red">
    <circle cx="150" cy="50" r="25" fill="#ff0000"></circle>
</svg>
```

Now there's a 1:2 ratio with the svg width and viewport width, and a 1:1 ratio with the svg height and viewport height. This has the effect of vertically centering the circle while the x position is still the same. What's interesting is that the circle doesn't get distorted at all.

If you actually want the distortion, add an svg attribute `preserveAspectRatio="none"`.

Know the difference between the preserveAspectRatio options `meet` and `slice` and its 10 possible align values.

## An Introduction to SVG Elements

Commmon SVG graphical elements that you're likely to run into are circle, ellipse, line, path, polygon, polyline, rect, text, and use.

Common SVG container elements that you're likely to see are defs, g, symbol and svg.

SVG has two types of gradient elements, linearGradient and radialGradient.

One method to animate an SVG is to use SVG animation elements, which are: animate, animateMotion, animateTransform, and set.

## SVG Graphical Elements

`<ellipse>` is identical to circle except that you need to provide two radii, `rx` and `ry`.

`<rect>` requires an x and y coordinate, as well as a width and height. Can also add an `rx` and `ry` attribute to round the corners (similar to border-radius in CSS).

`<line>` requires `x1`, `y1` and `x2`, `y2` attributes, as well as a `stroke` and optional `stroke-width` attribute.

The `<polyline>` element is like a collection of connected line elements. The coordinates are defined in a points attributes, which has the form `x1, y1, x2, y2, x3, y3`, and so on. Make sure to also use `fill="none"`.

The `<polygon>` element is identical to the `<polyline>` element except that it automatically connects the first and last co-ordinates to form a closed shape.

The `<path>` element: All of these basic shape elements can be created with a path element. The path can contain a set of points, lines, and arcs. Paths have a path data attribute defined as a D attribute. This path data attribute can contain `moveto`, `line`, `arcs`, `cubic`, and `quadratic` beziers, and closepath instructions.

###### `<path>` Specific Notes

- Moveto is noted with the letter M. It's essentially the starting point for the path.
- Lineto is noted with the letter L and it's used to draw straight lines from one point to another.
- Arcs and cubic and quadratic bezier curves are fairly complex, and it's unlikely that you'd want to create them by hand.
- Closepath instructions are noted with the letter Z. It connects the current point to the initial point with a straight line, essentially closing the path.

The `<text>` element is used to add fully accessible text within an SVG graphic. Example:

```
<svg width="300" height="300">
    <style>
        text { font-family: 'Myriad Pro', sans-serif; }
    </style>
    <rect x="0" y="85" fill="#FF742B" width="300" height="130" />
    <text x="70" y="175" fill="#FFFFFF" font-size="72">TEXT</text>
</svg>
```

`<use>`: The use element is an extremely powerful part of SVG. It's used to reference other SVGs or SVG elements that can be reused and render them in a given location, promoting re-use of graphics wherever possible.

Example:

```
<svg width="300" height="300">
    <!-- Item for reuse -->
    <defs>
        <g id="itemForReuse">
            <circle cx="50" cy="50" r="50" />
        </g>
    </defs>

    <!-- Item Use #1 -->
    <use xlink:href="#itemForReuse" x="0" y="0" fill="#FF742B" />

    <!-- Item Use #2 -->
    <use xlink:href="#itemForReuse" x="120" y="120" fill="#A2CE5D" />
</svg>
```

This draws 2 circles of different color and positions within the svg.

## SVG Container Elements

- `defs`: The `defs` element is used to contain elements that are intended for re-use. Anything within the `defs` element cannot actually be rendered until referenced by some other object, like a `use` element.
- `g`: The `g` element is used to group items. We may want to animate a collection of shapes or make sure that all their fill colors are made the same with CSS. That's where the `g` element comes in handy, because it allows us to group these items and then give the group an ID or class, which we can use to animate or style with CSS.
- `symbol`: The `symbol` element is used to store graphics that are meant to be used multiple times. Its contents are note rendered to the page until it's referenced from a `use` element. The added bonus `symbol` provides us is that it can contain both its own viewBox and preserveAspectRatio attributes.

Example of `<symbol>`:

```
<svg width="300" height="300">
    <!-- Symbol for reuse -->
    <symbol id="symbolForReuse" viewBox="0 0 500 500">
        <circle cx="50" cy="50" r="50" />
    </symbol>

    <!-- Item Use #1 -->
    <use xlink:href="#symbolForReuse" x="0" y="0" fill="#FF742B" />

    <!-- Item Use #2 -->
    <use xlink:href="#symbolForReuse" x="120" y="120" fill="#A2CE5D" />
</svg>
```

Not super clear on the difference between `defs` and `symbol`.

## SVG Gradient Elements

Gradient example:

```
<svg width="300" height="300">
    <!-- Gradient -->
    <defs>
        <linearGradient id="gradient" x1="0" x2="0" y1="0" y2="1">
            <stop offset="0%" stop-color="#FCC1A7" />
            <stop offset="100%" stop-color="#F16823" />
        </linearGradient>
    </defs>

    <!-- Rectangle with gradient fill -->
    <rect fill="url(#gradient)" x="0" y="0" width="300" height="200" />
</svg>
```

There are two types of gradients built into the SVG speck, linearGradient and radialGradient. These gradients are not rendered on their own, they're meant to be referenced and are defined within a `defs` section. They require an ID so that they can be referenced by other elements. They contain stops, which lets the gradient know what color it should be at certain points.

Here's an example of using CSS for the gradients:

```
<svg width="300" height="300">
    <!-- Gradient -->
    <defs>
        <linearGradient id="gradient" x1="0" x2="0" y1="0" y2="1">
            <stop class="stop01" offset="0%" />
            <stop class="stop02" offset="100%" />
        </linearGradient>
    </defs>

    <!-- CSS -->
    <style type="text/css">
        .rect01 { fill: url(#gradient); }
        .stop01 { stop-color: #FCC1A7; }
        .stop02 { stop-color: #F16823; }
    </style>

    <!-- Rectangle with gradient fill -->
    <rect class="rect01" x="0" y="0" width="300" height="200" />
</svg>
```

The `linearGradient`'s `x1, y1, x2, y2` values are used to control the direction that the gradient flows.

## SVG Animation Elements

SMIL stands for Synchronized Multimedia Integration Language, which is a specification for animation within XML documents.

The `<animate>` element will animate a single attribute or property of another element over time. Example:

```
<svg width="300" height="300">
    <rect x="0" y="0" width="300" height="200">
        <animate attributeName="x" from="-300" to="300" dur="2s" repeatCount="indefinite" />
    </rect>
</svg>
```

***

The `<animateMotion>` element uses the path data from a given path element to create a motion path for an object to move along. Example:

```
<svg width="300" height="300">
    <!-- Motion Path -->
    <path id="motionPath"
        d="M-141.333, 218C-60, 138.667, 20, 39.333, 74.667, 45.333s244.667, 200, 244.667, 200"
        stroke="#ccc" stroke-width="3" fill="none" />
    <!-- Animated Item -->
    <circle cx="" cy="" r="40">
        <animateMotion dur="1.2s" repeatCount="indefinite">
            <mpath xlink:href="#motionPath" />
        </animateMotion>
    </circle>
</svg>
```

***

The `<animateTransform>` element animates the translation, scaling, rotation, or skewing of another element over time.

When trying to animate the value of an attribute not covered by `animate`, `animateMotion`, or `animateTransform`, we can use `set`. This element will simply set a value for the attribute to animate to over a duration of time. Example:

```
<svg width="300" height="300">
    <circle cx="150" cy="150" r="10">
        <set attributeName="r"
            to="100"
            begin="1s"
            fill="freeze"
        />
    </circle>
</svg>
```

This changes the circle's radius from 10 to 100 after one second has passed.

# D.R.Y. Out Inline SVG Code with SVG "Sprites"

## Inline SVG Sprites, Using SVG Fragment Identifiers

Advanced concept, may want to revisit if you end up using SVGs a lot.

## Embedding SVGs as Backgrounds in CSS

[Grumpicon](http://www.grumpicon.com) is a web application that allows you to drag your SVG graphic files into their interface, then they perform some magic on them by generating the necessary CSS for each graphic. It even generates fallback PNG files for when SVG is not supported.

Author's preferred method is the inline SVG method because it provides the most flexibility.

# Animating SVG

## Introduction

Three main methods for SVG animation:
- CSS
- SMIL
- JavaScript

Be sure to look at the module 4 exercise files, as they contain 7 different example files you can look at (and learn from via the source code).

## Animating SVG with CSS

Certain CSS properties are interpreted differently, or not at all in different browsers. Therefore CSS animations should only be used for more simple animations, things like animating opacity, because once you start to get too complex, you're going to run into trouble.

Another issue with CSS animations is that we are limited to the properties that CSS provides. For example, there's no way of animating path data in CSS. One last issue with using CSS animations for SVG is that if we're using the image method to display our SVG, attempting to use an external stylesheet for the animation will simply not work.

## Animating SVG with SMIL

Fortunately SVG has its own set of animation elements as we saw earlier.

Amusingly, SMIL is not supported at all in IE.

## SVG Animation with JavaScript Manipulation & Animation Libraries

Using a JavaScript manipulation or animation library to animate SVG can provide much of what we've already seen and more, with the added benefit of great cross-browser support.

Snap.svg is know as the jQuery of SVG. It is capable of generating SVG or working with existing SVG code that you may already have.

Vivus is a nice SVG animation library that has been designed to give SVGs the effect of being hand-drawn. The vivus effect works best with line art since it's basically animating strokes on paths.

D3, or Data-Driven Documents, is used to bring data visualizations like charts and graphs to life by binding arbitrary data to the DOM and applying data-driven transformations to elements.

# SVG Optimization, Browser Support, Resources, and Examples

## SVG Optimization

This covers options for reducing the file size of an SVG. Could be worth revisiting in the future.

## Browser Support

In order to make an SVG (or any image) responsive, give it a CSS `width: 100%` and `height: auto`. Hilariously, this doesn't work in IE, where you instead have to use a padding hack.

## Tools, Resources & Examples

- [http://responsiveicons.co.uk](http://responsiveicons.co.uk)
- [http://codepen.io/betravis/pen/yyLYYd](http://codepen.io/betravis/pen/yyLYYd)
- [http://tympanus.net/Development/ElasticSVGElements/](http://tympanus.net/Development/ElasticSVGElements/)
- [http://tympanus.net/Tutorials/ShapeHoverEffectSVG/](http://tympanus.net/Tutorials/ShapeHoverEffectSVG/)

Other resources:

- [svg-news.com](svg-news.com)
- [smashingmagazine.com](smashingmagazine.com)
- [http://picsvg.com](http://picsvg.com) (converts bitmap to SVG, incredible!)
- [svgcircus.com](svgcircus.com) (app that allows you to visually created looped animations using svg!)
- [http://sarasoueidan.com/tools/circulus](http://sarasoueidan.com/tools/circulus)
- [grumpicon.com](grumpicon.com)