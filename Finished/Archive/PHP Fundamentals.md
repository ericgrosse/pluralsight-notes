# Introduction

## History of PHP

PHP used to stand for Personal Home Page. Now it stands for PHP Hypertext Preprocessor.

## Sample Programs

PHP is a weakly typed language

`<?=$title?>` same result as `<?php echo $title ?>`

# General Types

## Basics - Comments, Case Sensitivity

Three type of comments

```
// C++ line comment

/*
C-style comment block
*/

# Shell style comment
```

---

Class names and function names are NOT case sensitive. Company is the same as CoMpAnY.

## Type - Integer (Demo)

Can use `var_dump` to check a variable's value as well as its type

## Type - Boolean (Demo)

You can cast values to boolean using `(bool)`. Example:

`$hasValue = [];`
`var_dump((bool)$hasValue); // bool(false)`

## Type - Constant (Demo)

Constants are defined as follows:

```
define('NEW_CONSTANT', 'Hello new constant');
echo NEW_CONSTANT; // Hello new constant
```

Constants are global variables

## Determine Variable Types

Use functions `is_int`, `is_string`, `is_bool`, and so on. If true, it returns 1, otherwise it returns nothing (wtf?)

To check if a constant is defined, use the `defined` function.

# Functions

## Default Parameters (Demo)

Can set default parameters in the function definition's argument list:

```
function someFunction($someVar = 'default') {
	echo $someVar;
}
someFunction() // default
```

## Variable Functions (Demo)

Strangely enough, you can pass the name of a function as a string to a variable, then invoke it:

```
function getAuthor() {
    echo 'Charles Dickens';
}

$variableFunctionName = 'getAuthor';
$variableFunctionName();
```

You can also do the JavaScript approach:

```
$variableFunctionName = function() {
	echo 'Charles Dickens';
};
$variableFunctionName();
```

## Global Variables (Demo)

Use the `global` keyword (no point in doing this unless it's declared inside a function)

# Strings

## Single Quoted Strings (Demo)

Strings enclosed in single quotes will render variable names as-is instead of interpreting them. Example:

```
$currency = 'penny';
$sampleString = 'A $currency saved is a $currency earned.';
echo $sampleString; // A $currency saved is a $currency earned.
```

This is also true of escape characters (except when escaping quotes). Example:

`echo 'St. Patrick\'s Day\n'; // St. Patrick's Day\n`

## Double Quoted Strings (Demo)

Unlike single quoted strings, variables and escape characters get interpreted.

Two approaches to combine variables and strings:

```
$var = 2;
echo "{$var}nd place";
// or
echo $var."nd place";
```

You're allowed to place naked single quotes within a double quote, for example:

`$misc = "St Patrick's Day";`

## Here Document (Demo)

Here docs are another way to use strings in PHP.

`echo <<<EOT`
`Be not afraid of greatness;`
`some are born great,`
`some achieve greatness,`
`and others have greatness thrust upon them.`
`- William Shakespeare`
`EOT;`

(Note: I tried this and it didn't interpret newlines)

## `print()` Demo

`echo` and `print` are basically interchangeable, and you can surround `echo/print` statements with brackets if desired.

One difference with echo is that you can comma separate your values.

`echo 'Et', ' tu, ', 'Brute';`

## String Functions (Combining Several Sections)

- `strtoupper`
- `strtolower`
- `strlen`
- `strpos(string, searchTerm, offset) // case sensitive`
- `str_replace(newTerm, oldTerm, string)`
- `substr(string, startPos, [, length]) // startPos and length can be negative`
- `str_split(string, substringLength) // splits into an array`

Some weirdness in the `str_replace` function: The 4th (optional) parameter is not a parameter we're passing in, but rather a parameter we're getting back from the PHP function itself.

```
$quote = "To be or not to be? That is the question.";
$replaced = str_replace("be", "know", $quote, $count);

echo $replaced; // To know or not to know? That is the question.
echo $count; // 2
```

Notice that `$count` hasn't been declared anywhere. This is a variable that's been declared in the PHP function and then passed back to us. It is passed by reference.

# Arrays

## Indexed Arrays (Demo)

Arrays can be defined as follows:

`$authors = array("Charles Dickens", "Jane Austin", "William Shakespeare");`
or
`$books = ["Little Women", "Jane Eyre", "Richard III"];`

## Associative Arrays

Use arrow notation for key-value pairs:

```
$authors = [
	"quirky" => "Charles Dickens",
	"brilliant" => "Jane Austin",
	"poetic" => "William Shakespeare"
];
print($authors["quirky"]); // Charles Dickens
```

Can still use integers as the keys in associative arrays.

If you place a value in the array without a key, it will automatically be assigned an integer key.

You can check if an array key exists as follows:

`array_key_exists("quirky", $authors);`

## Other Functions (Combining Several Sections)

- `in_array(value, array, [, type]) // checks if a certain value exists in the array`
- `array_push(array, value)`
- `array_pop(array)`
- `count(array [, countRecursive]) // gives the length of the array`

---

Another way you can push to an array:

`$array[] = "value";`

In fact, this method is preferred since you would get an error using `array_push` on an undeclared array.

For associative arrays, you would do the following:

`$array["key"] = "value";`

## `unset()`

The `unset` function is not specific to arrays, it can be used for any variable type. Example:

```
$authors = ["Charles Dickens", "Jane Austin", "William Shakespeare"];
unset($authors[1]);
print_r($authors); // Array ( [0] => Charles Dickens [2] => William Shakespeare )
```

Can unset multiple variables at once:

`unset($authors[1], $authors[2])`

## Sorting Arrays

`sort(array)`

Horrible gotcha: If you sort an associative array, it will transform all your keys into indexed values! Workaround: use the `asort` function instead. You can also sort an associative array by key using `ksort`.

## Foreach Loop

Syntax:

```
foreach($array as $val) {}
// or
foreach($array as $key => $val) {}
```

# Classes & Objects

## Accessing Properties

Properties are accessed with its own arrow notation `->` (not to be confused with the `=>` arrow used for associative arrays):

```
class Person {
	public $firstName = "Samuel Langhorne";
	public $lastName = "Clemens";
	public $yearBorn = 1899;
}

$myObject = new Person();
echo $myObject -> firstName; // Samuel Langhorne
$myObject -> firstName = "S. L.";
echo $myObject -> firstName; // S. L.
```

## Creating Constants

Static variables are considered constants to that class and are declared using the `const` keyword, and are conventionally written in all uppercase letters.

## Accessing Constants

Syntax:

`$classInstance::NAME_OF_CONSTANT`
or
`ClassName::NAME_OF_CONSTANT`

## Creating Methods

All variables and methods in a class are `public` by default.

Creating a method is as simple as defining a function within the class.

## `$this`

```
public function getFirstName() {
    return $this->firstName;
}
public function setFirstName($tempName) {
	$this->firstName = $tempName;
}
```

## Constructors

Use the keyword `__construct`:

```
function __construct() {
    echo "I'm in the constructor";
}
```

## Initial Parameter Passing

Can write a constructor that sets each of your parameters, including default values:

```
function __constrcut($tempFirst = "", $tempLast = "", $tempBorn = "") {
	$this->firstName = $tempFirst;
    $this->lastName = $tempLast;
    $this->yearBorn = $tempBorn;
}
```

(Note: What if you want to set the 1st and 3rd parameter, but leave the 2nd one as its default?)

## Inheritance

It works exactly the same as any other object oriented language. In this case, the syntax is `A extends B`.

## Protected

With respect to inheritance, a child class can access the protected properties of its parent class.

If you try and access a protected attribute or method, an error gets thrown: `Fatal error: Cannot access protected property`

## Scope Resolution Operator

A static variable gets resolved using `self::$staticVariable` instead of using `$this`:

```
class Author extends Person
{
	public static $centuryPopular = "19th";

	public static function getCenturyAuthorWasPopular()
	{
		return self::$centuryPopular;
	}
}

echo Author::$centuryPopular.PHP_EOL; // 19th
echo Author::getCenturyAuthorWasPopular(); // 19th
```

## Include File

`include 'Person.php';`
`include_once 'Author.php';`

Using `include_once` is ideal when dealing with files that include classes or functions.

## Require File

Require vs Include - If `include` cannot find the file, it throws a warning, otherwise the rest of the code compiles. If `require` cannot find the file, an error is thrown and execution is terminated.

# Operators & Control Structures

## Arithmetic Operators

`**` is the exponentiation operator

## String Operator

You can shorthand string concatenation as follows:

```
$concat = "William";
$concat .= " Shakespeare";
echo $concat; // William Shakespeare
```

## Spaceship Operator

The spaceship operator `a <=> b` returns -1 if `a` less than `b`, 0 if `a` equals `b` and 1 if `a` greater than `b`.

```
echo 1 <=> 1 // 0
echo 1 <=> 2 // -1
echo 2 <=> 1 // 1
```

## Else If

else if is treated as one word, `elseif`

## Switch Statement

Exactly as you would expect

## Ternary Operator

Exactly as you would expect

## Null Coalesce Operator

This is a strange operator that sort of acts as a convenient shorthand for a special use case of the ternary operator. Best explained with an example:

```
$authors = ["Charles Dickens", "Jane Austin", "William Shakespeare"];
//$count = count($authors);
$outcome = $count ?? $anotherVariable ?? "Count unavailable";
echo $outcome; // Count unavailable
```

If `$count` is falsy, fallback to `$anotherVariable`. If `$anotherVariable` is falsy, fallback to the string `Count unavailable`.

## Alternate Syntax for Control Structures

Use an opening colon and a closed `end<control>`. For example:

```
for($i = 0; $i < 10; ++$i) :
	echo "Some message".PHP_EOL;
endfor;
```

# Databases

## Possible Databases

- MongoDB
- MS SQL
- MySQL/MariaDB
- PostgreSQL
- SQLite

Other databases also accessible through the PHP Extension Community Library

## PDO vs MySQLi

Both are / support:
- Object-Oriented
- Support Charsets
- Multiple Statements
- Server-Side Prepared Statements
- Stored Procedures

###### Differences

MySQLi
- Can use procedural calls
- Connects to MySQL, MaxDB, MariaDB

PDO
- Supports client-side prepared statements
- Connects to all the supported databases in PHP

PDO is newer (released in PHP v5.3)

## Setting up MySQL in Linux

Run `sudo apt-get update`

MySQL is already installed on Ubuntu by default

`mysql -V`

If the above returns nothing or `command not found`, do:

`sudo apt-get install mysql-server`
`sudo apt-get install phpmyadmin`

Select the apache server, follow the steps then navigate to `localhost/phpmyadmin`

## Setting up MySQL on Windows

After you setup XAMPP, make sure you run both Apache and MySQL. Netbeans is well configured to work with XAMPP.

## Creating a Database and Table with phpMyAdmin

Author's words: As a PHP developer, PHPMyAdmin will be your best friend forever.

To access phpMyAdmin, either click the Admin button next to MySQL in the XAMPP control panel, or just navigate to `localhost/phpmyadmin`.

In the top menu, click `User accounts -> Add user account`. In the `Login Information` section, type in a username, `localhost` for host name and a password. For `Global privileges`, check all. Click `Go`.

Create a new database (see the cylinder icon in the left panel). Name it something and select `Create`.

Create a table named `Authors` with 4 columns.

Set the first row's name to id, make it a Primary Key (under `Index`) and select `A_I` (short for auto-increment). The next 3 columns will be `first_name`, `last_name` and `pen_name` each `VARCAR` with length 50. `pen_name` also has the `Null` checkbox checked. Click `Save`.

There are entire Pluralsight courses on phpMyAdmin.

## Executing a Query - Delete, Update, Insert

Add some rows to the `authors` database (not shown in the video but not too hard to figure out)

## Grabbing Inserted Id

Here's the current database code:

```
<?php

$dbPassword = "123456";
$dbUsername = "PHPFundamentals";
$dbServer = "localhost";
$dbName = "PHPFundamentals";

$connection = new mysqli($dbServer, $dbUsername, $dbPassword, $dbName);

// echo "<pre>", print_r($connection), "</pre>";

if($connection->connect_errno) { // if connection to the DB failed
	exit("Database Connection Failed. Reason: ".$connection->connect_error);
}

$query = "INSERT into Authors (first_name, last_name, pen_name) VALUES ('John Ronald Reuel', 'Tolkien', 'J. R. R. Tolkien')";

$connection->query($query);
echo "Newly Created Author Id: ".$connection->insert_id;
$connection->close();

?>
```

After the `INSERT` statement in `$query` gets executed, we can grab its generated id using `$connection->insert_id`.

## Select Data From Database

New code:

```
$query = "SELECT * FROM Authors ORDER BY first_name";
$resultObj = $connection->query($query);

if($resultObj->num_rows > 0) {
	while($singleRowFromQuery = $resultObj->fetch_assoc()) {
		//echo "<pre>", print_r($singleRowFromQuery), "</pre>";
		echo "Author: ".$singleRowFromQuery['first_name'].PHP_EOL;
	}
}

$resultObj->close();
$connection->close();
```

The only thing that's unclear to me is what `fetch_assoc` is doing in order to make the while loop terminate.

## Prepared Statement Example

`bind_param` example:

`$so->bind_param("sdi", $stringVar, $floatVar, $intVar);`

The first parameter is a typestring. It's telling us our first parameter is a string, the 2nd is a decimal (or floating point) and the third is an integer.

New code:

```
$tempFirstName = 'Lucy Maud';

$query = "SELECT first_name, last_name, pen_name FROM Authors WHERE first_name = ?";
$statementObj = $connection->prepare($query);

$statementObj->bind_param("s", $tempFirstName);
$statementObj->execute();

$statementObj->bind_result($firstName, $lastName, $penName);
$statementObj->store_result();

if($statementObj->num_rows > 0) {
	while($statementObj->fetch()) {
		echo $firstName." ".$lastName." (".$penName.")".PHP_EOL;
	}
}

$statementObj->close();
$connection->close();
```

## PDO Example

Differences with `MySQLi`:

Connection string is slightly different:

`$connection = new PDO('mysql:host='.$dbServer.';dbname='.$dbName, $dbUsername, $dbPassword);`

Use `rowCount()` instead of `num_rows`

Use `foreach($resultObj as $singleRowFromQuery)` instead of the while loop

Set `$connection` to null instead of calling `close()` on it

# Web Programming

## Shorten Syntax with HTML

Set up a webpage `index.php` with an associated stylesheet.

At the top of the page, add:

```
<?php
require 'assets/dbInfo.php';

$query = "SELECT id, first_name, last_name, pen_name FROM Authors ORDER BY first_name";
$resultObj = $connection->query($query);
?>
```

`dbInfo.php` has the following contents:

```
<?php

$dbPassword = "123456";
$dbUserName = "PHPFundamentals";
$dbServer = "localhost";
$dbName = "PHPFundamentals";

$connection = new mysqli($dbServer, $dbUserName, $dbPassword, $dbName);

if($connection->connect_errno)
{
    exit("Database Connection Failed. Reason: ".$connection->connect_error);
}

?>
```

To make the select dropdown draw values from our SQL query:

```
<select name="author">
    <?php while($row = $resultObj->fetch_assoc()) : ?>
    <option value="<?=$row['id']?>"><?=$row['first_name']?> <?=$row['last_name']?>></option>
    <?php endWhile; ?>
</select>
```

I honestly find this intertwining of HTML and PHP code horrifying. What an awful separation of concerns, and it's syntactically ugly.

## `$_GET`

`index.php` has a form:

`<form method="get" action="final.php" >`

For the checkboxes, change the name from `"century"` to `"century[]"` to signifiy that the checkboxes should be saved to an array.

When you submit the form, you will be sent to `final.php`, which has its own php and html markup. At the top of the page, add:

```
<?php

echo "<pre>";
print_r($_GET);
echo "</pre>";

echo $_GET['author'];

?>
```

Whatever was sent in the form is captured in the `$_GET` associative array.

## $_POST

In each `span` in `final.php`, display the relevant data from the `$_POST` array. Example:

```
<div>
    <label>Name:</label>
    <span><?=$_POST['name']?></span>
</div>
```

## Form Validation

The form in `index.php` will have an action that redirects to itself instead of `final.php`. Validation code:

```
if(count($_POST) > 0) {
    if($_POST["email"] != "") {
        header('Location: final.php');
    }
    else {
        $emailError = "validation";
    }
}
```

Note the special string inside of the `header` function. This does a page re-direct to `final.php`. The `$emailError` variable is used as a class name as follows:

```
<div class="<?=$emailError?>">
    <label>E-mail Address:</label>
    <input type="text" name="email" />
</div>
```

The CSS for the class `validation` will then handle highlighting the form in red to indicate validation has failed for that input.

## `session_start()`

An issue with our approach to validation is that our `_POST` data didn't carry out to `final.php` when using the `header` function. We can fix this by using sessions.

Create a file called `include.php` whose only content is the declaration `session_start();` and have both `index.php` and `final.php` include this file.

## `$_SESSION`

In the validation block of code, if the validation passes, you want to transfer your `$_POST` data to `$_SESSION`:

```
if($_POST["email"] != "") {
    $_SESSION["formPostData"] = $_POST;
    header("Location: final.php");
}
```

Then in `final.php`, you can now display the post data as follows:

```
<?php

include 'assets/include.php';

echo "<pre>";
print_r($_SESSION);
echo "</pre>";

$postedData = $_SESSION['formPostData'];

?>
```

Then in the HTML body:

```
<div>
    <label>Favorite Author:</label>
    <span><?=$postedData["author"]?></span>
</div>
```

## `session_destroy()`

If you submit the form, navigate back to `index.php`, then navigate again to `final.php`, you'll notice the form data is still there! In fact, you shouldn't even be able to access `final.php` unless you submit the form. Change `final.php` as follows:

```
if(isset($_SESSION['formPostData'])) {
    $postedData = $_SESSION['formPostData'];
    unset($_SESSION['formPostData']);
}
else {
    header('Location: index.php');
}
```

If the session data relating to the form isn't set, you get redirected to `index.php`. If it is set, you store the data in a new variable `$postedData` and unset `$_Session['formPostData']`. This has the effect that even refreshing the page will get you redirected to `index.php`.