# jQuery Fundamentals

jQuery 2.x is supported across all modern browsers, use that

## Using Content Delivery Networks

Benefits
- Caching
- Regionality (big sites have data centers all over the world)
- Parallelism

A major drawback is if that you wouldn't be able to use your script if the CDN is down. A workaround is to make a fallback script as follows:

```
<script type="text/javascript" src="[CDN url]"></script>
<script>
	window.jQuery || document.write('<script src="jquery.js")></script>')
</script>
```

## Using the jQuery `ready()` Function

Code inside of `$(document).ready` runs when the DOM is loaded (not all the images or CSS have necessarily loaded). You don't need to use `$(document).ready` if you put the script using jQuery at the end of the `body` element.

## Getting to Know the jQuery Documentation

On the official site's API documentation, you'll see in the left pane an option called `Manipulation` which includes topics such as DOM inserstion, DOM removal, etc.

# Using jQuery Selectors

## Selecting Nodes by Tag Name

To select multiple tags in one query:

`$('p, a, span')`

To select descendants of an ancestor:

`$('table tr')`

## Selecting Nodes by Class Name

Currently Here (Course is too basic)