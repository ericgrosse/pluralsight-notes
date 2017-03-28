# Introduction

## About Jekyll

Jekyll uses plain text and supports markup languages like Markdown and Textile. In fact, when we install Jekyll, it will enable Markdown by default.

For the layouts, Jekyll uses the liquid templating language. This is a Ruby library that was built originally for the Shopify ecommerce system, and they extracted it out of Shopify and offer it up as its own library for use.

# Installing Jekyll

## Installing Jekyll with Ruby Gems

Jekyll isn't officially supported on Windows. However the Jekyll documentation does include some information about installing on Windows.

Follow [these instructions](http://jekyll-windows.juthilo.com/1-ruby-and-devkit/) to install Ruby and the Ruby DevKit. Then in the terminal:

`gem install jekyll`

Author also installs the `rdiscount` gem because he prefers this markdown library over the default Kramdown library.

# How Jekyll Works

## Exploring the Jekyll Files and Directories

In our posts, the `YAML` frontmatter is saved into variables to be used in our template.

`_site` is the generated site.

All the directories without an underscore get pulled directly into `_site` upon compilation.

The most important file is `_config.yml`.

`_layouts/default.html` is essentially our base layout, and it has all of the reusable content and markup for every page on the site, and what is inheriting it will be placed where the `{{content}}` variable is.

`_layouts/post.html` is for the single entry, and it inherits from the default layout.

# Configuring the Site

## Generating a New Jekyll Site

```
jekyll new <name-of-site>
cd <name-of-site>
jekyll serve
```

Then navigate to `localhost:4000`.

Author ported over `images`, `javascripts`, `stylesheets`, and the content of `_posts` from his exercise files, and deleted the `css` directory.

## Jekyll Posts and YAML

`YAML` is a human-friendly data serialization standard that makes it easy to structure data for use by high-level programming languages, and is really popular in the Ruby and Python communities.

Feel free to delete `twitter_username` and `github_username` from `_config.yml`.

Author also configured is `_config.yml` file as follows:

```
markdown: rdiscount
rdiscount:
	extensions: [smart]
permalink: /articles/:title
```

# Building the Templates

## Building the Default Layout

Style `default.html` however you like: Use google fonts, import bootstrap, etc.

If you replace the page title with:

`<title>{{site.title}}</title>`

This will pull the title used in the `_config.yml` file. Can use the other `YAML` properties in a similar fashion.

Make sure to include ``{{content}}`` somewhere.

## Learning Liquid

The homepage is actually the root `index.html` file.

Liquid syntax:

```
{% for post in site.posts %}

{% endfor %}
```

and

```
{{ post.title }}
```

Another interesting feature is post limiting and offsetting:

```
{% for post in site.posts limit: 3 %}
```

```
{% for post in site.posts offset: 3 limit: 10 %}
```

## Generating the Site

The command `jekyll serve` both generates the `_site` files and fires up a localhost server. You can even set up your own localhost server that points to the `_site` directory.

## Building the Posts Template

In `post.html`, the page's title is accessible through `{{page.title}}` and content is available via ``{{content}}``.

Related posts are generated automatically by Jekyll when you build the site, and they are described by Jekyll as "low-quality results, but really fast to generate."

```
{% for post in site.related_posts limit: 10 %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
```

If you want to use something more powerful for generating related posts, you can use `LSI` (latent semantic indexing), and this will allow you to create a collection of related posts and really get better results in trms of how they are related, and author recommends this if you are really looking to have those related posts be very, very specific.

`jekyll serve --lsi`

###### `watch` flag

Instead of running `jekyll serve` for every little change, we can instead do `jekyll serve --watch` to monitor any changed files and automatically rebuild those files based on the changes. Note that this won't work when changing `_config.yml` (need to stop and rebuild the site in that case).

## Building a Posts Archive Template

Create a new file `archive.html` at the root of the project with the following contents:

```
---
layout: default
title: "My Archive"
---

<!-- HTML content -->
{% for post in site.posts %}
	<h4><a href="{{ post.url }}">{{ post.title }}</a></h4>
    <p>{{ post.content | truncatewords:50 }}</p>
{% endfor %}
```

Note the use of the pipe `truncatewords`, this could definitely come in useful.

## Creating a Feed

Author pretty much showed a `feed.xml` file you can just copy/paste and not have to worry about.

Assuming you have `jekyll serve --watch` running, in another terminal tab do `curl http://localhost:4000/feed.xml` and see what's outputted.

In `index.html`, can add a paragraph at the end with a link to `/feed.xml`.

# Deploying to Github Pages

Skipped, don't really care about deploying to Github pages.

