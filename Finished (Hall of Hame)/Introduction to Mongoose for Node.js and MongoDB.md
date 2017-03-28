# Instructions for Running the Last Exersise File

- In the `package.json` file, change mongoose from `v3` to `v4` then do `npm install`
- In the terminal, run `mongod`
- In `app.js`, change your mongoose connection string to `mongoose.connect('mongodb://localhost:27017/standupmeetingnotes')` or something similar
- Run `npm start`
- Navigate to `localhost:3000` in the browser

Now whenever you create a new note in the UI, you can check that the note was persisted in the database using Robomongo.

# Setting Up the Demo Application

## Demo

`express -H` used to generate a mockup site using the Hogan view engine

Using this generator, `npm start` sets up a server on `localhost:3000`

The author changes the view engine from Hogan to Swig. This is actually ideal since it allows you to set the view engine to HTML.

# MongoDB

## Hosting Options

- Cloud based virtual servers (ex/ Microsoft Azure)
- Database-as-a-service (ex/ Mongolabs, Compose.io)

# Building the Mongoose Schema

Difference between schema and model: http://stackoverflow.com/a/22950402/2472351
Basically, the schema is for the structure and the model is for the programming interface
You pass in the actual data in an instance of the model

```
var Quiz = mongoose.model(‘Quiz’, quizSchema);

var quiz1 = new Quiz({
	name: ‘Favorite places quiz’,
	description: ‘Demo quiz’,
	…
});
```

## Allowed Data Types

| Mongoose Schema Types | JavaScript Data Types |
|--------|--------|
| String | String |
| Number | Number |
| Date | Object |
| Buffer | Object |
| Boolean | Boolean |
| Mixed | Object |
| ObjectId | Object |
| Array | Array (Object) |

## More Complex Schema Example

```
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

// child address schema
var addressSchema = new Schema({
	type: String,
    street: String,
    city: String,
    state: String,
    country: String,
    postalCode: Number
});
// parent customer schema
var customerSchema = new Schema({
	name: {
    	first: String,
        last: String
    },
    address: [ addressSchema ],
    createdOn: { type: Date, default: Date.now },
    isActive: { type: Boolean, default: true }
});
```

Note the use of `address: [ addressSchema ]`, which is using a nested Schema.

Be careful when naming a Mongoose field `type`. If you really must, do the following:

```
type: { type: String } // as opposed to just type: String
```

## Demo: Building Our Daily Stand-Up Schema

If you do not define an ID field, Mongoose will create one for you by default

A schema looks as follows:

```
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var standupSchema = new Schema({
	memberName: String,
    project: String,
    workYesterday: String,
    workToday: String,
    impediment: String,
    createdOn: { type: Date, default: Date.now }
});
```

Can also add fields to a Schema after the initial definition:

```
exampleSchema.add({
	middleName: String
});
```

This is useful for conditionally adding Schema fields:

```
if (includeMiddleName) {
	exampleSchema.add({
    	memberName: {
        	first: String,
            middle: String,
            last: String
        }
    });
} else {
	exampleSchema.add({
    	memberName: {
        	first: String,
            last: String
        }
    });
}
```

Schema definitions are just JSON. You could also load in a saved JSON file as your Schema.

# Building a Model

## Introduction

```
// Build a model from the customer schema
var Customer = mongoose.model('Customer', customerSchema);
```

Can optionally provide a 3rd parameter specifying the MongoDB collection name. If this isn't provided, Mongoose will provide a collection name from the model name given. Mongoose will attempt to pluralise the model name (so Customers instead of Customer).

You can build any number of models against the Schema definition:

```
// Build a model fro mthe customer schema
var Customer = mongoose.model('Customer', customerSchema);

// Add a custom property to the schema
customerSchema.add({ discountCode: String });

var DiscountCust = mongoose.model('DiscountCust', customerSchema);
```

## Documents

Everything in Mongoose starts with a Schema and a model is built (or compiled) from a Schema
A document in Mongoose is simply an instance of our Model

Example:

```
// Schema
var personSchema = new Schema({
	firstName: String,
    lastName: String
});

// Model
var Person = mongoose.model('Person', personSchema);

// Document
var bob = new Person({
	firstName: 'Bob',
    lastName: 'Doe
});
```

# Queries

## Documents and Their Helper Methods

```
var Standup = require('../models/standup.server.model.js');

// No callback... Deferred execution
var query = Standup.find();

// With callback... Executes query immediately
Standup.find(function(err, results) {
	// Handle the error or results here
});

// With callback and query conditions
Standup.find({ memberName: 'David' }, function(err, results) {
	// Handle the error or results here
});

// Limit the returned fields
Standup.find({ memberName: 'Mary' }, 'memberName impediment', function(err, results) {});

// Using findOne with deferred execution
var query = Standup.findOne();
query.exec(function(err, results) {});

// Find by ID with chained method calls
Standup.findById(id).exec(function(err, results) {});

// Find by ID and return every field except impediment
var query = Standup.findById(id, '-impediment');
```

### Comparison Query Operators

```
Customer.find({ discount: { $gte: 10, $lt: 20 } }, function(err, results) {
		if (err) throw err;
		console.log(results);
});
```

An alternative way to query for discounts `> 10` and `< 20`:

```
Customer.where("discount").gte(10).lt(20).exec(function(err, results) {
	if (err) throw err;
	console.log(results);
});
```

Can chain together `where` methods:

```
Customer.where("discount").gte(10).lt(20).where("zipCode", "12345").exec(function(err, results) {
	if (err) throw err;
	console.log(results);
});
```

## Updating and Removing Documents

```
var Standup = require('../models/standup.server.model.js');

var condition = { memberName: 'Mary' };
var update = { impediment: 'None - Mary no longer works here!' };

Standup.update(condtion, update, function(err, numberAffected, rawResponse) {...});
```

Sometimes it's actually better to find a document, make some changes and save it (meaning use two database calls instead of one):

```
Standup.findOne({ memberName: 'Mary'}, function(err, doc) {
	// Handle errors, validate results, etc
    doc.impediment = 'None - Mary won the lottery and is on an island now';
    doc.save(function(err) {
    	// Handle errors
    });
});
```

As for removals:

```
// Remove any document created on or after Halloween day
var gteDate = new Date(2014, 10, 31);
Standup.remove({createdOn: {$gte: gteDate}, function(err) {
	// handle error here
}});
```

There is also a `findByIdAndUpdate` and `findByIdAndRemove` method

## Demo - Querying Documents

The Swig template syntax is actually decently readable:

```
{% for note in notes %}
    <div>Note from {{ note.memberName }}</div>
    <div> Work yesterday: {{ note.workYesterday }}</div>
    <div> Work today: {{ note.workToday }}</div>
    <div> Impediments: {{ note.impediments }}</div>
{% endfor %}
```

To query for the 12 most recent Standup meeting notes:

```
exports.list = function(req, res) {
	var query = Standup.find();

    query.sort({createdOn: 'desc'})
    .limit(12)
    .exec(function(err,results){
        res.render('index', { title: 'Standup - List', notes: results });
    });
};
```

The router is set up to simply forward the route handling to our controller, as follows:

```
// GET homepage
router.get('/', function(req, res) {
	return standupCtrl.list(req, res);
});

// GET New Note page
router.get('/newnote', function(req, res) {
	return standupCtrl.getNote(req, res);
});

// POST New Note page
router.post('/newnote', function(req, res) {
	return standupCtrl.create(req, res);
});
```

To filter a query by member:

```
exports.filterByMember = function(req, res) {
	var query = Standup.find();
    var filter = req.body.memberName;

    query.sort({ createdOn: 'desc' });

    if (filter.length > 0) {
    	query.where({ memberName: filter });
    }

    query.exec(function(err, results) {
    	res.render('index', { title: 'Standup - List', notes: results });
    });
};
```

I don't understand why the author chooses to use a POST request for this since it's performing the querying actions of a GET request

# Validation

To do validation on a specific field:

```
var reMatch = /[a-zA-Z];
var customerSchema = new Schema({
	name: {
		type: String,
		required: true,
		match: reMatch
	},

	// more stuff
});
```

To do enum validation:

```
var impediments = ['none', 'minor', 'blocking', 'severe'];

var standupSchema = new Schema({
	impediment: {
		type: String,
		required: true,
		enum: impediments
	},

	// more stuff
});
```

Can also validate for min and max (just add those properties to the object)

## Introduction to Middleware

Simple example leading you through the entire mongoose process:

```
var personSchema = new Schema({
	firstName: { type: String, required: true },
	lastName: { type: String, required: true },
	status: { type: String, required: true, default: 'Alive' }
});

// Build a model from the person schema
var Person = new mongoose.model('Person', personSchema);

// New document instance of a Person model
var newPerson = new Person({ firstName: 'John', lastName: 'Doe' });

// Save the document
newPerson.save(function(err) {
	if (err) {
		return handleError(err);
	}
});
```

Order of events:
Save -> Defaults applied -> Validation -> Error (if any)

## Custom Validators

Can write a custom validator, then use it in the schema with `validate: nameOfValidatorFunction`

## Demo - Adding Validation to Our Application

```
var memberNameValidator = [
	function(val) {
    	return (val.length > 0 && val.toLocaleLowerCase() !== 'none)
    },
    // Custom error text
    'Select a valid member name'
];

var memberNameValidator = [
	function(val) {
    	var testVal = val.trim();
        return (testVal.length > 0);
    },
    // Custom error text
    '{PATH} cannot be empty'
];
```

Must also do error handling on the database `save` call:

```
entry.save(function(err) {
	if (err) {
    	var errMsg = 'Sorry, there was an error saving the stand-up meeting note. ' + err;
        res.render('newnote', { title: 'Standup - New Note (error)', message: errMsg });
    }
    else {
    	console.log('Stand-up meeting note was saved!'); // Logs to the terminal, not the browser
        // Redirect to the home page to display list of notes
        res.redirect(301, '/');
    }
});
```

If the error condition is met, we'll actually display the error in the UI through Swig:

```
{% if message.length > 0 %}
	<div class="alert alert-danger alert-error">
    	{{ message }}
    </div>
{% endif %}
```

# Wrapping Things Up

## More Tools

Make sure to visit [docs.mongodb.com/ecosystem/tools/administration-interfaces](docs.mongodb.com/ecosystem/tools/administration-interfaces) as this provides some database UI tools for MongoDB.

## Alternative Framework

For larger applications, author recommends using the MEAN stack over just using an Express generator