---
layout: screencast
title: "Authentication with Ruby on Rails 4.2"
lang: en
date: 2015-02-02 06:00:00
tags:
  - Rails
  - Ruby
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "A simple password authentication for a vanilla Ruby on Rails 4.2 application. A Step-by-Step Guide."
excerpt: "Screencast: A simple password authentication for a vanilla Ruby on Rails 4.2 application. A Step-by-Step Guide."
youtube_video_id: zQJtlvlNunw
video_length: 14:33 min
custom_css:
  - blog
  - syntax
  - screencast
---

This screencast shows how to add a simple password authentication for your Ruby on Rails 4.2 application without using the big shots like Devise or OmniAuth (see [ruby-toolbox.com](https://www.ruby-toolbox.com/categories/rails_authentication) for a list of popular authentication gems).

## Create the Rails application

We start with a fresh Ruby on Rails application.

{% highlight bash %}
rails new shop
cd shop
{% endhighlight %}

For the `has_secure_password` method we need the `bcrypt` gem. And because we don't need a JSON API in this application deactivate the `jbuilder` gem.

**Gemfile**

{% highlight ruby %}
[...]

# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
# gem 'jbuilder', '~> 2.0'

[...]

# Use ActiveModel has_secure_password
gem 'bcrypt', '~> 3.1.7'

[...]
{% endhighlight %}

After saving the `Gemfile` we need to run `bundle`.

{% highlight bash %}
bundle
{% endhighlight %}

## Generate a Root Page

As a start we create a `Page` controller with an `index` view:

{% highlight bash %}
rails g controller Page index
{% endhighlight %}

We use that view as the `root_path`:

**config/routes.rb**

{% highlight ruby %}
Rails.application.routes.draw do
  get 'page/index'
  root 'page#index'
end
{% endhighlight %}

We start the rails development server:

{% highlight bash %}
rails s
{% endhighlight %}

Fire up the browser and open http://0.0.0.0:3000

## Generate a User scaffold

We use a `User` model to store the user information and the password digest. We do not store the password in clear text in the database.

{% highlight bash %}
rails g scaffold User first_name last_name email password:digest
rake db:migrate
{% endhighlight %}

In the `User` model we add a couple of validations and a `to_s` method.

**app/models/user.rb**

{% highlight ruby %}
class User < ActiveRecord::Base
  has_secure_password

  validates :first_name,
            presence: true
  validates :last_name,
            presence: true
  validates :email,
            presence: true,
            uniqueness: true,
            format: {
              with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
            }

  def to_s
    "#{first_name} #{last_name}"
  end
end
{% endhighlight %}

## Create a new user

We create the User "Jon Smith" with
http://127.0.0.1:3000/users/new


## Create a Session controller

Having a `User` model is nice but we need a `Session` mechanism to create a `login` and `logout` procedure. Because we live in a RESTful world `login` would be `new` plus `create` and `logout` would be `destroy` in a `Sessions` controller.

{% highlight bash %}
rails g controller sessions new create destroy
{% endhighlight %}

Now we create a couple of routes for this to work:

**config/routes.rb**

{% highlight ruby %}
Rails.application.routes.draw do
  resources :sessions, only: [:new, :create, :destroy]

  get 'signup', to: 'users#new', as: 'signup'
  get 'login', to: 'sessions#new', as: 'login'
  get 'logout', to: 'sessions#destroy', as: 'logout'

  resources :users

  get 'page/index'
  root 'page#index'
end
{% endhighlight %}

## Login Form

The login form is the `new.html.erb` view for a new `Session`.

**app/views/sessions/new.html.erb**

{% highlight ruby %}
<h1>Log In</h1>

<%= form_tag sessions_path do %>
  <div class="field">
    <%= label_tag :email %><br>
    <%= text_field_tag :email %>
  </div>
  <div class="field">
    <%= label_tag :password %><br>
    <%= password_field_tag :password %>
  </div>
  <div class="actions">
    <%= submit_tag "Log In" %>
  </div>
<% end %>
{% endhighlight %}

## Session Controller

The `Session` controller needs 3 actions:

- new
- create
- destroy

With that we use the RESTful approach.

**app/controllers/sessions_controller.rb**

{% highlight ruby %}
class SessionsController < ApplicationController
  def new
  end

  def create
    user = User.find_by_email(params[:email])
    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect_to root_url, notice: 'Logged in!'
    else
      render :new
    end
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_url, notice: 'Logged out!'
  end
end
{% endhighlight %}

## Create a current_user method

We need a mechanism to access the `current_user` everywhere in the application. For that we add a `current_user` method to the `ApplicationController` and use `helper_method` to provide a helper method for the views too.

**app/controllers/application_controller.rb**

{% highlight ruby %}
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  protect_from_forgery with: :exception

  private

  def current_user
    User.where(id: session[:user_id]).first
  end
  helper_method :current_user
end
{% endhighlight %}

To show a user if he/she is already logged in and if not where to login or where to sign up we add a little header HTML in `application.html.erb`

Additionally I add some code to show flash messages if there are any.

**app/views/layouts/application.html.erb**

{% highlight html %}
<div id="user_header">
  <% if current_user %>
    Logged in as <%= current_user.email %>.
    <%= link_to "Log Out", logout_path %>
  <% else %>
    <%= link_to "Sign Up", signup_path %> or
    <%= link_to "Log In", login_path %>
  <% end %>
</div>

<% flash.each do |key, value| %>
  <%= content_tag(:div, class: "alert alert-#{key}") do %>
    <p><%= value %></p>
  <% end %>
<% end %>
{% endhighlight %}

Because we render the flash messages in `application.html.erb` we can delete them in the following files:

- **app/views/users/show.html.erb**
- **app/views/users/index.html.erb**

{% highlight html %}
<p id="notice"><%= notice %></p>
{% endhighlight %}

## Auto login when a new user signs up

When ever a new user signs up it makes sense for him to be logged in right away. That's easy done by setting the session `session[:user_id] = @user.id` in the `users_controller.rb`.

**app/controllers/users_controller.rb**

{% highlight ruby %}
  # POST /users
  def create
    @user = User.new(user_params)

    if @user.save
      session[:user_id] = @user.id
      redirect_to root_url, notice: 'User was successfully created.'
    else
      render :new
    end
  end
{% endhighlight %}

## Next step

In the next screencast I'll show how to use this authentication to setup an authorization system which grands or denies access to specific users.
