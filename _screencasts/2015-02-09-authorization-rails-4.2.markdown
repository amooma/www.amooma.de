---
layout: screencast
title: "Authorization with Ruby on Rails 4.2"
lang: en
date: 2015-02-09 06:00:00
tags:
  - Rails
  - Ruby
  - Authorization
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "A simple authorization system for a vanilla Ruby on Rails 4.2 application. A Step-by-Step Guide."
excerpt: "Screencast: A simple authorization for a vanilla Ruby on Rails 4.2 application. A Step-by-Step Guide."
youtube_video_id: NmAGd9ak3f4
video_length: 10:27 min
custom_css:
  - blog
  - syntax
  - screencast
---

In the [last screencast](/screencasts/2015-02-02-authentication-rails-4.2/) I created an authentication system for a vanilla Ruby on Rails 4.2 application. In this screencast we will add basic authorization to it.

## A basics blog

We are going to create a blog application. Any logged in user will be able to create new posts. Posts can be edited and destroyed by the user himself but not by anybody else.

## Create the Rails application

Please visit the [Authentication Screencast](/screencasts/2015-02-02-authentication-rails-4.2/) and create a new `blog` application the same way. You should be able to create a new account and log into that account. A `current_user` method and a `current_user` helper should be available.

## Post Scaffold

We start with a `Post` scaffold.

{% highlight bash %}
rails g scaffold Post user:references title content:text
rake db:migrate
{% endhighlight %}

Because we render the flash messages in `application.html.erb` we can delete them in the following files:

- app/views/posts/show.html.erb
- app/views/posts/index.html.erb

The `Post` model needs a couple of validations and a `to_s` method:

**app/models/post.rb**

{% highlight ruby %}
class Post < ActiveRecord::Base
  belongs_to :user

  validates :title,
            presence: true

  validates :content,
            presence: true

  validates :user,
            presence: true

  def to_s
    title
  end
end
{% endhighlight %}

And the `User` model needs a `has_many` association to the `Post` model:

**app/models/user.rb**

{% highlight ruby %}
has_many :posts, dependent: :destroy
{% endhighlight %}

Because we'll take care of the `user_id` attribute in the controller we can delete the following code in the Post form:

**app/views/posts/_form.html.erb**

{% highlight erb %}
<div class="field">
  <%= f.label :user_id %><br>
  <%= f.text_field :user_id %>
</div>
{% endhighlight %}

And because we will not send the `user_id` through the form we can remove that from the `post_params` method in the `Post` controller too:

**app/controllers/posts_controller.rb**

{% highlight ruby %}
# Only allow a trusted parameter "white list" through.
def post_params
  params.require(:post).permit(:title, :content)
end
{% endhighlight %}

Lastly we have to change the `create` method in the same controller to build a new post with the `current_user.posts.build`:

**app/controllers/posts_controller.rb**

{% highlight ruby %}
# POST /posts
def create
  @post = current_user.posts.build.new(post_params)

  if @post.save
    redirect_to @post, notice: 'Post was successfully created.'
  else
    render :new
  end
end
{% endhighlight %}

For easier navigation I'll add two links to our navigation.

**app/views/layouts/application.html.erb**

{% highlight erb %}
<%= link_to 'Home', root_path %> |
<%= link_to 'Posts', posts_path %> |
<% if current_user %>
  Logged in as <%= current_user.email %>.
  <%= link_to "Log Out", logout_path %>
<% else %>
  <%= link_to "Sign Up", signup_path %> or
  <%= link_to "Log In", login_path %>
<% end %>
{% endhighlight %}

Now a user can create, edit and destroy a post. But unfortunately any user can edit any post now and even somebody who is not logged in at all could edit and destroy a post.

We need authorization to fix that.

## before_action :authorize

We create a private `authorize` method in the `Post` controller and trigger it with a `before_action`. Because we don't need it for the `index` and `show` views we can `except` those.

**app/controllers/posts_controller.rb**

{% highlight ruby %}
class PostsController < ApplicationController
  [...]
  before_action :authorize, except: [:show, :index]

  [...]

  def authorize
    if current_user.nil?
      redirect_to login_url, alert: "Not authorized! Please log in."
    else
      if @post && @post.user != current_user
        redirect_to root_path, alert: "Not authorized! Only #{@post.user} has access to this post."
      end
    end
  end
end
{% endhighlight %}

## Links in the view

Obvioulsy it doesn't make any sense to show a link to a person who is not logged in. A couple of `if` clauses and the `current_user` helper will fix that:

**app/views/posts/index.html.erb**

{% highlight erb %}
<h1>Listing Posts</h1>
<table>
  <thead>
    <tr>
      <th>User</th>
      <th>Title</th>
      <th>Content</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @posts.each do |post| %>
      <tr>
        <td><%= post.user %></td>
        <td><%= post.title %></td>
        <td><%= post.content %></td>
        <td><%= link_to 'Show', post %></td>
        <td><% if current_user && post.user == current_user %><%= link_to 'Edit', edit_post_path(post) %><% end %></td>
        <td><% if current_user && post.user == current_user %><%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %><% end %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<% if current_user %>                                                    <%= link_to 'New Post', new_post_path %>
<% end %>
{% endhighlight %}

After that we'll do the same in the `show` view.

Now you are good to go. Have fun with your new authorization system!

## Alternatives

Please have a look at [ruby-toolbox.com](https://www.ruby-toolbox.com/categories/rails_authorization) for a detailed list of available authorization gems. **Do not use `cancan`** because that is not longer maintained! `cancancan` seems to be a good alternative (but I've never tried it). Many people like `Pundit`.
