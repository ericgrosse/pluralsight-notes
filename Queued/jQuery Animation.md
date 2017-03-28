# Simple jQuery Animation

## Show, Hide and Toggle Methods

Can check if an element is hidden using `$(<selector>).is(':hidden')`

Here's an example for hiding and showing an element by clicking on another element:

```
$('input').click(function() {
	$('#animate').toggle('slow');
});
```

Clearly `toggle` is a very powerful method that you'll probably be using quite often

## Fade Methods

In addition to `hide`, `show` and `toggle` methods, there are associated `fade` methods: `fadeIn`, `fadeOut`, `fadeToggle`

A drawback of using `fade` is that content below what's being faded will jump as soon as the fade finishes. If this is a problem, use `hide/show/toggle` instead.

Author reccommends fade animations for elements that are positioned absolutely.

Can also use `fadeTo` to fade to a specific opacity setting

## Slide Methods

`slideToggle` does the same thing as `toggle` but without the fading. Can decompose `slideToggle` into `slideUp` and `slideDown`. One reason to do this is to give them different durations.

## The `animate()` Method

The `animate` function is used to animate any numerical CSS value. The `hide/show`, `fade` and `slide` functions animate CSS values as well. But with the `animate` function, you specifically select which value you want to animate.

Some common CSS values include `top` and `left` for absolutely positioned elements. It's also common to see the width and height of elements adjusted or animated. We can also do any CSS value such as `font-size`, `border-width`, and dozens of others.

Note that jQuery needs the CSS property names to be provided in camelCase, meaning `font-size` would be specified as `fontSize`.

***

`animate` example:

```
$('img').animate({left: '300px'}, 3000);
```

This makes all the absolute positioned images on the page move `300px` to the right (assuming initial `left: 0`) over 3 seconds.

Can animate more than one property at a time:

```
$('img').animate({top: '50px', left: '300px'}, 3000);
```

## `animate()` Parameters

In the jQuery documentation, there is a chain icon that indicates that a function call can be chained.

`animate` API:

```
.animate( properties [, duration] [, easing] [, complete] )
```

Example:

```
$('#img-1').animate({left: '300px'}, 2000, 'swing', function() {
  $('body').append('We are done!');
});
$('#img-2').animate({left: '300px'}, 2000, 'linear');
```

The last parameter is a callback that's called when the animation finished its execution.

## `animate()` Options

There is another way to use the `animate` function:

```
.animate( properties, options )
```

Example:

```
$('#img').animate({
  left: '300px',
  top: '300px'
},
{
  duration: 2000,
  specialEasing: {
    left: 'linear',
    top: 'swing'
  }
});
```

Another example using the `step` function:

```
$('#img').animate({
  left: '300px'
},
{
  duration: 5000,
  step: function(now, tween) {
    $('p').text(now);
    if (tween.pos > 0.5) {
      $(this).stop();
    }
  }
});
```

In an empty paragraph tag, we display the `left` position value in real-time as the animation progresses. The `tween.pos` condition tells the animation to stop halfway through (note this isn't perfectly exact; my animation stopped at `151.13px`, and it's different every time).

This `step` function is useful for changing an animation's behavior part-way through the animation.

The `progress` function works similarly:

```
progress: function(animation, progress, remainingMS) {
  $('#one').text(progress);
  $('#two').text(remainingMS);
}
```

## Chaining Animations

Chaining example:

```
$('img')
  .hide(500)
  .show(500)
  .animate({left: '400'}, 500)
  .queue(function() {
    $('p').text('Here\'s some text');
    $(this).dequeue();
  })
  .delay(1000)
  .animate({left: '-=200'}, 500);
```

## Stopping Animations

Use `stop` or `finish` on the element being animated.

# Easing

## Easing Definition and Samples

[easings.net](easings.net) displays a huge variery of easing functions

## Easing with jQuery UI

`linear` and `swing` are the only two easings built into jQuery. To get more easing options, use `jQuery-UI` instead.

Example with `jQuery-UI` imported:

```
$('img').animate({left: '500px'}, 3000, 'easeOutBounce');
```

## A jQuery Easing Plug-In

[easings.net](easings.net) includes a jQuery plugin for adding easing animations.

## Creating Custom Easing Functions

Author modifying the jQuery plugin file itself to add custom easing functions.

# Advanced jQuery Animation

Skipped for now

# Creating an Animated Button

Skipped for now

# Creating an Animated Picture Gallery

## Loading and Displaying Images

A lot of initial setup:

`index.html`

```
<div id="gallery"></div>
```

`style.css`

```
#gallery {
  position: relative;
  left: 80px;
  top: 80px;
  background-color: #555;
  border: 2px solid black;
  width: 700px;
  height: 400px;

  perspective: 250px;
  perspective-origin: 50% 50%;
}

.galleryImage {
  position: absolute;
  border: 1px solid black;
}
```

`script.js`

```
$(document).ready(function() {
  for (var i = 1; i <= 12; ++i) {
    var name = i + '.png';
    if (i < 10) {
      name = '0' + name;
    }
    var img = $('<img class="galleryImage" src="/img/' + name + '" />');
    $('#gallery').append(img);
  }

  var g = new PS.Gallery('gallery');
});
```

This appends `png` images with filenames `01` to `12` into `<div id="gallery">`. After that, `var g = new PS.Gallery('gallery');` runs the code in `gallery.js`.

`gallery.js`

```
(function(ns) {

  ns.Gallery = function(id) {

    var gallery = $('#' + id);
    var images = gallery.find('.galleryImage');

    var xPos = 50;
    var yPos = 50;
    var gWidth = gallery.width();

    images.each(function(i, el) {
      var img = $(el);
      img.data('homeX', xPos);
      img.data('homeY', yPos);
      img.data('currentScale', 1);
      img.css({
        'left': xPos,
        'top': yPos,
        'width': '50px'
      });
      xPos += 95;
      if (xPos > gWidth - 100) {
        xPos = 50;
        yPos += 115;
      }
    });
  }

}(window.PS = window.PS || {}));
```

This adds a `PS` object to the global window, and its `Gallery` property is set to the function taking `id` as a parameter.

The data method for jQuery lets you maintain key value pairs that get assigned to a DOM element. This is really useful because we don't have to create a new object and store all of these variables somewhere else.

## Mousing Over Images

Add hover functionality to the gallery:

```
img.hover(
  function (event) {
    var target = $(event.target);
    bringImageToTop(target);
    animateImage(target, 500, target.data('homeX'), target.data('homeY'), 2);
  },
  function (event) {
    returnAllToNormal();
  }
);
```

This relies on a bunch of helper functions, shown below:

```
var animateImage = function(img, duration, left, top, scale) {
  img.stop();
  img.css('textIndent', img.data('currentScale'));
  img.animate({
    'left': left,
    'top': top,
    'textIndent': scale, // use textIndent css property for scaling
  },
  {
    duration: duration,
    step: function(now, fx) {
      if (fx.prop === 'textIndent') {
        // scale
        img.data('currentScale', now);
        img.css('transform', 'scale(' + now + ')');
      }
      else if (fx.prop === 'left') {
        img.css('left', now);
      }
      else if (fx.prop === 'top') {
        img.css('top', now);
      }
    }
  });
};
```

We stop the current image animation, set a dummy `textIndent` css property to the image's current scale, then animate the image with the `left`, `top` and `scale` parameters passed into the function.

The step function updates the `left`, `top` and `scale` properties throughout the animation.

```
var returnAllToNormal = function() {
  images.each(function(i, el) {
    var img = $(el);
    animateImage(img, 500, img.data('homeX'), img.data('homeY'), 1);
  });
};
```

This function returns the image to its original state, with scale set to 1.

```
var bringImageToTop = function(img) {
  images.each(function(i, el) {
    $(el).css('z-index', 0);
  });
  img.css('z-index', 1);
};
```

This function ensures that the currently hovered image has a higher `z-index` than the other images in the gallery.

## Animating Images Away from the Mouse

When we hover over an image, it would be a cool effect to shift the other images in the gallery to make room for it.

Add a helper function to do this:

```
var avoidImage = function(target) {
  images.each(function(i, el) {
    var img = $(el);
    if (img[0] !== target[0]) {
      var xdiff = img.data('homeX') - target.data('homeX');
      var ydiff = img.data('homeY') - target.data('homeY');

      var adj = 40;
      var xAdjust = (xdiff > 0) ? adj : (xdiff < 0) ? -adj: 0;
      var yAdjust = (ydiff > 0) ? adj : (ydiff < 0) ? -adj: 0;

      if (yAdjust !== 0) {
        xAdjust = 0;
      }

      var newX = img.data('homeX') + xAdjust;
      var newY = img.data('homeY') + yAdjust;

      animateImage(img, 500, newX, newY, 1);
    }
  });
};
```

The idea is to iterate through each image, compare the currently iterated image's position to the target image, and adjust accordingly.

`if (img[0] !== target[0])` compares the DOM `img` to the DOM `target` (instead of their jQuery wrapped counterparts).

