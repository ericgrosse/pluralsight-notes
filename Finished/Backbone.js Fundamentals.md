## Backbone local storage plugin

In order to use this plugin, need to make 2 changes: First, import this script

`<script src="https://raw.github.com/jeromegn/Backbone.localStorage/master/backbone.localStorage.js"></script>`

Then in your collection, replace the url with a localStorage object

###### Working example

```
var Person = Backbone.Model.extend({}),
  People = Backbone.Collection.extend({
      model: Person,
      localStorage: new Backbone.LocalStorage('People')
  });

var people = new People([{name: 'John', age: 19}, {name: 'Sarah', age: 64}]);
var john = people.at(0);
var sarah = people.at(1);

people.at(0).save();
people.at(1).save();

people.fetch({
  success: function () {
      console.log(JSON.stringify(people));
  }
});
```

You can check what’s being saved by checking the Resources tab then `Localstorage -> file://`

## Server related stuff

For the backbone server, can either use a transient backbone server
https://github.com/liammclennan/resourceserver
Or write a specialized api with asp.net mvc web api, ruby on rails, node.js etc

In the former case, do a git clone of the repository, navigate to the directory, do npm install then npm start. The server is now running on localhost:3002.

***

`people.create({name: 'Tom', age: 50});`

Saves to the server and adds to the collection
Backbone does this through a POST request
A successful request leads to a response with status code 200 and a payload with the id

***

`people.fetch()`

Fetch the collection from the server
In the case of a model, reset the model’s state from the server

***

`person.save()`
Create or update depending upon person.isNew()
Create is the same as collection.create()

***

`person.destroy()`
Deletes the model on the server and remotes it from its client-side collection

***

Example of a model fetch:

```
var Person = Backbone.Model.extend({}),
  People = Backbone.Collection.extend({
      model: Person,
      url: 'http://localhost:3002'
  });

var people = new People();
var person = new Person({id: 0});
people.add(person)
person.fetch({
  success: function () {
      console.log(JSON.stringify(person));
  }
});
```

Note that we added the person model to the people collection in order to give the person model access to the REST url. Otherwise you would get an error “url” property or function must be specified.

Also note that we’re creating an empty person model and then populating it with the data from the server.

***

Example of a `person.save()`

```
var Person = Backbone.Model.extend({}),
  People = Backbone.Collection.extend({
      model: Person,
      url: 'http://localhost:3002'
  });

var people = new People();
var person = new Person({name: 'Joe', age: 55});
people.add(person);
person.save();
```

***

In a separate command line window in the resourceserver directory, with the server running, type in:

`curl -vX POST http://localhost:3002/people -H "Content-Type: application/json" -d '{"name": "Bob", "age": 24}'`
`curl http://localhost:3002/people // {"name": "Bob", "age": 24}`

***

Backbone.js is not a framework
It is not MVC
It is a library of tools that help build a richer web

Backbone depends on Underscore and JQuery

***

Gmail, Facebook, Twitter, Trello are all SPAs

The main advantage of SPAs is avoiding page reloads (where most of the reloaded content is redundant), and achieving a user experience as responsive as a desktop application

SPA Challenges
- Lack of tools and experienced developers
- Extra work for SEO (google sees an empty page because the content hasn’t been generated yet)

***

- Models represent the data required by your application. They hold your application’s data and raise events when data changes.
- Collections group models
- Views handle events from models and collections. They are responsible for rendering any markup

***

In the google developer console, you can simply type Backbone and it will return a backbone object

```
Backbone
Backbone.Model
var book = new Backbone.Model({title: 'White Tiger', author: 'Aravind Adiga'});
book
book.get(‘title’)
book.set(‘title’, ‘The Stripey Tiger’)
book.toJSON() // Incredibly useful
```

***

The following is awesome Backbone example code. It creates 3 rectangle views, each bound to a rectangle model, where clicking a rectangle makes it move 10 pixels to the right.

```
***********
index.html
***********

<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Rectangles</title>
	<!--<link rel="stylesheet" href="style.css">-->
	<script src="jquery-1.11.3.js"></script>
	<script src="underscore.js"></script>
	<script src="backbone.js"></script>
	<style type="text/css">
		.rectangle {
			position: absolute;
			border: 4px solid black;
		}
	</style>
</head>
<body>
	<h1>Rectangles</h1>
	<div id="canvas"></div>
</body>
<script src="rectangles.js"></script>
</html>
```

```
**************
rectangles.js
**************

(function() {
	var Rectangle = Backbone.Model.extend({});

	var RectangleView = Backbone.View.extend({
		tagName: 'div',
		className: 'rectangle',
		events: {
			'click': 'move'
		},
		render: function () {
			this.setDimensions();
			this.setPosition();
			this.setColor();
			return this;
		},
		setDimensions: function () {
			this.$el.css({
				width: this.model.get('width') + 'px',
				height: this.model.get('height') + 'px'
			});
		},
		setPosition: function () {
			var position = this.model.get('position');
			this.$el.css({
				left: position.x,
				top: position.y
			});
		},
		setColor: function () {
			this.$el.css({
				'background-color': this.model.get('color')
			});
		},
		move: function () {
			this.$el.css('left', this.$el.position().left + 10);
		}
	});
	var models = [
		new Rectangle({
			width: 100,
			height: 60,
			position: {
				x: 300,
				y: 150
			},
			color: '#ff0000'
		}),
		new Rectangle({
			width: 26,
			height: 300,
			position: {
				x: 500,
				y: 75
			},
			color: '#00ff00'
		}),
		new Rectangle({
			width: 300,
			height: 70,
			position: {
				x: 310,
				y: 200
			},
			color: '#0000ff'
		}),
	];

	_(models).each(function (model) {
		$('div#canvas').append(new RectangleView({model: model}).render().el);
	});
	
})();
```

***

Models are the foundation of your user interface

Why do we need a special type for creating models? Javascript has object literal notation built in that already provides an efficient way of declaring new objects. Well, models provide a lifecycle. Objects are created, they go through a series of changes, they are validated and synchronized to some data source. And, models communicate state changes to your application by raising events. Through this mechanism, changes to a single backbone model can ripple through the user interface without any direct coupling or redundancy.

Constructor functions are typically named with an uppercase in Javascript
`var Vehicle = Backbone.Model.extend({});`

`extend()` is a function shared by Model, Collection, Router and View.

***

```
var Vehicle = Backbone.Model.extend({
	prop1: '1'
});

var v = new Vehicle();
var v2 = new Vehicle();

v.prop1 = 'one';

console.log(v.prop1); // one
console.log(v2.prop1); // 1
```

Why can you set using `v.prop1 = 'one'` instead of `v.set('prop1', 'one')`? The 2nd approach doesn’t even seem to work. Seem to be confusing data in the backbone model with data in an instantiation of the backbone model.

***

To create a static method for your backbone model:

```
var Vehicle = Backbone.Model.extend({},
{
	summary: function () {
		return "Vehicles are for travelling"
	}
}
);

console.log(Vehicle.summary()); // Vehicles are for travelling
```

***

Constructors use a special initialize property

```
var Vehicle = Backbone.Model.extend({
	initialize: function () {
		console.log('vehicle created');
	}
});

var car = new Vehicle();
```

***

custom `toString` function
```
asString: function() {
	return JSON.stringify(this.toJSON());
}
```

***

Backbone inheritance
```
var A = Backbone.Model.extend(...)
var B = A.extend(...)
```

***

Can set many properties at once
```
ford.set({
	‘maximumSpeed’; ‘99’,
	‘color’: ‘blue’
});
```

***

`Shift + enter` lets you write multiline input into the Chrome console

***

Use `has` to test if an attribute has been defined

```
var ford = new Vehicle();
ford.set(‘type’, ‘car’);

ford.has(‘type’); // true
ford/has(‘year’); // false
```

***

Make an attribute change event handler. One is for general events, the other is for a specific attribute being changed.

```
var ford = new Backbone.Model({
	type: 'car',
	color: 'blue'
});

ford.on('change', function () {
	console.log("something changed");
});

ford.on('change:color', function () {
	console.log("color changed");
})

ford.set('type', 'truck'); // something changed
ford.set('color', 'green'); // color changed
 // something changed
```

***

The following is an example of an underscore function. The variable volcano takes an empty object and adds the contents of Backbone.Events

```
var test = {}
console.log(test) // Object {}

var volcano = _.extend({}, Backbone.Events);
console.log(volcano) // Object{} with a bunch of methods (ex/ bind, listenTo, listenToOnce, etc)
```

***

Custom event handler

```
var volcano = _.extend({}, Backbone.Events);

volcano.on('disaster:eruption', function () {
	console.log('duck and cover');
});

volcano.trigger('disaster:eruption'); // duck and cover
```

***

On trigger, forward some data to the event handler

```
var volcano = _.extend({}, Backbone.Events);

volcano.on('disaster:eruption', function (options) {
	console.log('duck and cover - ' + options.plan);
});

volcano.trigger('disaster:eruption', {plan: 'run'});
```

The information in the trigger’s 2nd parameter {plan: ‘run’} is sent to on’s anonymous function parameter (options)

***

Remove an event handler
`volcano.off(‘disaster:eruption’);`

***

id vs cid

***

The `defaults` property specifies default values for attributes that are not set in the constructor

```
var Vehicle = Backbone.Model.extend({
	defaults: {
		‘color’: ‘white’,
		‘type’: ‘car’
	}
});

var car = new Vehicle();
car.get(‘color’); // white
car.get(‘type’); // car
```

Not clear on why you need both initialize and defaults

***

Can validate the values you are setting

***

All views have an associated DOM element at all times (.el)

***

Adds `<li data-value=“12345” id=“thing” class=“active”></li>` to the page’s body

```
var V = Backbone.View.extend({
	tagName: 'li',
	id: 'thing',
	className: 'active',
	attributes : {
		'data-value': 12345
	}
});

var v = new V();

$('body').prepend(v.el);
```

***

Can also add to a DOM element that already exists

```
var V = Backbone.View.extend({});
var v = new V({el: '#test'});
v.$el.css('background-color', 'CornflowerBlue');
```

***

All views have en `el` property that references the view’s DOM element

```
var v = new Backbone.View({el:'body'});
v.el // <body><!-- Whatever content is already here --></body>
```

***

`$el` is a cached jQuery wrapper around `el`
Avoids repeated `$(this.el)`
`v.$el // [<body>...</body>]`

***

Render is the function that renders the view’s element (`.el`). The default implementation is a no-op (?).
Should return `this`

***

Generating markup from models and binding views to the model’s change events is the core of Backbone.js

***

###### Combining Views and Models

Pass the model to the view’s constructor

```
var v =  new View({
	model: myModel
});
```

-Bind the view’s render method

```
myModel.on(‘change’, function() {
	$(‘body’).append(v.render().el);
});
```

===

Example of a view refreshing every second (based on changes to the `Date` object)

```
var RefreshingView = Backbone.View.extend({
	initialize: function () {
		this.model.on('change', function () {
			this.render();
		}, this);
	},
	render: function () {
		this.$el.html(this.model.get('text'));
	}
});

var m = new Backbone.Model({text: new Date()});
var v = new RefreshingView({model: m, el: 'body'});
v.render();

setInterval(function () {
	m.set({text: new Date()});
}, 1000);
```

***

`Backbone.View().make(...)` function has been obsoleted
`remove(...)` still works, however

***

It’s very important to understand that Backbone views should be self contained. Therefore, they only handle events that occur within their own elements.

***

Example of a click event handler on the css class clickable

```
var FormView = Backbone.View.extend({
	events: {
		'click .clickable': 'handleClick',
		'change': function () {
			console.log('handleChange');
}
	},
	render: function () {
		this.$el.html('<input type="text" class="clickable" placeholder="clickable"/> <input type="text" />');
		return this;
	},
	handleClick: function () {
		console.log('handleClicks');
	}
});

var fv = new FormView();
$('body').append(fv.render().el);
```

***

Not a fan of underscore templates, at least in the use of template script blocks. Ex/ the following in an HTML file:

```
<script id="latlon-template" type="text/html">
	<p>Latitude: <%= lat %></p>
	<p>Longitude: <%= lon %></p>
</script>
```

Handlebars templates looks a bit better, especially pre-compiling for better performance

***

Client-side routing means responding to url changes in client-side javascript code
This is done by defining routes

Client-side routes are a way to trigger a function when the browser url changes

***

Not at all impressed by Backbone’s routing. It seems much simpler to use Angular routing. Here’s a minimal backbone routing example to illustrate:

```
var Workspace = Backbone.Router.extend({
	routes: {
		'search/:query': 'search'
	},
	search: function (query) {
		console.log('searched for ' + query);
	}
});

var router = new Workspace();
Backbone.history.start();

router.navigate('search/dogs', {trigger: true});
```

***

Backbone collections can be sorted based on a comparator function. The following collection sorts its models based on each model’s sequence number.

```
var Vehicle = Backbone.Model.extend({});

var Vehicles = Backbone.Collection.extend({
	model: Vehicle,
	comparator: function (vehicle) {
		return vehicle.get('sequence');
	}
});

var vehicles = new Vehicles([
	{color: 'red', sequence: 5},
	{color: 'blue', sequence: 1},
	{color: 'yellow', sequence: 3},
	{color: 'mauve', sequence: 2},
	{color: 'notacolor', sequence: 4}
]);

JSON.stringify(vehicles);
```

***

Could also use a comparator with 2 arguments

```
comparator: function (vehicle1, vehicle2) {
	return vehicle1.get('sequence') < vehicle2.get('sequence') ? -1 : 1;
}
```

***

Adding to a collection using the add method

```
var collection = new Backbone.Collection();
collection.add(new Backbone.Model({name: 'Fred', age: 6}));
console.log(JSON.stringify(collection)); // [{'name': 'Fred', 'age': 6}]
```

What’s interesting is that you don’t have to add a Backbone model. If you add a plain old Javascript object, it will convert it to a backbone model!

```
var collection = new Backbone.Collection();
collection.add({name: 'Fred', age: 6});
console.log(JSON.stringify(collection)); // [{'name': 'Fred', 'age': 6}]
```

***

Can also add multiple models at once by passing an array
`collection.add([{name: 'Fred', age: 6}, {name: 'Billy', age: 9}]);`

***

Can even add an event handler for additions to the collection
`collection.on(‘add’, function…)`

***

To retrieve a model at a specific index from the collection, need to use the at method

```
var collection = new Backbone.Collection([
	{name: 'Fred', age: 6},
	{name: 'Sue', age: 29},
	{name: 'Dave', age: 74}
]);

console.log(JSON.stringify(collection.at(0))); // {"name":"Fred","age":6}
```

***

Example of collection forEach and map functions

```
var collection = new Backbone.Collection([
  {name: 'Fred', age: 6},
  {name: 'Sue', age: 29},
  {name: 'Dave', age: 74}
]);

collection.forEach(function (model) {
  console.log(model.get('name')); // Fred \n Sue \n Dave
});

var mapped = collection.map(function (model) {
  return model.get('name').toUpperCase();
});

console.log(JSON.stringify(mapped)); // [“FRED”, “SUE”, “DAVE”]
```

***

Example of collection find function

```
var dave = collection.find(function (model) {
  return model.get('name') === 'Dave';
});

console.log(JSON.stringify(dave)); // {“name”: “Dave”, “age”: 74}
```

## Testing

Common test tools
- Jasmine
- Mocha
- QUnit

Pattern for testing models
- Initialize a model with a specific state
- Test that the model’s behavior matches expectations

***

Interesting feature of Jasmine: Checking if a certain piece of code should throw an error

```
describe('negative length or width', function () {
  it('should throw an error if the length or width is set to a negative value', function () {
      function setDimensions() {
          rectangle.set({length: 1, width: -9});
      }
      expect(setDimensions).toThrow();
  });
});
```

***

For testing without a browser, try Jasmine-node or Mocha