# CSS Transition Fundamentals

## Browser Support and Vendor Prefixes

-webkit- (Chrome, Safari)
-moz- (Mozilla Firefox)
-o- (Opera)
-ms- (Microsoft Internet Explorer / Edge)

All modern browsers including IE10 support CSS3 transitions, so you don't really need to use the vendor prefixes.

## Demo: Blue to Green

Not a bad idea to put your HTML and CSS side-by-side in a 2 column view in Sublime

Creating a color transition when hovering on an element is as simple as the following:

```
.square {
	width: 250px;
	height: 250px;
	margin: 1rem 0 2rem 1rem;
	background-color: blue;	
}

#color .square {
	transition-property: background-color;
	transition-duration: 0.8s; 
}

#color .square:hover {
  background-color: green;
}
```

## Demo: Square to Circle

The following makes a blue square transition into a purple circle:

```
.square {
	width: 250px;
	height: 250px;
	margin: 1rem 0 2rem 1rem;
	background-color: blue;
}

#circle .square {
	transition-property: border-radius, background-color;
	transition-duration: 0.8s, 1s;
}

#circle .square:hover {
  border-radius: 50%;
  background-color: purple;
}
```

## Using the Shorthand

With the shorthand property, you can specify the transition property, its duration and its delay all at once.

```
#circle .square {
	transition: border-radius 0.8s, background-color 1s;
}
```

# CSS Transition and 2D Transform

## Introduction to 2D Transform

The Transform Methods
- Skew(x, y) or SkewX(x) or SkewY(y)
- Scale(w, h)
- Rotate(angle)
- Translate(x, y) or TranslateX(x) or TranslateY(y)

# The `skew()` Method

```
.square {
	width: 150px;
	height: 150px;
	background: blue;
	position: relative;
}

#skew .square {
  background-color: purple;
  transition: transform 0.7s;
}

#skew .square:hover {
	transform: skewX(-20deg);
}
```

## The `scale()` Method

```
#scale .square div {
   background: #000;
   position: absolute;
   left: 25%;
   top: 25%;
   width: 75px;
   height: 75px;
   transition: transform 0.7s;
}

#scale .square div:hover {
	transform: scale(2,2);
}
```

## The `rotate()` Method

```
#rotate .square {
  background-color: tomato;
  transition: transform 2s;
}

#rotate .square:hover {
	transform: rotate(720deg);
}
```

This animation is the greatest thing ever!

## The `translate()` Method

```
#translate .square {
	background-color: orange;
	transition: transform 2s;
}

#translate .square:hover {
	transform: translateX(900px);
}
```

Can use multiple transforms at the same time. Ex/

`transform: translateX(900px) rotate(360deg)`

## Using Transition-timing-function

- linear (same speed from start to finish)
- ease (starts slow, ends slow)
- ease-in (starts slow, ends fast)
- ease-out (starts fast, ends slow)
- ease-in-out (starts slow, ends slow)

Note that `ease` and `ease-in-out` are different in their middle behavior

In my opinion, `ease-in-out` is the nicest looking animation

Transition timing properties are added as the 3rd parameter after transform. Ex/

`transition: transform 6s 1s ease-in;`

Transitions as a transform with a duration of 6 seconds, a delay of 1 second and an ease-in timing.

## Tutorial: The Bike Rider

Instead of using transition timing functions such as ease-in, try out `cubic-bezier`. You can preview this effect at [cubic-bezier.com](cubic-bezier.com). This site is fucking awesome.

# Transforming and Animating in 3D

## Introduction

- Perspective
- Perspective-origin
- Transform-origin
- Transform-style
- Backface-visibility

## Perspective

Perspective is what gives the illusion of depth in a 2-D image, and is based on the vanishing point.

[https://desandro.github.io/3dtransforms/examples/perspective-03.html](https://desandro.github.io/3dtransforms/examples/perspective-03.html) to experiment with perspective

Changing the perspective-origin affects the positioning of the vanishing point.

## CSS 3-D Transform

Adds a property `translateZ()` and a shorthand `translate3d(x, y, z)`, same idea for `rotate` and `scale`

The `transform-origin` property affects where your element lands after the animation completes (also seems to affect the placement of your rotation axis)

## 3D Transitions Demonstration

You must always establish a perspective in order to transform in 3-D space

Also need to use a property `transform-style: preserve-3d;`

## Flipping the Pictures

Awesome demo, you'll probably want to experiment with the source code at some point

## Creating the 3D Cube

`transition: all` applies a transition to any CSS property that changes

## Animating the 3D Cube

The final demo uses images for each side of the cube, it looks amazing when you use `Backface Hidden`. This is truly innovative design.

# Final Project- Whistler White Ski Resort

## Designing with 2D Transform

Look into CSS3 Foundation and Foundation Icons

Remember that you can use transforms without transitions. Sometimes it is useful just to have an element displayed as rotated at a certain angle, for instance.

## Animating the Navigation Links

Google for a site called "Creative Link Effects". The effects are goddamn incredible.

Really cool effect: Place text as a caption in an image with opacity 0, and transition its opacity to 1 when you hover over the image.

Other cool effects on image hover: Translate header text, scale the image to be larger, add a box shadow

## Gallery Pictures: Rotation in 3D

The property `transform-style: perserve-3d;` is used to allow the element's children to use 3D effects.

Cool image effect: Rotate the image horizontally on hover, and maybe have the backface display some text

## Contact Us with an Original Button Link

Google "Creative Button Styles", every bit as useful as "Creative Link Effects"