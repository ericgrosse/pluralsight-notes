# Getting Started

Look into `apigility.org`, it's a tool for creating APIs

## Introduction

"Any application that can be written in JavaScript, will eventually be written in Javascript" - Jeff Atwood

# AngularJS 101

## Data Binding

You can give an alias to a controller using the `as` keyword:

```
<div ng-controller="CountryController as countryCtrl">
    {{ countryCtrl.countries.name }}
</div>
```

In the controller, you can use `this` instead of `$scope` if you'd prefer.

# PHP as an AngularJS Backend

## Protocols and Data Formats

PHP comes with excellent support for JSON

## Using JSON with PHP

###### Creating JSON

```
$data = array('one' => 1, 'two' => 2);
echo json_encode($data);
```

###### Parsing JSON

```
$json = '{"one": 1, "two": 2}';
var_dump(json_decode($json));
```

## Creating the Model Classes

Author created two PHP classes in separate files, `Country.php` and `State.php`. These will be used for our data model.

## Creating the Repository Class

Author also added a file `CountryRepository.php`, as follows:

```
<?php

require 'Country.php';
require 'State.php';

class CountryRepository {
	private static $countries = [];

	protected static function init() {
		$countries = [];

		array_push($countries, new Country('Austria', 'at', [new State('Styria'), new State('Vienna')]));
		array_push($countries, new Country('Canada', 'ca', [new State('Ontario'), new State('Quebec')]));
		array_push($countries, new Country('Luxembourg', 'lu'));

		self::$countries = $countries;
	}

	public static function getCountries() {
		if(count(self::$countries) === 0) {
			self::init();
		}
		return self::$countries;
	}
}

?>
```

## Implementing the PHP Service

Add a folder `services` with the file `getCountries.php` with the following:

```
<?php

require '../classes/CountryRepository.php';

header('Content-type: application/json');

$countries = CountryRepository::getCountries();
echo json_encode($countries);

?>
```

## Sending HTTP Requests with AngularJS

Setting up the HTTP request to our PHP file is surprisingly easy:

```
(function() {
	var app = angular.module('funwithcountries', []);

	app.controller('CountryController', function($http) {
		var self = this;

		$http({
			method: 'GET',
			url: 'services/getCountries.php'
		}).success(function(data) {
			self.countries = data;
		});

	});
})();

```

## Using an AngularJS Service

Since we will probably make several HTTP requests to that same `services/getCountries.php` url (and similar urls), it makes sense to convert that code into a re-usable service:

```
(function() {
	var app = angular.module('funwithcountries', []);

	app.factory('CountryService', function($http) {
		var baseUrl = 'services/';

		return {
			getCountries: function(path) {
				return $http.get(baseUrl + path + '.php');
			}
		};
	});

	app.controller('CountryController', function($http, CountryService) {
		var self = this;

		CountryService.getCountries('getCountries').success(function(data) {
			self.countries = data;
		});

	});

})();
```

# More AngularJS

## Two-way Data Binding

To be continued