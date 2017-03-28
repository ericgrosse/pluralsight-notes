# Introduction to jQuery UI

## Getting Started

When you download jQuery UI, you can use select & de-select checkboxes based on the components you want to use, resulting in a smaller script size.

Need 3 lines of code to run JQuery UI
- Link element for `css/ui-lightness/jquery-ui`
- Script element for `js/jquery`
- Script element for `js/jquery-ui`

## Styles and Themes

If you go on jQuery UI's official website, you'll see a `Themes` link in the navbar. In here you will find the theme roller. On this page we can essentially  built a custom CSS file for use with jQuery UI.

In the theme roller, there's a `Gallery` tab that shows you pre-defined themes you can select from.

Personally I like the following styles in the gallery:
- Smoothness
- Start
- Overcast
- Eggplant
- Dark Hive
- Cupertino
- **Excite Bike**
- **Dot Luv**
- **Black Tie**

Once you've picked a theme and downloaded it, place the folder next to the `ui-lighntess` folder in the `css` directory.

In your links tag, you would actually replace the reference to `ui-lightness` with the reference to your custom CSS (ex/ vader)

## Options

The first parameter for every jQuery UI method is an options object. Ex/

`$('#accordion').accordion({ header: 'h3' });`

In the documentation for each Widget, you'll see the documentation categorized into `Options`, `Methods` and `Events`.

---

A modeless dialog is a dialog window that doesn't prevent you from interacting with the rest of the page.

## Events

These are custom event handlers for actions such as `create`, `focus`, `resize`, etc. Simply use the name of the event as a property name in the options parameter, and its associated function is the callback for the event. For example:

```
$('.selector').dialog({
	beforeClose: function(event, ui) {}
});
```

## Methods

You can pass in a string to your widget, and this will fire the method associated to that string. For example:

```
$('#dialog-link').click(function(event) {
    $('#dialog').dialog('open');
    event.preventDefault();
});
```

This would trigger the dialog widget's `open` method.

# jQuery UI Widgets Part 1

## Dialogs and AutoOpen

To have a dialog that only opens when a button is clicked:

```
$(function() {
    $('#dialog').dialog({
    	autoOpen: false
    });
    $('#openDialog').click(function() {
    	$('#dialog').dialog('open');
    });
});
```

## Dialogs and Buttons

One of the cool options for the dialog widget is the buttons options:

```
buttons: {
    'Yes': function() {
        $('#dialog').dialog('close');
        $('#dialogDecision').text('Action confirmed');
    },
    'No': function() {/* Another callback implementation */}
}
```

## 3 2 1 Countdown

Here's an example of a progress bar that decrements by 1 from 100 every 25 ms:

```
var value = 100;

$('#progress').progressbar({
    value: value
});

countdown();

function countdown() {
    --value;
    $('#progress').progressbar('option', 'value', value);
    $('#countdown').text(value);

    if (value > 0) {
        setTimeout(countdown, 25);
    }
    else {
        $('#countdown').text('completed');
        $('#progress').hide();
    }
}
```

The key is to invoke `progressbar` repeatedly using `setTimeout`.

## Slider

Here's a slider that sets the opacity of the element before `<div id="slider></div>`

```
$('#slider').slider({
    min: 0,
    max: 100,
    value: 100,
    slide: function(event, ui) {
        $('#slider').prev().css({ opacity: ui.value / 100 });
    }
});
```

## Tabs

HTML

```
<div id="tabs">
    <ul>
        <li><a href="#tab-1">C#</a></li>
        <li><a href="#tab-2">LINQ</a></li>
        <li><a href="#tab-3">jQuery UI</a></li>
    </ul>
    <div id="tab-1">
        <p>123 dupree</p>
    </div>
    <div id="tab-2">
        <p>456 candle sticks</p>
    </div>
    <div id="tab-3">
        <p>789 pine</p>
    </div>
</div>
```

JS

```
$('#tabs').tabs();
```

## Dynamic Bonus Content

The content of a tab can be an entire html page loaded from the server

# jQuery UI Widgets Part II

## The Accordion

HTML

```
<h3>The Accordion</h3>
<div id="accordion">
    <h3><a href="#">C#</a></h3>
    <div>123 dupree</div>
    <h3><a href="#">LINQ</a></h3>
    <div>456 candle sticks</div>
    <h3><a href="#">jQuery UI</a></h3>
    <div>789 lime</div>
</div>
```

JS

```
$('#accordion').accordion();
```

Interesting options: `heightStyle`, `collapsible`

## The Autocomplete

**This is a pretty awesome feature**

HTML

```
<div>
    <label for="search">Search:</label>
    <input id="search" type="text"/>
</div>
```

JS

```
var classes = [
    'C#',
    'Entity Framework',
    'LINQ',
    'ASP.NET MVC',
    'jQuery UI',
    'CSS',
    'Ruby',
    'jQuery',
    'JavaScript',
    'Test driven development',
    'Design patterns'
];

$('#search').autocomplete({
    source: classes
});
```

Autocomplete is using a regular expression search and displaying the first 4 results it finds. You can hook up the autocomplete search to a remote data source if you'd like.

## Working with Remote Data

Interesting options:

```
$('#search').autocomplete({
	source: 'autocompletejson.ashx',
    minLength: 2,
    delay: 1500
});
```

## The Button and the Sprite

The button widget can be used to give different button types the same appearance.

HTML

```
<div id="buttons">
    <button>A button element</button>
    <input type="submit" value="An inut submit"/>
    <a href="#">An anchor button</a>
</div>
```

JS

```
$('#buttons').children().button();
```

In my opinion this is kind of pointless since you can do the same with bootstrap using pure CSS.

## Radios and Checkboxes

You can transform the appearance of a group of radio buttons or checkboxes into buttons.

HTML

```
<div id="radios">
    <input type="radio" id="language1" name="language"/><label for="language1">JavaScript</label>
    <input type="radio" id="language2" name="language"/><label for="language2">C#</label>
    <input type="radio" id="language3" name="language"/><label for="language3">C++</label>
</div>

<div id="checks">
    <input type="checkbox" id="paradigm1" name="paradigm"/><label for="paradigm1">Object Oriented</label>
    <input type="checkbox" id="paradigm2" name="paradigm"/><label for="paradigm2">Functional</label>
    <input type="checkbox" id="paradigm3" name="paradigm"/><label for="paradigm3">Procedural</label>
</div>
```

JS

```
$('#radios').buttonset();
$('#checks').buttonset();
```

Again, seems pointless since you can do the same with Bootstrap

## The Datepicker

This is another awesome widget for showing a calendar, and highly customizable

HTML

```
<div>
    <input type="text" id="dateselection"/>
</div>
```

JS

```
$("#dateselection").datepicker({
    numberOfMonths: 2,
    showWeek: true,
    changeMonth: true,
    changeYear: true,
    showButtonPanel: true,
    minDate: new Date(2008, 1 - 1, 1),
    maxDate: new Date(2012, 12 - 1, 31)
});
$("#dateselection").datepicker("setDate", new Date(2011, 6 - 1, 11));
```

# Interactions with jQuery UI

## Draggables

Draggables work by adding an inline style with `position: relative` and `top` and `left` properties varying based on your mouse position.

## Snapping, Stacking, Handles, Helpers

To make sure that your currently dragged element always appears over other draggable elements, use the option `stack: '.ui-draggable'`

If you add the option `helper: 'clone'`, you will drag a copy of the draggable element while the original element stays in place.

## Accepting Elements

`revert: 'invalid'` will make the element you're dragging travel back to its original position when you let go of the mouse

## Summary

Got lazy with the note taking towards the end, just read the site's documentation and play around with the options

# jQuery UI Effects

## Colors

jQuery UI adds functionality on top of jQuery's `animate` method. For example, jQuery `animate` cannot be used to change `background-color`, whereas it can with jQuery UI's `animate`.

CSS

```
body {
	background: #eee;
}

#target {
	$dimensions: 200px;
	width: $dimensions;
	height: $dimensions;
	background: black;
	border: 5px solid white;
	border-radius: 10px;
	margin: 10px;
}
```

JS

```
$('#target').click(function() {
	$(this).animate({
		width: '+=20',
		height: '+=20',
		'background-color': 'red'
	});
});
```

## Effects

Example of an effect:

```
$('#target').click(function() {
	$(this).effect('explode',
		{ pieces: 16 }, 'slow', function() {});
});
```

Note that you can use the effect name in other places. For example:

```
$('#trash').droppable({
	activeClass: 'opaque',
    drop: function(event, ui) {
    	ui.draggable.hide('explode'); // Effect is used here
    }
});
```

## Easing

Easing example:

```
$('#target').click(function() {
	$(this).animate({
		left: 1000
	}, 1000, 'easeInOutElastic');
});
```

Note that the above example depends on `<div id="target"></div>` having the CSS property `position: relative` set.

## CSS Transitions

Using `addClass` (or one of its variants like `toggleClass`) with an additional parameter for the animation speed triggers the same effect you get from native CSS animations! Example:

CSS

```
body {
	background: #eee;
}

.target {
	$dimensions: 100px;
	width: $dimensions;
	height: $dimensions;
	background: black;
	border: 5px solid white;
	border-radius: 10px;
	margin: 10px;
	position: relative;
	left: 0px;
}

.newTarget {
	$dimensions: 200px;
	background: #990000;
	width: $dimensions;
	left: $dimensions;
	border: 10px solid black;
}
```

JS

```
$('.target').click(function() {
	$(this).toggleClass('newTarget', 'slow');
});
```