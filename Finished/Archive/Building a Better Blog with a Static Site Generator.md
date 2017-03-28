# Why Do My Blog Pages Take Forever to Load?

## Advantages of a Static Site Generator

- Speed
- Scalability
- Reliability
- Lower Attack Surface
- Version Control
- Tools
- Zero lock-in

Since you do all your processing once and deploy plain old HTML, CSS and JavaScript files to your readers, it is literally impossible to have a faster blog. Since the blog is 100% stateless, you can scale it to as many servers as you like, giving you practically infinite scalability. Next, you have maximum reliability because you have the least amount of moving parts that can break. You will never have a database error if you don't have a database, for instance.

No website is immune from attack, but a static website with no logins will obviously have fewer entrypoints than a CMS. Securing a website is like securing a house: The fewer doors and windows you have, the harder it is to break in.

Odds are that if you work with code at any level, you have a favorite text editor. It's an old friend whose quirks and strengths you know by heart. Why not use that to write your blog instead of a generic blog's engine editor?

Since most static site generators use a similar for storing blog posts in plain text, you can avoid locking yourself in to specific blog engines.

## Traditional CMS vs Static Site Generator

Traditional CMS: When I write a post, it's saved into a database. When a reader asks to see that post, Wordpress fetches the content of that post from the database, picks the appropriate template, applies styles and other modifications the theme requires, and then sends the page to the reader. Unless we implement an extra caching step, it does this for every single reader, even if the blog post was written years ago.

Static site generator: I write a post in a text editor and save it in my local file system. The generator loads and runs the content metadata from the file, picks the appropriate template, applies styles and other modifications the theme requires, and then saves basic HTML to another file ready to be served. I upload the plain HTML, CSS and JavaScript files to my web server. Whenever a reader asks to see that post, possibly years later, the web server simply sends the page to the reader immediately.

## The Writing Experience

- Write Markdown in text editor
- Render site locally
- Publish with Git

# What Are the Top 10 Static Site Generators?

Check out [staticgen.com](staticgen.com). It lists static site generators by stars, forks, issues and title, and you can filter by programming language.

- Jekyll: Isn't specifically for blogging, written in Ruby
- Octopress: Built on top of Jekyll and meant for blogging
- GitBook: Geared towards books, not blogs
- Hexo: Author's choice

# Creating Our Blog with Hexo and DocPad

## Introduction

YAML: **Y**AML **A**in't **M**arkup **L**anguage
YAML is a human friendly data serialization standard for all programming languages
Example:

```
---
title: 'A title'
date: 2015-08-02 19:38:58
tags: ['Greeting', 'Hello', 'Being Friendly']
---
```

Markdown is a text-to-HTML conversion tool for web writers

## Install Hexo, Create New Blog

When you add tags in the metadata of your first blog post, Hexo will add a Tags and Tag Cloud widget. The tag cloud makes tags that are used more often appear bigger.

Permalinks are blogspeak for the actual url for a specific post (ex/ `/2015/08/08/hello-world`)

In `_config.yaml`, can configure permalink to, for example, not include the day in the permalink (just remove `:day`)

Under the `scaffolds` folder, `post.md` configures the metadata for each blog post you make. So, for example, you could give the `Default` tag to every post.

###### Adding Images

`mkdir images` in the project's `source` folder. Keep in mind that any directory under `source` will just get directly copied to the output without any changes.

The markdown for an image is the same for a hyperlink but prefixed with an exclamation mark

`![Mountain Stream](/images/mountainStream.jpg)`

## Install DocPad, Create New Blog with Casper

Analogy: If the blogging platforms were cars, Hexo would be a solid family car with automatic transmission and power steering. DocPad would be a car with manual transmission that you built yourself from a kit because you really enjoy working on cars and don't trust anyone else to build one to your specifications.

### Installation Steps

```
npm install -g docpad@6.78
mkdir myBlog && cd myBlog
docpad run
```

You will get to select from 22 different skeletons. For now, pick Casper.

For some reason, the site gets hosted on `http://desktop-ndub012:9778/` instead of localhost

There's no built-in `docpad` command for making new posts so you have to go the old-school route of manually creating new posts in your IDE and copy-pasting in the metadata.

## Starting from Scratch with DocPad

The url `http://foo.com/posts/mypost.html` gets mapped to `/render/posts/mypost.html.md` in the project folder structure.

DocPad is the base
Plugins are the bricks

Essential Plugins
- Render Markdown to HTML: marked
- Templating engine: eco
- Paging: paged
- Tagging: tags
- Partial Files: partials

`docpad install <plugin>` to install plugins

## Installing DocPad and Create Custom Blog

Use `docpad run` again, but this type pick No Skeleton

Some optional configuration steps (renaming folders and using `docpad.js` instead of its coffeescript counterpart):

```
cd src
mv documents render
mv files static
cd ..
touch docpad.js
```

Install some plugins:

```
docpad install marked
docpad install eco
docpad install paged
docpad install tags
docpad install partials
```

Note: At this point I kind of lost interest in DocPad, still taking notes for Hexo however.

# Enhancing Our Blog with a Bootstrap Theme

## Introduction

Styling options for a blogger:
- Modify default theme
- Modify/adapt WordPress theme
- Roll our own
- Modify/adapt Bootstrap theme

WordPress are difficult to adapt since they are a blend of HTML, CSS and embedded PHP code.

Plan
- Hexo: Modify default theme
- DocPad: Add a BootStrap theme

A good blog structure has a menu, a banner image and a two column content area

## Modifying Our Hexo Blog

Hexo uses Stylus as its CSS preprocessor

To change the banner image, go to `_variables.styl` and change `banner-url`.

A good tool for picking color's is adobe's color wheel (google color.adobe.com). An incredibly helpful option is that you can upload an image and it will pick colors for you based on the "Color Mood" of your image. Once you have your desired color mood, click the color wheel icon at the top-right to pick the hex value you want.

When in doubt, cycling through the color options in the Dev tools can be really helpful for getting roughly the color you want (you can then go to a color wheel and get a more exact color)

If you reload the page, the banner flashes because the url is coming from CSS. The workaround is to put the banner into the HTML as an `img` tag. In `header.ejs`, add

`<img src="/css/images/banner.jsp style="display:none;" alt="" />`

## Modifying the Blog Home Bootstrap Theme

[startbootstrap.com/template-overviews/blog-home/](startbootstrap.com/template-overviews/blog-home/) and click the download button (or fork from github)

`<div class="row jumbotron">` used as a banner

```
.jumbotron {
	background: url("img/banner.jpg") no-repeat center;
    background-size: cover;
    color: white;
}
```

Author also uses this property a lot
`text-shadow: 8px 8px 14px rgba(62,62,62,1)`

A nice `background-color` for the entire blog is `snow`

Some really nice styling for the articles:

```
article {
	background-color: snow;
    padding: 1rem 2rem 2rem 2rem;
    margin-bottom: 2rem
    box-shadow: 3px 3px 14px gray;
}
```

## Add an RSS Feed to Hexo

Amusingly, Hexo has an RSS feed symbol in the top-right corner, but clicking on it gives a message `Cannot GET /atom.xml`

To fix this:

`npm install hexo-generator-feed --save`

Then in `_config.yml`, under `theme: landscape`, add:

```
feed:
  type: atom
  path: atom.xml
  limit: 20
```

I'm assuming the plugin's readme covers what these config options actually do

## Add Disqus to Hexo

Login to your Disqus account and click Gear Icon -> Add Disqus to Site. Make sure to use the Universal Code option. You'll be given some code to copy-paste into your project, although you don't need to do this since Hexo does this work for us.

In `_config.yml` add
`disqus_shortname: <name>`

Also need to change the `url` property in `_config.yml` to point to `localhost:4000`

At this point Disqus works with the blog, although it needs further configuration.

In `themes\landscape\layout\_partial\article.ejs`, cut the following code from the bottom of the page

```
<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
  <div id="disqus_thread">
    <noscript>Please enable JavaScript to view the <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
  </div>
</section>
<% } %>
```

and paste it under the `footer` tag. Note that you won't see the Disqus comments directly on the homepage, you have to click a specific article to see them.

## Add MailChimp to Hexo

A lot of websites follow this template for success:

- Give away content
- Build mailing list
- Make premium content
- Offer premium content to subscribers

Author recommends using MailChimp.

The mailing address is required by law and will be available to every single person you email so you probably shouldn't use your home address. Author recommends using a post office box.

After the annoying registration process, you create a new list, then a signup form (use classic and copy paste code into `articles.ejs`). Wrap the code in `<div class="subscribe article-footer>` and place it after the `</footer>` but before the Disqus code.

You should see a nice form for subscribing to a mailing list.

## Add Google Analytics to Hexo

You only need the Tracking ID, not the full script code to copy-paste.

Need to edit the `themes\landscape\_config.yml` file, not the global `_config.yml` file. There will be an empty `google_analytics` property, which is where you paste the tracking ID.

# Workflow: Writing Our Blog and Deploying it with Git

## Writing Your Blog Posts

Learn to use Snippets in Sublime, it will save you so much time. Author is using snippets to generate the YAML metadata for his blog posts.

Example of a Snippet written in atom (`snippets.cson` file)

```
'.source.gfm':
	'Insert YAML metadata':
    	'prefix': 'head'
        'body': """
        ---
        title: "${1:Title}"
        type: "${2:post}"
        date: "2015-${3:07}-${4:04}"
        tags: ['${5:Programming}']
        ---
        $6
    """
```

Now when you type `head` + `<Tab>`, the YAML metadata will auto-generate and you can tab through each property & add them.

Useful application for a snipet: Adding a hyperlink or image, or really any block of non-trivial HTML/markdown you use repeatedly

It would be really useful to have a snippet for multiline code with bash rendering

Honestly, proper application of snippets makes writing a blog post in native HTML seem feasible

###### Markdown Preview

In Atom, if you do Ctrl + Shift + P and search for markdown, you will find a feature called preview toggle. This creates a split view with markdown on the left and a preview on the right.

## Deploying with Git

Supposedly, all you have to do is clone your output repository to your website's root folder.

Author presents 3 hosting options
- GitHub Pages
- Linux VPS with NGINX
- Microsoft Azure Web App

Github Pages is free and easy, but limited to one website per account.

Linux VPS with NGINX is more difficult and has a variable cost, but offers complete flexibility. You need to set up a VM with a web server, and SSH into the server. Once there, you git clone the repository.

Microsoft Azure Web App is easy but overly expensive.

## Putting It All Together: Writing and Deploying

There are 2 local repositories: One for the blog's root folder, and one for its `out` or `dist` folder (depending on how you named it). You can think of these as Source and Publish repositories.

In the Source repository, add `node_modules/` and `out/` to your `.gitignore`.

The 2 local repositories will correspond to 2 remote repositories on Github.

###### NGINX Setup

Assuming you see `eri<c@<machine>:~$ ` after SSHing into the web server, enter the following:

```
cd /var/www/html
sudo rm *
cd ..
git clone <out repository> html
```

And that's it. Every push to the Github Publish repository will automatically trigger an update in NGINX's repo (I think?). According to the author, the new local repository on the server will sync with the source.

The Visual Studio Code IDE has some pretty impressive Git support

# Bringing Our Old Blog up to Speed

## Import a WordPress Blog into Hexo

`npm install hexo-migrator-wordpress --save`

In WordPress admin, go to Tools -> Export -> Download Export File. This will download a special XML file. Then enter:

`hexo migrate wordpress <path to xml file>`

This runs through the WordPress posts, converts them to markdown for us and adds the YAML metadata. Unfortunately the migration botches the images.

Download the downML plugin in WordPress. This creates a new left menu, which lets you download a `medialibrary.zip` file. Unzip the folder and drop the images into the Hexo blog's image directory. Then for each image on the blog, you need to manually copy the image path, the alt text and the caption.

If you have hundreds of posts to migrate, this isn't really feasible and you need to write a program to automate fixing your image tags.