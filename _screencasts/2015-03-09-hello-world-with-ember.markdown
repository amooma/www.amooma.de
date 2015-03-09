---
layout: screencast
title: "Hello World with Ember CLI"
lang: en
date: 2015-03-09 20:00:00
tags:
  - Ember
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "How to create a Hello World application with Ember CLI using pods."
excerpt: "Screencast: How to create a Hello World application with Ember CLI using pods."
youtube_video_id: phIK3wtEIwk
video_length: 06:15 min
custom_css:
  - blog
  - syntax
  - screencast
---

I like Hello World! examples of programming languages and frameworks. At [EmberConf 2015](http://emberconf.com) I was lucky to talk to [Stefan Penner](https://twitter.com/stefanpenner) (the Ember CLI inventor) about this and he helped me to create this screencast.

The task is simple: We want to create a new Ember application which displays a "Hello World!" message. I presume that you have a running Ember CLI environment. In case you have not: You find a description of how to install Ember CLI at the [Ember CLI homepage](http://www.ember-cli.com/). If you have not already installed [Node](https://nodejs.org/) Stefan Penner suggests using [Nave](https://github.com/isaacs/nave) to install it.

## Version Check

First let's make sure that we are using a current Ember CLI, Node and npm version:

{% highlight bash %}
$ ember -v
version: 0.2.0
node: 0.12.0
npm: 2.7.0
{% endhighlight %}

If you have an old Node version installed I suggest searching for uninstall HowTos ([LMGTFY](https://www.google.com/search?q=uninstall+node+js&oq=uninstall+node+js)) and uninstall it completely before installing an up to date version with [Nave](https://github.com/isaacs/nave).

## Create a new Ember application

The Ember CLI command's name is `ember`. You can display the help message with all the options with `ember --help`.

Creating a new ember application can be done with `ember new name-of-the-application`. For our Hello World example that would be:

{% highlight bash %}
$ ember new abc-example
version: 0.2.0
installing
  create .bowerrc
  create .editorconfig
  create .ember-cli
  create .jshintrc
  create .travis.yml
  create Brocfile.js
  create README.md
  create app/app.js
  create app/components/.gitkeep
  create app/controllers/.gitkeep
  create app/helpers/.gitkeep
  create app/index.html
  create app/models/.gitkeep
  create app/router.js
  create app/routes/.gitkeep
  create app/styles/app.css
  create app/templates/application.hbs
  create app/templates/components/.gitkeep
  create app/views/.gitkeep
  create bower.json
  create config/environment.js
  create .gitignore
  create package.json
  create public/crossdomain.xml
  create public/robots.txt
  create testem.json
  create tests/.jshintrc
  create tests/helpers/resolver.js
  create tests/helpers/start-app.js
  create tests/index.html
  create tests/test-helper.js
  create tests/unit/.gitkeep
  create vendor/.gitkeep
Installed packages for tooling via npm.
Installed browser packages via Bower.
Successfully initialized git.
$
{% endhighlight %}

After that we have to `cd` into the new directory.

{% highlight bash %}
$ cd abc-example
13:11 $
{% endhighlight %}

## Start the development server

The command `ember server` or `ember s` starts the development webserver.

{% highlight bash %}
$ ember server
version: 0.2.0
Livereload server on port 35729
Serving on http://localhost:4200/

Build successful - 3478ms.

Slowest Trees                  | Total
-------------------------------+----------------
Concat: Vendor                 | 2831ms

{% endhighlight %}

The new ember application is now running and available at the URL [http://localhost:4200/](http://localhost:4200/)

Any change in the code will lead to an automatic rebuild of the application. You can always change the code and follow up these changes in your browser.

## Create the Hello World

The command `ember g route --pod about` will generate a new `about` pod with all the needed files for our Hello World example.

{% highlight bash %}
$ ember g route --pod about
version: 0.2.0
installing
  create app/about/route.js
  create app/about/template.hbs
installing
  create tests/unit/about/route-test.js
13:24 $
{% endhighlight %}

I'm using pods because according to Stefan Penner they will become the default anyway. Pods are a file and directory structure choice which scales better for big applications.

## Add the Hello World HTML

In case you stopped the ember server you can restart it now with `ember s`. The new page is accessable at [http://localhost:4200/about](http://localhost:4200/about) but of course we have to fill it with some content.

**app/about/template.hbs**

{% highlight html %}
{% raw %}
<p>
Hello World!
</p>

{{outlet}}
{% endraw %}
{% endhighlight %}

After saving the file we have a Hello World page!

I'm not diving into the logic of {% raw %}`{{outlet}}`{% endraw %} here. For now think of it as a mechanism to include content from deeper layers of the application. Ruby on Rails developers can think of it as `yield`.

## A basic navigation bar

To round this example up we add a mini navigation bar at the top of each page. It will consists of a link to the index and a link to the about page.

The best place for this navigation bar in our basic application is the file `app/templates/application.hbs`. Please add the following code to that file:

**app/templates/application.hbs**

{% highlight html %}
{% raw %}
<p>
{{#link-to 'index'}}index{{/link-to}} |
{{#link-to 'about'}}about{{/link-to}}
</p>

<h2 id="title">Welcome to Ember.js</h2>

{{outlet}}
{% endraw %}
{% endhighlight %}

Now your Hello World application has a simple navigation bar on top of each page.

## The index route

You might ask yourself: How can the index page work at all? We did not create a route for that. That is because of the conventions of Ember. If an ember application doesn't find an index route it will create one in memory with defaults. But the moment we create our own index route ember will take that. Let's do that for the sake of this example.

{% highlight bash %}
$ ember g route --pod index
version: 0.2.0
installing
  create app/index/route.js
  create app/index/template.hbs
installing
  create tests/unit/index/route-test.js
14:11 $
{% endhighlight %}

By now the application hasn't changed for the user. To add a marker we change the content of `app/index/template.hbs`:

**app/index/template.hbs**

{% highlight html %}
{% raw %}
<p>
This is the index page.
<p>

{{outlet}}
{% endraw %}
{% endhighlight %}

Now you can visit [http://localhost:4200/](http://localhost:4200/) and see the new index page. At the end let's update the `app/templates/application.hbs` template to get rid of the `Welcome to Ember.js` message on every page.

**app/templates/application.hbs**

{% highlight html %}
{% raw %}
<p>
{{#link-to 'index'}}index{{/link-to}} |
{{#link-to 'about'}}about{{/link-to}}
</p>

{{outlet}}
{% endraw %}
{% endhighlight %}

Congratulation! Now you have a mini Ember application.

Please send me feedback to this screencast by e-mail to stefan.wintermeyer@amooma.de or via Twitter to [@wintermeyer](https://twitter.com/wintermeyer)
