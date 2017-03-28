# Introduction to Ruby on Rails 3

## Do I Need to Know Ruby

Ruby specifics worth knowing upfront
- Ruby arrays and hashes
- Symbols (i.e. :name, :some_symbol, etc.)
- returns in methods

Every line of a Ruby program is interpreted. In methods and other code blocks, whatever the last line of code is in that block, that line's interpreted value will be returned automatically whether you issue the return statement or not. (WTF)

## Rails 4 Features

Russian doll caching seems like a cool concept

## Benefits of Rails

Nothing really sticks out (at least when comparing to Node) except for Rails generators (used to quickly stub out code to begin writing business logic)

# Installing Rails and Setting up Your Development Environment

Keep in mind that all you have to do to set up a Node Express backend is install Node (takes like 30 secs) then `npm install express`. Now let's keep in mind everything you have to do to get Rails up and running.

- Install Ruby
- (Optional) Install Ruby Versoin Manager (RVM)
- Install Ruby gems and bundle
- Install Rails itself

## Databases and Rails

Rails 3 and 4 come shipped with SQLite by default (this is definitely a major plus)

## Rails Servers

Rails comes bundled with a development server called WEBrick (not meant for production though). Sounds like it has live reload functionality, which is a big plus.

## IDEs

SublimeText is mentioned, thank god.

Author mentions that code completion support for Rails isn't great.

# Creating Your First Rails Application

## Rails Command Line Overview

- rails options
 - `rails new <app_name>` creates a new rails application with the given name
 - `rails console` starts up the interactive rails console
 - `rails server` starts up the rails server
 - `rails generate` generates various aspects of a rails application
- rails shorthand versions
 - rails c = rails console
 - rails s = rails server
 - rails g = rails generate

## Demo: Creating the Project

When you execute `rails new <project_name>`, rails creates a ton of files for you.

In fact, when I tried it, it created 56 files and 38 folders.

## Rails Project Structure

Rails is an extremely opinionated framework

## Demo: Walkthrough of a Project's Layout

Rails is so different from the development you're accustomed to, it would take a long time to transition to the Rails environment.

## Demo: Starting the Server and Using the Application

After running `rails server` and navigating to `localhost:3000`, you'll see a default UI that is amusingly bad. It looks like a UI made in the 90s.

## Generating a Scaffold

- What is a scaffold?
 - A rails scaffold is a full CRUD implementation of a model
 - Use a generator to create the scaffold
 - Scaffold invludes views, controller, model, db migration and tests
 - A great place to start to see how all of the piece of rails work together
- Resume Scaffold
 - `rails generate scaffold Resume name:string phone:string email:string street:string city:string state:string zip:integer summary:text`
 - Inspect generated code

Creating the rails scaffold above took over 16 seconds

## Demo: Scaffold Through the Code

Rails uses the `erb` templating scheme by default. It's essentially `html` with `erb` script inside of the `html`.

Here is some sample code from an `index.html.erb` file:

```
<tbody>
  <% @resumes.each do |resume| %>
    <tr>
      <td><%= resume.name %></td>
      <td><%= resume.phone %></td>
      <td><%= resume.email %></td>
      <td><%= resume.street %></td>
      <td><%= resume.city %></td>
      <td><%= resume.state %></td>
      <td><%= resume.zip %></td>
      <td><%= resume.summary %></td>
      <td><%= link_to 'Show', resume %></td>
      <td><%= link_to 'Edit', edit_resume_path(resume) %></td>
      <td><%= link_to 'Destroy', resume, method: :delete, data: { confirm: 'Are you sure?' } %></td>
    </tr>
  <% end %>
</tbody>
```

So now you have to deal with a special `<% %>` syntax, using `<% end %>` on loops, and getting used to all the ruby syntax such as:

```
<% @resumes.each do |resume| %>
```

and:

```
<%= link_to 'Destroy', resume, method: :delete, data: { confirm: 'Are you sure?' } %>
```

## Rake Overview

Great, another command-line tool we need to use

[By this point, I'm already thoroughly convinced that I do not like Rails]