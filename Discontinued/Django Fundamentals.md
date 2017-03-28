# Introduction to Django

## Batteries Included

Django comes with a powerful object relational mapping API (ORM), which allows you to write pure Python classes that represent your database tables. That means you don't have to write SQL queries to interact with your database.

Django can generate an admin interface, a web based user interface for editing content.

## Django Principles

- Be more productive with less code
- Write good, clear, clean code
 - Loose couple
 - DRY (don't repeat yourself)
- Very thoroughly documented
- Great, friendly community

# Installing Django

## Demo: Windows Installation

In addition to the python installation, download `ez_setup.py` and `get-pip.py` and double click on each file.

## Virtualenv

### Why We Need Virtualenv

As far as I understand, Virtualenv is similar to local `npm` packages in a Node project. You use it so that everyone working on the code downloads the same dependencies.

Honestly a good argument for sticking with full stack JavaScript is that you can stick with `npm` as your package manager, rather than having to use `pip` and virtualEnv for the backend.

Creating a new virtual environment is as simple as executing `virtualenv myproject`. You can also include a `-p` flag to specify which Python interpreter you want to use.

# Starting a Django Project

## Demo: Starting a New Project

Author starts the new project with the following command:

```
django-admin.py startproject boardgames
```

Not a fan of this: Need to explicitly use a `.py` extension, `startproject` is verbose, it's a lot more awkward than typing say, `express myapp`.

The next command the author runs is:

```
python manage.py runserver
```

Again, not a fan. Have to call `python`, then a python file with the `.py` extension, then finally the actual command. In Node, you could just run `http-server`, `node start` or something similar.

## Demo: Adding a Page

In `urls.py`, we have:

```
url(r'^admin/', include(admin.site.urls)), # originally there
url(r'', 'main.views.home') # added by author
```

The use of `r` before the urls is annoying.

[Stopped here because I found out you can do Python list comprehensions in JavaScript using `map` and `filter`, and can even use `CoffeeScript` to do the same]