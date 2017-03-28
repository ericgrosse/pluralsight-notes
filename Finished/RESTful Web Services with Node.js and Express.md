# Getting Data

## Wiring up to MongoDB and Mongoose

Given `Book` is a Mongoose model, it is common to see the following inside the callback of `Book.find`:

```
if (err)
  console.log(err);
```

Instead, you can do the following:

```
if (err)
  res.status(500).send(err);
```

This is a cleaner way of showing there was an error in our API call

## Filtering with the Query String

Want to be able to add query selectors to our API url

```
http://localhost/api/book?genre=Fiction
```

This will tell the backend to filter the books returned to only those matching the genre of Fiction.

Getting this to work is surprisingly simple:

```
var query = req.query;
Book.find(query, callback);
```

Now you can filter based on any property in the JSON request. One problem with this is filtering by nonsense properties like `asdfghjkl` causes the backend to return an empty response.

Instead, create an empty query object, and add properties to the query based on the properties the query contains:

```
var query = {};

if (req.query.genre) {
  query.genre = req.query.genre;
}
```

## Getting a Single Item

Use `req.params` to retrieve variables in a url

Can chain a `.get()` and a `.post()` (in fact can chain all verbs)

# Updating Data

## Middleware

`Book.findById` already used in `get`, `put` and `patch` methods, which is motivation for refactoring it into middleware.

```
bookRouter.use('/:bookId', function(req, res, next) {
  Book.findById(req.params.bookId, function(err, book) {
    if (err)
      res.status(500).send(err);
    else if (book) {
      req.book = book;
      next();
    }
    else {
      res.status(404).send('No book found');
    }
  });
});
```

This middleware retrieves the book corresponding to the given ID in the database (if any), and adds that book as a property of the request object.

With this in place, our REST API code gets greatly simplified. For example, our GET request becomes:

```
.get(function(req, res) {
  res.json(req.book);
});
```

What's cool is that the middleware even does the error handling for us, so we don't need to replicate that in each REST API call.

## Implementing Patch

We only want to update a book with the book properties present in the request.

Naive implementation

```
.patch(function(req, res) {
  if (req.body.title) {
    req.book.title = req.body.title
  }
  // And so on for each additional property
});
```

This approach obviously doesn't scale. Instead it's better to use a `for..in` loop to iterate over each property of `req.body`.

```
.patch(function(req, res) {
  if (req.body._id)
    delete req.body._id
  for (var p in req.body) {
    req.book[p] = req.body[p];
  }

  req.book.save(function(err) {
    if (err)
      res.status(500).send(err);
    else
      res.json(req.book);
  });
});
```

Note that we don't want to update the book ID, which is why we delete it from `req.body` if it exists.

## Implementing Remove

```
.delete(function(req, res) {
  req.book.remove(function(err)) {
    if (err)
      res.status(500).send(err);
    else
      res.status(204).send('Removed');
  });
});
```

Notice that upon a successful deletion, we send a status code of `204`.

# Testing

Look into `supertest` for running integration tests.

# HATEOAS

## Introduction

Add hyperlinks to your API so you can navigate through it in a very intuitive manner.

## Adding Hypermedia to Our API

On our endpoint `GET /api/books`, we want to loop through each book and add a hyperlink to the respective book before returning the array of books. This is complicated by the fact that each book is a mongoose model.

To circumvent this, create a new book that is a copy of the book by doing `var newBook = oldBook.toJSON()`. `toJSON` strips out all the mongoose related stuff from the object.

```
var returnBooks = [];
books.forEach(function(element, index) {
  var newBook = element.toJSON();
  newBook.links = {}
  newBook.links.self = 'http://' + req.headers.host + '/api/books/' + newBook._id;
  returnBooks.push(newBook);
});
res.json(returnBooks);
```

Also note that `req.headers.host` pulls in the base of our url, in this case, `localhost:8000`.

***

The other link we'll add will take us from `/api/books/:bookID` to `/api/books?genre=<matching genre>`.

```
bookRouter.route('/:bookId')
  .get(function(req,res){
    var returnBook = req.book.toJSON();
    returnBook.links = {};
    var newLink = 'http://' + req.headers.host + '/api/books/?genre=' + returnBook.genre;
    returnBook.links.FilterByThisGenre = newLink.replace(' ', '%20');
    res.json(returnBook);
  })
```

Recall that the `bookRouter` has middleware that finds the book matching `req.params.bookId` and attaches it to `req.book`.

The line `returnBook.links.FilterByThisGenre = newLink.replace(' ', '%20');` is used to handle spaces in the url.