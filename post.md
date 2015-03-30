Sprockets is a gem that is used for asset packaging and you may know of it from its use in Rails' own [Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html). Bower is a JavaScript library that is used to manage packages that contain assets such as JavaScript or CSS files. Sprockets and Bower work terrifically well together and this guide will go through how we can setup a new app that uses Sprockets and Bower together.

## Setting up Bower

The Rails Asset pipeline is configured to load assets from three places within an application: `app/assets`, `lib/assets` and `vendor/assets`. For Bower to work with the asset pipeline, it needs to install its packages into one of these directories.

Let's first create a new Rails application:

```
rails new marked_man
```

Let's initialize Bower within our application and configure it to install not to its default path of `bower_components`, but instead to `vendor/assets/bower`. First, initialize Bower by running this command:

```
bower init
```

Leave all the options as default, save for the one beginning with "would you like to mark this package as private", as we definitely *do* want this to be private. When you're done, you will have a `bower.json` that looks like this:

```json
{
  "name": "marked_man",
  "version": "0.0.0",
  "authors": [
    "Ryan Bigg <git@ryanbigg.com>"
  ],
  "license": "MIT",
  "private": true,
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ]
}
```

Next, we'll need to configure the directory where Bower installs to. To do this, we need to create the `.bowerrc` file and put this content in it:

```json
{
  "directory": "vendor/assets/bower",
  "json": "bower.json"
}
```

This file holds configuration options for Bower. It tells Bower that the directory we want to install our packages into is `vendor/assets/bower`, and not the default `bower_components`. It also tells Bower that it can find more configuration at `bower.json`.

## Installing and using Marked

Marked is a full-featured markdown parser and compiler written in JavaScript. We'll be using it in our application to parse some Markdown and display it on the page.

Before we can do that, we'll need to install Marked with Bower:

```
bower install marked
```

With Marked installed with Bower, we can now reference it in our application. Let's generate a new controller called `posts`:

```
rails g controller posts
```

Along with this controller we'll generate a new template at `app/views/posts/show.html.erb` which just has some text that we'll process through Marked:

```markup
<div id='content'>

</div>

<%= javascript_include_tag "marked" %>
<script>
  $(document).ready(function () {
    $('#content').html(marked('#Marked in browser\n\n Rendered by **marked**'))
  })
</script>
```

This code includes Marked using `javascript_include_tag` and then uses marked to parse some very basic Markdown. If we had some Markdown in a database, we could use this library to display this Markdown in a nicely formatted way using Marked.

We'll need to route to this page too, so let's add a `root` route to `config/routes.rb` for that:

```ruby
Rails.application.routes.draw do
  root to: "posts#show"
end
```

When we start this app up with `rails server` and go to `localhost:3000`, we'll see our nicely formatted Markdown:

![Marked formatting](/images/marked-formatting.png)

Now you know how to use Sprockets and Bower together. But why stop there? I mean, you could if you want to. I'm not going to force you to read any further. But why not investigate a little further to find out *how* this all works? Wouldn't that be fun?

## Sprockets + Bower: how does they even?

The Asset Pipeline is seen as a black box by many. A tweet by a prominent Rubyist and fantastic guide writer reads:

> Sprockets still as undocumented as ever. Literally two people in human history know how it works.

While this is not exactly true, the Bower integration for Sprockets is fairly recent and as such, not many people do know how it works. Let's find out *together*. It'll be fun!

Our goal in this section is to find out how `javascript_include_tag` knows how to include the `marked.js` asset from the marked package that was installed by Bower. We know that it works, but we don't yet know how it works.

For this section, you'll need easy access to three GitHub repos: `rails/rails`, `sstephenson/sprockets` and `rails/sprocket-rails`. Clone all of these to the same directory now.










