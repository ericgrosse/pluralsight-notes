Currently on: 

# Introduction

## What is Knex?

Knex is a data access library for working with relational databases using Node. It provides you with a lot of great features such as:

- Connection pooling: Creating a connection to a database is a very expensive operation. Being able to use an already established connection can greatly improve the speed of your application.
- Database migrations
- Data seeding
- Protects against SQL injection
- Transactions
- Callbacks, promises, data streams, etc

At its heart, Knex is a query builder. It allows you to write code using a fluid syntax similar to jQuery. Knex is also a schema builder, allowing you to both create and modify the tables of your database. 

In addition, it standardizes the code you write, and the responses you get from different databases. Instead of having to maintain multiple versions of your code to support each databases' particular SQL dialect, you can write your code once and let Knex handle those differences for you.

###### Differing SQL Dialects

Postgres
```
CREATE TABLE item
(
	id SERIAL
)
```

MySQL
```
CREATE TABLE item
(
	id INT NOT NULL AUTO_INCREMENT
)
```

Using Knex, we could write just one piece of code

```
knex.schema.createTable('item', function(tbl) {
	tbl.increments();
});
```

