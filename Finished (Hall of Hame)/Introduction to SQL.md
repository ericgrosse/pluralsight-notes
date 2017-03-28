# SQL Basics

## Introduction

What is SQL
- Structured Query Language
- A special-purpose programming language
 - Its purpose it to manipulate relational databases
 - Declarative language
 - Contains both a data definition syntax as well as a data manipulation syntax
- It's both an ANSI and ISO standard

## What is a Database

There are many different types of databases
- Relational
- Object-oriented
- Document-based (noSQL)

## Naming Things

- Table names will be singular. Ex/ user, email, phone
- Column names will never be repeated inside of a particular database - this is to keep things clear when looking at names of columns

## RDMS

Short for Relational Database Management System
- More than just the database
- Also includes tools and applications to help manage larger-scale database systems
- Most extend the ANSI SQL language with vendor-specific extensions
- Most are proprietary
 - Oracle has PL/SQL
 - SQL Server has T-SQL
- Most ANSI SQL will work with any RDMS

## SQL in This Course

Author is using MySQL for this course
- Free and open-source
- Runs on all major platforms: Windows, OSX, Linux
- Had an ANSI operation mode that enforces ANSI compliance (most RDMS's do not have this feature)

# The SELECT Statement

## Introduction

Querying data is all about asking questions of that data
- Pretend that data is actually another human that knows everything that is stored in that data
- All queries could be postulated in English
- Understanding the mapping between English and SQL can help you learn SQL much faster

Example questions:
- Who are all my contacts?
- How many contacts do I havE?
- Which of my contacts is the oldest?

---

The use of `SELECT *` is considered an anti-pattern
- It is inefficient because the database has to look up all the columns for you
- It is ddangerous because the order of columns might change later and break code that depended on the order of the columns

## FROM Clause

You should qualify all the columns in the select list with the Table's name. This is to avoi a column name collision later if you subsequently add other tables to this query. Fortunately, you can alias your Table's name in the FROM clause. Example:

What are all the first names of the people in my contact list?

```
SELECT p.person_first_name
FROM person p;
```

## Limiting the Result Set

There are two ways to limit the number of rows in a simple SELECT statement.
- One is to add additional clauses after the FROM clause
- Another is to use the DISTINCT qualifier before the select list

Example:

What are all the unique first names among my contacts?

```
SELECT DISTINCT p.person_first_name
FROM person p;
```

## WHERE Clause

The WHERE clause is like a search
- Much like searching on the web, but with more precision
- The body of the WHERE clause will be a set of expressions that can evaluate to TRUE or FALSE
- If the expression evaluates to TRUE for a particular row, it will be returned in the result set

Example: Who are all the people in my contact list that have the first name Jon?

```
SELECT p.person_first_name
FROM person p
WHERE p.person_first_name = 'Jon';
```

###### Simple Operators

- `=` (Equal to)
- `<>` (Not equal to)
- `>, >=, <, <=` (Inequality operators)

## WHERE Clause

Infuriatingly enough, the author doesn't give video instructions on how to set up mysql, although minimal instructions and a `constacts.sql` file are found in his exercise files.

I opted to use XAMPP and phpMyAdmin to import the `contacts.sql` file, then navigated to `/c/xampp/mysql/bin` in the terminal and executed `./mysql.exe --user=root`. This runs MariaDB for some reason, although it seems to function almost exactly the same as MySQL.

Useful commands:

- `SHOW DATABASES;`
- `USE <DB Name>;`
- `SHOW TABLES;`

It's also useful to deliberately omit semicolons + hit `Enter` since this allows you to type in multiline input

---

The column(s) used in the `WHERE` clause don't have to appear in the `SELECT` clause

Comparison operators work with strings, meaning the following is a valid query:

```
SELECT a.address_zip_postal
FROM address a
WHERE a.address_state_province_county > 'California';
```

Result:

```
+--------------------+
| address_zip_postal |
+--------------------+
| 7777               |
| 11111              |
+--------------------+
```

## More Operators

`BETWEEN` is a useful operator. Example:

```
SELECT p.person_first_name
FROM person p
WHERE p.person_contacted_number BETWEEN 1 and 20
```

Note that `BETWEEN` is inclusive for the lower and upper values.

## `LIKE`

`LIKE` is a special operator just for strings
- You give `LIKE` a pattern and it will match strings that match that pattern
- % is the wild-card character
- The % can go anywhere in the string

Example: Who are all the people in my contact list that have a first name that begins with the letter J?

```
SELECT p.person_first_name
FROM person p
WHERE p.person_first_name LIKE 'J%';
```

Result:

```
+-------------------+
| person_first_name |
+-------------------+
| Jon               |
+-------------------+
```

This is a really powerful feature! Can do things such as `LIKE 's%o%'`

## `IN`

- An expression with `IN` requires a column and a list of potential values
 - Values can be numeric or string or date
 - It might seem to overlap with `BETWEEN`, but it will come in handy later
- If a row's column value matches any of the potential values in the list, then the row is added to the result set

Example: Who are all the people in my contact list that are named Jon, Shannon, or Fritz?

```
SELECT p.person_first_name
FROM person p
WHERE p.person_first_name
IN ('Jon', 'Shannon', 'Fritz');
```

Result:

```
+-------------------+
| person_first_name |
+-------------------+
| Jon               |
| Shannon           |
| Fritz             |
+-------------------+
```

## Demo: `IN`

Can use `IN` instead of `BETWEEN` if you wanted to:

```
SELECT p.person_first_name, p.person_contacted_number
FROM person p
WHERE p.person_contacted_number
IN (0,1,2);
```

Result:

```
+-------------------+-------------------------+
| person_first_name | person_contacted_number |
+-------------------+-------------------------+
| Shannon           |                       0 |
| Fritz             |                       1 |
+-------------------+-------------------------+
```

It makes more sense to use `BETWEEN` for integers though since you don't have to manually type in each value you want to check (just the start and end values).

## `IS` and `IS NOT`

- `IS` is a special operator that helps address when a value in a column might be `NULL`
 - `NULL` is special in SQL and it doesn't work with the equality operator

Example: Who are all the people in my contact list that don't have a last name?

```
SELECT p.person_first_name
FROM person p
WHERE p.person_last_name
IS NULL
```

Result: Empty set

Of course, you could also check `IS NOT NULL`.

# `JOINS`

## Introduction

###### `ORDER BY`

- The `ORDER BY` clause allows you to sort the result set
 - `ORDER BY` comes after the `WHERE` clause, but the `WHERE` clause isn't required
- You specify one or more of the columns from the result set
 - Multiple columns are separated by commas
- Ascending (ASC) order is the default
 - You can add the keyword `DESC` to cause the ordering to be descending

Example: Who are all the people in my contact list, ordered by last name?

```
SELECT p.person_first_name, p.person_last_name
FROM person p
ORDER BY p.person_last_name DESC;
```

Result:

```
+-------------------+------------------+
| person_first_name | person_last_name |
+-------------------+------------------+
| Fritz             | Onion            |
| Jon               | Flanders         |
| Shannon           | Ahern            |
+-------------------+------------------+
```

## Set Functions

- SQL includes a number of built-in functions to provide additional functionality
- Some of those functions help to turn column data from Tables into computed values
 - You pass the column name as the parameter to the function
- You use these functions instead of columns in the select list
- These functions help add additional interesting questions we can ask of the data

Examples:
- How many contacts do I have?
- Who is the contact that I've interacted with the least?
- What is the average number of times I've contacted people in my contact list?

Computational Functions: `COUNT`, `MIN`, `MAX`, `AVG`, `SUM`. Note that `COUNT` includes `NULL` values when used with `*`.

## Demo: Set Functions

It is perfectly acceptable to use `SELECT COUNT(*)`in your SQL query. This can be used to find out how many rows are in your table. Example:

```
SELECT COUNT(*) AS NumberOfPeople
FROM person p;
```

Result:

```
+----------------+
| NumberOfPeople |
+----------------+
|              3 |
+----------------+
```

Notice the use of the `NumberOfPeople` alias.

###### Other Examples

```
SELECT MAX(p.person_contacted_number) AS Max
FROM person p;
```

Result:

```
+------+
| Max  |
+------+
|    5 |
+------+
```

## Demo: Qualifiers and Set Functions

Can combine the functionality of qualifiers and set functions. Example:

```
SELECT a.address_street FROM address a;

+----------------+
| address_street |
+----------------+
| Pluralsight    |
| Trainsignal    |
| Pluralsight    |
| Pluralsight    |
+----------------+

SELECT COUNT(a.address_street) FROM address a;

+-------------------------+
| COUNT(a.address_street) |
+-------------------------+
|                       4 |
+-------------------------+

SELECT COUNT(DISTINCT a.address_street) FROM address a;

+----------------------------------+
| COUNT(DISTINCT a.address_street) |
+----------------------------------+
|                                2 |
+----------------------------------+
```

## `GROUP BY`

If you have 100 columns for which you want to compute the sum (ex/ sales of a company) and want to break down the sums by region (ex/ 4 regions each with 25 rows), you would use `GROUP BY`.

Example: What is the count of every unique first name among my contacts?

```
SELECT COUNT(DISTINCT p.person_first_name), p.person_first_name
FROM person p
GROUP BY p.person_first_name;
```

Result:

```
+-------------------------------------+-------------------+
| COUNT(DISTINCT p.person_first_name) | person_first_name |
+-------------------------------------+-------------------+
|                                   1 | Fritz             |
|                                   1 | Jon               |
|                                   1 | Shannon           |
+-------------------------------------+-------------------+
```

## `HAVING`

`HAVING` comes after `GROUP BY` and is basically `GROUP BY`'s version of a `WHERE` clause.

Example: What is the count of unique first names among my contacts that appears at least 5 times?

```
SELECT COUNT(DISTINCT p.person_first_name) AS NameCount
FROM person p
GROUP BY p.person_first_name
HAVING NameCount >= 5;
```

Result: Empty set

## `JOINS`

- A join in SQL is creating a new result set from two or more tables
- Joins are made by extending the `FROM` clause to include all of the tables you wish to query
- The vast majority of the time, you will also extend the `WHERE` clause to include an equality expression that includes columns from each of the joined tables
 - Most often these columns will be primary or foreign keys
 - Using the relations between the Tables to answer more complex questions

### `CROSS JOIN`

- `CROSS JOIN` is the simplest type of `JOIN` but perhaps the least useful
 - Also incredibly inefficient
- The `CROSS JOIN` computes a Cartesian Product
 - All the rows and columns from both tables
- It isn't very useful because there isn't any connection between the rows
- `CROSS` keyword is optional

Example: What are all the first names and email addresses I have?

```
SELECT p.person_first_name, e.email_address
FROM person p, email_address e;
```

### `INNER JOIN`

- `INNER JOIN` is the expected "typical" join in a relational system
- Take all the rows from Table A
 - Find all the rows in Table B where a key in Table A is equal to a key in Table B
 - Make a new result set with all those rows
- `INNER JOIN` goes between table names in the `FROM` clause
- The `ON` clause follows the `INNER JOIN` clause
 - Boolean expression to match the key columns

Example: What are all my contacts' email addresses?

```
SELECT p.person_first_name, p.person_last_name, e.email_address
FROM person p
INNER JOIN email_address e
ON p.person_id = e.email_address_person_id;
```

Result:

```
+-------------------+------------------+-----------------------+
| person_first_name | person_last_name | email_address         |
+-------------------+------------------+-----------------------+
| Jon               | Flanders         | jon.flanders@mail.com |
| Jon               | Flanders         | jonf@anothermail.com  |
| Fritz             | Onion            | fritz@mail.com        |
+-------------------+------------------+-----------------------+
```

Notice that in the `ON` clause, we are matching the first table's primary key to the 2nd table's foreign key.

### `OUTER JOIN`

- The difference between the `INNER JOIN` and the `OUTER JOIN` relates to `NULL`
- The `INNER JOIN` only joins against rows where there is a match in the joined table
- `OUTER JOIN` works even when a row in one of the tables doesn't match with a row in the joined table
 - i.e., even when there is no row that matches the `WHERE` clause expression
- A `FULL OUTER JOIN` will return a result set with all the joined rows
 - The rows from the first table will also be returned, matched with `NULL` values for the second table's columns
 - If there are any rows in the second table that match the expression, they are also returned - and the cells from the first table will be `NULL`

Example: What are all my contacts and their email addresses, including the ones missing an email address and the ones with an email address but missing a contact name?

```
SELECT p.person_first_name, p.person_last_name, e.email_address
FROM person p FULL OUTER JOIN email_address e
ON p.person_id = e.email_address_person_id;
```

Note: For some reason this isn't working for me in MariaDB, although `LEFT OUTER JOIN` and `RIGHT OUTER JOIN` work just fine.

`LEFT OUTER JOIN` only has `NULL`s on the right, whereas `RIGHT OUTER JOIN` only has `NULL`s on the left

Left outer join: What are my contacts and their email addresses, including those I don't have an email for?

Right outer join: What are my contacts and their email addresses, including those I don't have a person for?

### Self `JOIN`

- It is odd but sometimes useful to join a table against itself
- There isn't any specific syntax for this `JOIN`, but it is worth mentioning that the same table can be on both the left and right hand side of a JOIN clause

# Adding, Changing, and Removing Data

## Insert

- The `INSERT` command enables you to put new data into a Table
- Really should be known as `INSERT INTO`
 - `INTO` is a required part of the command
- Table name follows `INSERT INTO`
 - Only one Table at a time is allowed
- The list of columns follows in a set of parens
 - Separated by commas
 - All required columns must be present
- After the columns comes the `VALUES` keyword
 - Followed by a parens that hold onto the list of values to be put into each column
 - Each column in the list must have a matching value in the value list

Example:

```
INSERT INTO person (person_id, person_first_name, person_last_name, person_contacted_number, person_date_last_contacted, person_date_added)
VALUES (4, 'Joe', 'The Plumber', 8, '2016-09-14 11:43:31', '2016-01-14 11:43:31');
```

## Inserting Multiple Rows

- You can insert multiple rows using multiple value lists
 - Separate each value list with a comma
- You can also use a query to select multiple rows "into" another Table

Example:

```
INSERT INTO person p
SELECT * from old_person op
WHERE op.person_id > 300;
```

## `UPDATE`

- `UPDATE` enables you to change one or more columns in a Table
- Without a `WHERE` clause, `UPDATE` (like `DELETE`) will affect all the rows in the Table
- `SET` keyword follows Table name (and alias)

Example:

```
UPDATE email_address e
SET e.email_address_person_id = 5
WHERE e.email_address = 'aaron@mail.com';
```

## `DELETE`

- `DELETE` deletes one or more rows
 - Permanently! Be careful!
- Command is actually `DELETE FROM`
- Table name comes after the `FROM`
- Only one Table at a time is allowed
- With no conditions - `DELETE FROM` deletes all rows!

###### Transactions

- A transaction is a specific computer science concept
 - Note the same as an ATM or bank transaction
- A Transaction is a construct that creates a "context" around one or more SQL statements

Transaction Syntax

```
START TRANSACTION;
DELETE FROM
person;
COMMIT;
-- or ROLLBACK;
```

The idea is that if you accidentally deleted more than you intended, you can roll back the transaction and try again.

Delete example:

```
DELETE FROM person p
WHERE p.person_first_name LIKE 'J%';
```

Another example:

```
DELETE FROM person p
WHERE p.person_id IN (1,2,3,4,5,6)
```

A delete might fail with a message, *a foreign key constraint fails*. This happens when you try to delete a row that another table references. You have to delete rows in reverse order of their references.

# Creating Tables

## Introduction

- There is a whole set of SQL commands that relate to creating and modifying constructs in a database
- These are classified as DDL
 - Data Definition Language
 - Fomrally, it is a subset of SQL
- Many RDMS products have visual tools to help you create Tables and relations

Amusingly `CREATE DATABASE` and `USE DATABASE` are not part of `ANSI SQL`.

## `CREATE TABLE` Example

```
CREATE TABLE email_address
(email_address_id INTEGER,
email_address_person_id INTEGER,
email_address VARCHAR(55));
```

## `NULL` or `NOT NULL`

- After the column type, you can specify `NULL` or `NOT NULL`
- NULL is the default
 - In fact, ANSI SQL disallows the default to be specified (most DBs allow it however)
 - Means that `NULL` values are valid values for that column in a particular row
- `NOT NULL` means that a valid value is required for that column
 - An attempt to insert a `NULL` value will fail
 - An attempt to use `INSERT` without specifying a value for that column will fail

## `PRIMARY KEY` & `CONSTRAINT`

###### Primary Key

- After the data type you can add the `PRIMARY KEY` constraint instead of `NULL` or `NOT NULL`
 - `PRIMARY KEY` columns are implicitly `NOT NULL`
- It is possible for more than one column to form together as a compound `PRIMARY KEY`

###### Constraints

- You can add a `CONSTRAINT` clause at the end of your column definitions
 - You can add the `PRIMARY KEY` constraint this way if you want to
 - You can add `FOREIGN KEY` constraints

Example:

```
CREATE TABLE phone_number
(phone_number_id INTEGER NOT NULL,
phone_number_person_id INTEGER NOT NULL,
phone_number VARCHAR(55) NOT NULL,
CONSTRAINT PK_phone_number PRIMARY KEY
(phone_number_id))
```

`PK_phone_number` is the `CONSTRAINT` name, `PRIMARY KEY` is the `CONSTRAINT` type, and the column it applies to is `phone_number_id`.

## `ALTER TABLE`

- `ALTER TABLE` enables you to add or change a column definition or `CONSTRAINT` on an existing Table
 - Often used in database creation script to enable correct order of operations (common to create all the tables and then add in all the foreign key constraints)
- Allows you to do anything you could do in `CREATE TABLE` definition
- Warning - doesn't work if Table already has data in it that conflicts with the change
 - For example, you can't change a column to `NOT NULL` if it already has `NULL` data in it
 - Need to use some sort of temporary Table to hold the original data, change it, `ALTER` the Table, and then put the data back

Exmaple:

```
ALTER TABLE email_address
ADD CONSTRAINT FK_email_address_person
FOREIGN KEY (email_address_person_id)
REFERENCES person (person_id)
```

## `DROP TABLE`

- `DROP TABLE` command removes the Table from the database
 - Also removes all the data
- Use it wisely
- You often have to deal with relations
 - Can't delete Table that has a column referenced by another Table as a foreign key

## Demo: `CREATE TABLE`

It is typical to create all your tables first, then use `ALTER TABLE` to add in your foreign key constraints.

---

Importing a file into `mySQL` is as simple as entering the following into the terminal:

`mysql --user=root --password=<password> < <file>.sql`

Note that the author used the `mysql` command from Desktop, so you should be able to configure it to run from anywhere.

Look into stored procedures.