# Course Overview

Firebase is a blazing-fast, real-time, noSQL backend-as-a-service.

# Introduction

## Welcome

When Joe Eames puts up a slide that says "Firebase is AWESOME", you know this is a tool worth learning.

## Course Repository

[github.com/joeeames/FirebaseFundamentalsCourse](github.com/joeeames/FirebaseFundamentalsCourse)

Important: You need to clone this repository in order to use the JSON data. In particular, you should import the JSON files into your firebase dashboard.

## Why You Should Learn Firebase

- NoSQL
- Proven
- Real-Time
- Developer Friendly
- Versatile

## The Demo App

The course walks you through building a Twitter clone.

Author is using jQuery and Underscore templates because he believes it's the most basic way to put together a reasonable front-end.

> I don't expect that very many people will be using this kind of a front-end, and I certainly don't expect that the code that we write in jQuery or Underscore is going to be a model of a good way to write a front-end. Use Angular or React instead for that.

Basically, jQuery and Underscore are used in order to simplify the course since the front-end isn't the main focus.

# Firebase Basics

## NoSQL Concepts

NoSQL data stores are generally better at scaling and can be easily scaled by spreading the work over multiple machines.

A drawback of this is eventual consistency. The best example of this is when you go shopping at amazon.com. Sometimes you can buy something that only has a few items left in stock. Then sometime later you can receive an email letting you know that your purchase didn't go through because the item was actually out of stock because someone else bought the last one before you clicked buy. The part of the database you were talking to wasn't aware of the order being processed elsewhere in the system.

### NoSQL Categories

Key/Value store - has a unique key and then some data attached to that. They are very simple to understand and some good examples of that are Amazon's SimpleDB, Redis, and Voldemort.

Document database - Very similar to key/value stores. Basically a giant JSON document, which is essentially a bunch of nested key-values. This is the most common type of database we generally talk about when talking about NoSQL due to the popularity of MongoDB. Examples: CouchDB, RethinkDB.

Column data store - Examples: Cassandra, HBase

Graph databases - Useful for modelling things that are naturally expressible as graphs with nodes and relationships, such as routes, businesses or people. Examples: InfiniGraph, Neo4J (???)

Important to understand that NoSQL databases can be very different from each other.

## Firebase Essentials

Firebase is technically a key-value store, although it looks a lot like a document database.

One of the great benefits of Firebase is that it is hosted for you.

Firebase is URL-oriented. This means that each piece of data is accessible at a specific unique url, which corresponds to the location of the data in your Firebase store. You can think of your data in Firebase as a large tree or JSON graph. This is the main reason it is categorized as a key-value store.

Example URLs (these actually worked when I tried them!):

[https://je-example.firebaseio.com/favorite/movie.json](https://je-example.firebaseio.com/favorite/movie.json)
[https://je-example.firebaseio.com/favorite/sport.json](https://je-example.firebaseio.com/favorite/sport.json)
[https://je-example.firebaseio.com/favorite.json](https://je-example.firebaseio.com/favorite.json)

Another essential aspect of Firebase is that it is real-time. In addition to being really fast, this makes it different from most other data stores. In accessing typical data over the web, you make a request for the data, then the data is returned in the response, and you are finished. If you think the data may have changed, and you want to check for updated data, you have to make the request again. 

Firebase is different. When you access data, you instead begin listening at a specific url. You will receive a snapshot of what the data looks like, and any time the data changes, firebase will notify you with an event. This means that you don't have to worry about your client getting out of date with the server. Being real-time is one of the identifying characteristics of Firebase. But don't be misled into thinking that if your application has no need for real-time data, that Firebase isn't right for your project. Even if you don't truly need real-time data access, it turns out that the real-time nature of Firebase is extremely handy, even when it's not required, and not anymore complex than non-real-time data, and in many cases can even be simpler since you don't have to deal with the case of being out of date with the server.

Finally, Firebase offers integrated authentication. This makes implementing authentication in your application a breeze.

## The Management Console

It's amazing how user-friendly the management console is. Interacting with a DB couldn't possibly be simpler than this.

## Understanding Data - Arrays

Firebase does not support arrays as they work in JavaScript. This is due to the automatic numeric indexing in arrays in JavaScript.

Example:

```
[
  0 - "Luke Skywalker",
  1 - "Han Solo",
  2 - "Jar Jar Binks",
  3 - "Boba Fett"
]
```

User 1 - Delete element at index 2
User 2 - Update element at index 2 (this updates Boba Fett instead of Jar Jar Binks)

## Understanding Data - Null Locations

If you navigate to a firebase url that doesn't exist, you'll see in the dashboard a key with a null value. In addition, if you add `.json` to the url, it will return `null`.

## Understanding Data - Refs

```
var url = "https://example.firebaeio.com/";
var baseRef = new Firebase(url);
var moviesRef = new Firebase(url + "movies/");
var booksRef = new Firebase(url + "books/");
```

## Refs - child(), parent(), and root()

The intuitive way to access a child location would be as follows:

```
var url = "https://example.firebaeio.com/";
var moviesRef = new Firebase(url + "movies/");
var starWarsRef = new Firebase(url + "movies/star_wars/");
```

However, this can get tedious pretty quickly. Instead, you can do:

```
var starWarsRef = moviesRef.child("star_wars");
```

There's also a `parent` method:

```
var moviesRef = starWarsRef.parent();
```

And a `root` method:

```
var baseRef = starWarsRef.root();
```

## Data Design - Deep Nesting

The following approach could be taken for the design of our mock twitter database:

```
app -> users -> userID -> [email, followers, name, tweets] -> tweetID -> [created, text]
```

Unfortunately, this is a really bad way to do things. We want shallow nesting, not deep nesting.

In order to understand why this is bad, you have to understand how Firebase works when you query for a piece of data. You always query for a specific node using a url.

Suppose you request a specific user. In the nested structure, we would get back something like the following:

```
{
  email: "C-3PO@hotmail.com",
  followers: {
    -jgiuerghirogjo: "-K-fjionbigvnuisnbvi-f"
  },
  name: "C-3PO",
  tweets: {
    -K-fjionbigvnuisnbvi-f: {
      created: "1970-01-16T23:56:51-07:00",
      text: "This asteroid may not be entirely stable."
    },
    -K-idsonbsignui_wz: {
      created: "1970-01-16T23:46:56-07:00",
      text: "R2 is a rusty bucket of bolts!"
    },
    -K-sXCVjigniug17E: {
      created: "1970-01-08T17:02:01-07:00",
      text: "Just call me Golden Rod"
    }
  }
}
```

Suppose you want just the user's email address and name. In the request, you're also get all the user's followers and tweets. This is obviously a problem when a user has tens of thousands of tweets. Things get even worse if you request all the users. This approach clearly doesn't scale at all.

In the shallow nesting setup, the users node reveals an array of users, each user object only containing a key identifier, email and name. Similar flattening is done for tweets and followers.

## Data Design - Duplicate Data

In the shallow nesting setup, followers returns an array of followers, where the value of each entry is the unique hash corresponding to the user.

Suppose you want to find out the follower's username and email address. In a relational database you would use a `join` operation between two tables. The Firebase equivalent is to duplicate data.

# Reading Data

## Demo Intro

Author is initially using the nested data approach in order to give a concrete example of the problems with such an approach.

## Reading Data with `on()`

I am really impressed by the real-time functionality of Firebase. First off, didn't have to run a server at all, `file:///` works just fine. Second, changes made to the data in the Firebase dashboard are reflected immediately in the UI!

In the `script.js` file, add the following before `handleUserChange`:

```
var firebaseRoot = "https://mytwitterclone-pluralsight.firebaseio.com/twitterClone/",
  usersRef = new Firebase(firebaseRoot + 'users');

usersRef.on('value', function(snap) {
  setUsers(snap.val());
});
```

This places a listener on the value of `users`.

Inside of `handleUserChange`, add the following:

```
if (userKey) {

  var userRef = usersRef.child(userKey);

  userRef.on('value', function(snap) {
    setTweetBox(snap.val());
  });
```

The line `var userRef = usersRef.child(userKey)` is a common pattern for retrieving a specific value from a collection (in this case, a `user` from `users`).

## Reading Data with `once()`

The `.on('value')` event fires not just when the event value changes, but also fires for the initial value.

If instead you use the `.once('value')` event handler, it will only listen for the initial value.

## Displaying the Timeline - 1st Attempt

A timeline on Twitter shows all of your tweets, as well as  all the tweets of the people you're following, ordered by when they were created.

Algorithm implementation: For a specific user, look at who they're following and gather all those tweets. As pseudocode:

```
/*
get list of users who are following
for each user in following
  get that user's tweets
  add those tweets to an array
take the entire list of tweets, order by date
take the top N (25 or so)
display them
*/
```

> Now looking at this algorithm, I am absolutely ready to quit. This is absolutely ridiculous that we've got to gather all this different data, interleave it together, in the right order, and take only the top N. We really want our server to be doing this for us. For one thing, we'll be selecting way too much data.

## Displaying the Timeline - 2nd Attempt

Author created a separate timeline key on the userObjects, and populated it with an array of user keys, each key having a value of all the tweets in that user's timeline.

## Detaching Event Listeners

The current UI setup has the issue that when a specific user's name is updated, the new name shows up in the `user-tweet-box`, even if that user wasn't even selected. This can be fixed by detaching the correct event listeners:

```
var timelineRef,
  timelineHandler,
  userRef,
  userHandler,
  stopListening = function() {
    if (typeof timelineRef === 'object' && typeof timelineHandler) {
      timelineRef.off('value', timelineHandler);
    }

    if (typeof userRef === 'object' && typeof userHandler) {
      userRef.off('value', userHandler);
    }
  };

...

var handleUserChange = function(e) {
    var userKey = $(e.target).val();

    stopListening();

    if (userKey) {

      timelineRef = userObjectsRef.child('timeline').child(userKey);
      timelineHandler = timelineRef.on('value', function(snap) {
        setTimeline(flatten(snap.val()).reverse(), userKey);
      });

      userRef = usersRef.child(userKey);

      userHandler = userRef.on('value', function(snap) {
        setTweetBox(snap.val());
      });

      userObjectsRef.child('following').child(userKey).once('value', function(snap) {
        setFollowing(snap.val());
      });

    } else {
        setTweetBox({});
        setTimeline({});
        setFollowing({});
    }
```

Notice the call to `stopListening()` in the beginning of the `handleUserChange` function, and the assignment of our `timeline` and `user` handlers:

```
timelineHandler = timelineRef.on('value', function(snap) {
  setTimeline(flatten(snap.val()).reverse(), userKey);
});

userHandler = userRef.on('value', function(snap) {
  setTweetBox(snap.val());
});
```

# Writing Data

## Using `push()` to Update Data

Here is the following code related to pushing a tweet:

```
userObjectsRef.child('tweets').child(userKey).push(tweet, function(err) {
  if(err) {
    console.warn('error!', err);
  } else {
    userRef.once('value', function(snap) {
      var user = snap.val();
      userObjectsRef.child('timeline').child(userKey).push({
        created: tweet.created,
        text: tweet.text,
        userKey: userKey,
        user: {
          email: user.email,
          key: userKey,
          name: user.name,
          username: user.username
        }
      });
    });
  }
})
```

Let's go through this step by step:

```
userObjectsRef.child('tweets').child(userKey).push(tweet, function(err) {...});
```

From your `userObjects` reference, retrieve the `tweets` object (which is an array of users) and then grab a specific user by key. This user object is an array of tweets, and you're pushing a new tweet to it (which is a JavaScript object created in the UI, specifically from extracting data from the textarea after hitting the "Tweet" button). This triggers a callback function.

Since tweets are duplicate data, (found in both the `tweets -> user` and `timeline -> user` arrays), we also have to push the tweet into the user's timeline. Hence in the callback we have:

```
userRef.once('value', function(snap) {
  var user = snap.val();
  userObjectsRef.child('timeline').child(userKey).push({
    created: tweet.created,
    text: tweet.text,
    userKey: userKey,
    user: {
      email: user.email,
      key: userKey,
      name: user.name,
      username: user.username
    }
  });
});
```

It's a little confusing that we're using a `userRef` in addition to the `usersObjectsRef`. This has to do with our flattened database structure. At the top-level we have:

- twitterClone
 - userObjects
 - users

In the `users` object, you have an array of users, and each user is an object containing detailed information about the user such as email, username, tweet count, etc.

Anyway, in the `userObjectsRef`, look up `timeline -> specific user` and push an object with a bunch of data, including a `user` object with information gathered from `userRef`.

I can only imagine how confusing this must get when you duplicate data in more than one place!

Supposedly the process we just followed is a fairly common pattern in Firebase:

> So we see this pattern of pushing some data in, then querying some data, getting a hold of that data, and then using that to push more data in

###### A note on dates and times

Bad: `created: (new Date()).toString()`
Good: `created: Firebase.ServerValue.TIMESTAMP`

In the first approach, we're using the date and time set on the client box. In the second approach, we're using the date and time used on the server.

## Using `update()` to Update Data

```
userRef.update({tweetCount: (user.tweetCount || 0) + 1}, function(err) {});
```

If `user.tweetCount` is `undefined`, `0` gets used instead, and is then incremented by 1.

Kind of wish author would go over Firebase promises (if that's even a thing) because this particular portion of the lesson already shows some callback hell.

## Structuring Data for New Features

> Rather than detecting that we were successful in pushing a tweet and reacting to that inside of a callback, instead I'm going to put a listener on the user's tweets

The `fannedOut` entry in a tweet object is a boolean that represents whether or not a tweet has been broadcasted to the timeline of all the followers of the user who tweeted.

***

This course just got confusing as hell! The logic for updating data is fairly complicated, have to jump all over the place in the database tree to understand what's happening. It doesn't help that a lot of the database keys are ID hashes instead of proper names.

## Events and Server Code

[Currently here]