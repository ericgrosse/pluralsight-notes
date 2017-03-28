# Introduction

The exercise files are pretty cool, especially the `Example Page` which has separate tabs for `Content`, `HTML`, `CSS` and `JavaScript` (basically JSFiddle without the editing)

## Life Without jQuery

Here's all the work required to add and remove a class name from an element in native JavaScript:

```
var h2 = document.getElementsByTagName('h2');
h2[0].className += ' highlight';
var classNames = h2[0].className.split(' ');
classNames.splice(classNames.indexOf('highlight'), 1);
h2[0].className = classNames.join(' ');
```

Whereas in jQuery:

```
$('h2').addClass('highlight');
$('h2').removeClass('highlight');
```

# Finding Parts of a Page

## Selectors in a Web Page

If you inspect an element in the dev console, you'll notice the DOM hierarchy for reaching that element at the very bottom. If you hover over any of the tabs, you can directly inspect any of the element's ancestors!

## Demo: CSS3 Selectors

To find all anchor tags whose `href` begins with `http`:

`$('a[href^="http://"]')`

## HTML Literals

Currently here (course is too basic)