---
layout: screencast
title: "Fragement Caching with Ruby on Rails 4.2"
lang: en
date: 2015-02-24 21:00:00
tags:
  - Rails
  - Cache
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "A beginner's guide to use fragement caching in a Ruby on Rails 4.2 application."
excerpt: "Screencast: A beginner's guide to use fragement caching in a Ruby on Rails 4.2 application."
youtube_video_id: l-T__mBnOAA
video_length: 19:30 min
custom_css:
  - blog
  - syntax
  - screencast
---

Fragement caching is the first caching mechanism most Rails developer use. Many times 2-3 lines of code will result in a much faster application. But it is tricky to debug and can become a resource grave if not used well.

## Our Sandbox Rails Application

To demonstrate the effects I create an address book application and use the [Faker Gem](https://github.com/stympy/faker) to populate the database with entries.

{% highlight bash %}
rails new address_book
cd address_book
echo "gem 'faker'" >> Gemfile
sed -i '' 's/\(gem .jbuilder\)/\# \1/' Gemfile
bundle
rails g controller Page index
sed -i '' "s/\# root 'welcome\#index'/root 'page\#index'/" config/routes.rb
rails g scaffold company name
rails g scaffold person first_name last_name company:references
rake db:migrate
{% endhighlight %}

With the Faker gem we generate 1,000 persons and 100 companies. Each person belongs_to a company.

This gives a nice data pool to show the effect of caching.

**db/seeds.rb**

{% highlight ruby %}
100.times { Company.create(name: Faker::Company.name )}

1000.times { Person.create(first_name: Faker::Name.first_name,
                           last_name: Faker::Name.last_name,
                           company: Company.all.sample )}
{% endhighlight %}

Then we add the has_many association and a to_s method to the company model.

**app/models/company.rb**

{% highlight ruby %}
class Company < ActiveRecord::Base
  has_many :people

  def to_s
    name
  end
end
{% endhighlight %}

## Activate Caching in the Development Environment

Caching is deactivated by default in the development environment. You have to activate it for this screencast.

**config/environments/development.rb**

{% highlight ruby %}
[...]
config.action_controller.perform_caching = true
[...]
{% endhighlight %}

## What does a fragment cache do?

Sometimes a view takes a lot of time to render the HTML which gets delivered to the browser. An example in the page#index view:

**app/views/page/index.html.erb**

{% highlight erb %}
<h1>Loop Example</h1>
<ul>
  <% 3.times do %>
    <% sleep 1 %>
    <li>test</li>
  <% end %>
</ul>
{% endhighlight %}

Because of the 1 second sleep in each loop run this page is very slow. It takes about 3,080 ms to be rendered by Rails:

{% highlight ruby %}
Started GET "/" for ::1 at 2015-02-24 13:48:00 +0100
Processing by PageController#index as HTML
  Rendered page/index.html.erb within layouts/application (3003.9ms)
Completed 200 OK in 3080ms (Views: 3079.4ms | ActiveRecord: 0.0ms)
{% endhighlight %}

So lets have a look at http://guides.rubyonrails.org/caching_with_rails.html#fragment-caching and use fragment caching to cache the result of the loop:

**app/views/page/index.html.erb**

{% highlight erb %}
<h1>Loop Example</h1>

<% cache do %>
  <ul>
    <% 3.times do %>
      <% sleep 1 %>
      <li>test</li>
    <% end %>
  </ul>
<% end %>
{% endhighlight %}

The first request obviously didn't improve because the cache had to be written. But the second request was delivered in 74ms:

{% highlight ruby %}
Started GET "/" for ::1 at 2015-02-24 13:53:44 +0100
Processing by PageController#index as HTML
  Cache digest for app/views/page/index.html.erb: 923da8c3db3a930ad66428f858e00cce
Read fragment views/localhost:3000/page/index/923da8c3db3a930ad66428f858e00cce (0.2ms)
  Rendered page/index.html.erb within layouts/application (1.5ms)
Completed 200 OK in 74ms (Views: 73.1ms | ActiveRecord: 0.0ms)
{% endhighlight %}

Rails generates a cache key from the MD5 sum of the file (in this case app/views/page/index.html.erb). So the cache gets expired when ever the content of the view file gets changed.

To delete the cache in your development environment you can use this rake task:

{% highlight bash %}
rake tmp:cache:clear
{% endhighlight %}

The use case of this is very limited. Normally fragment caching is used in combination of ActiveRecord.

## The People Show View

When we request the show view of the first Person it takes about 120 ms:

{% highlight ruby %}
Started GET "/people/1" for ::1 at 2015-02-24 14:16:53 +0100
Processing by PeopleController#show as HTML
  Parameters: {"id"=>"1"}
  Person Load (0.1ms)  SELECT  "people".* FROM "people" WHERE "people"."id" = ? LIMIT 1  [["id", 1]]
  Company Load (0.1ms)  SELECT  "companies".* FROM "companies" WHERE "companies"."id" = ? LIMIT 1  [["id", 69]]
  Rendered people/show.html.erb within layouts/application (1.0ms)
Completed 200 OK in 121ms (Views: 119.9ms | ActiveRecord: 0.2ms)
{% endhighlight %}

But guess what! We can add a fragment cache:

**app/views/people/show.html.erb**

{% highlight erb %}
<p id="notice"><%= notice %></p>

<% cache @person do %>
  <p>
    <strong>First name:</strong>
    <%= @person.first_name %>
  </p>

  <p>
    <strong>Last name:</strong>
    <%= @person.last_name %>
  </p>

  <p>
    <strong>Company:</strong>
    <%= @person.company %>
  </p>

  <%= link_to 'Edit', edit_person_path(@person) %> |
  <%= link_to 'Back', people_path %>
<% end %>
{% endhighlight %}

After we have written the fragment cache with the first request any following request gets rendered with content from the cache:

{% highlight ruby %}
Started GET "/people/1" for ::1 at 2015-02-24 14:24:01 +0100
Processing by PeopleController#show as HTML
  Parameters: {"id"=>"1"}
  Person Load (0.1ms)  SELECT  "people".* FROM "people" WHERE "people"."id" = ? LIMIT 1  [["id", 1]]
  Cache digest for app/views/people/show.html.erb: 512ccb993dfd163db3a3799b571bf196
Read fragment views/people/1-20150223194338611556000/512ccb993dfd163db3a3799b571bf196 (0.2ms)
  Rendered people/show.html.erb within layouts/application (1.3ms)
Completed 200 OK in 91ms (Views: 90.0ms | ActiveRecord: 0.1ms)
{% endhighlight %}

The cache key this time is a little bit more complicated. It still adds an MD5 checksum of the view file so it gets busted when ever you change the content of that file (which is what we want). But in addition to that it adds a cache key for the Person object. This is provided by the ActiveRecord method cache_key ([http://apidock.com/rails/ActiveRecord/Base/cache_key](http://apidock.com/rails/ActiveRecord/Base/cache_key)). You can test it in the Rails console:

{% highlight bash %}
$ rails c
Loading development environment (Rails 4.2.0)
>> Person.first.cache_key
  Person Load (0.1ms)  SELECT  "people".* FROM "people"  ORDER BY "people"."id" ASC LIMIT 1
=> "people/1-20150223194338611556000"
>> exit
$
{% endhighlight %}

Because of the cache_key method every update of the object in the database will result in a new cache key and therefor bust the existing cache.

Conclusion: We have an easy to use system to cache views which where rendered with data provided by ActiveRecord objects.

## The People Index View

The people#index view is a bit more complex but Rails fragment caching has us covered. We can use @people and Rails will automagiacally create a cache_key for that.

**app/views/people/index.html.erb**

{% highlight erb %}
<p id="notice"><%= notice %></p>

<% cache @people do %>
  <h1>Listing People</h1>

  <table>
    <thead>
      <tr>
        <th>First name</th>
        <th>Last name</th>
        <th>Company</th>
        <th colspan="3"></th>
      </tr>
    </thead>

    <tbody>
      <% @people.each do |person| %>
        <tr>
          <td><%= person.first_name %></td>
          <td><%= person.last_name %></td>
          <td><%= person.company %></td>
          <td><%= link_to 'Show', person %></td>
          <td><%= link_to 'Edit', edit_person_path(person) %></td>
          <td><%= link_to 'Destroy', person, method: :delete, data: { confirm: 'Are you sure?' } %></td>
        </tr>
      <% end %>
    </tbody>
  </table>

  <br>

  <%= link_to 'New Person', new_person_path %>
<% end %>
{% endhighlight %}

Rails is kind enough not to blame us but we are waisting a lot of time by creating a cache_key for every Person in the @people variable. This has to be done every time the cache is used. Which is unnecessary. All we need to know is when the last Person instance was updated and that can be requested with the [maximum method](http://api.rubyonrails.org/classes/ActiveRecord/Calculations.html#method-i-maximum).

Additionally we have to account the count of all people. Because if one Person got deleted we have to expire the cache but the maximum(:updated_at) didn't change.

We could add all this in the view but it is DRYer to use a helper method. I re-used the example from [guides.rubyonrails.org/caching_with_rails.html](http://guides.rubyonrails.org/caching_with_rails.html#fragment-caching).

**app/helpers/people_helper.rb**

{% highlight ruby %}
module PeopleHelper
  def cache_key_for_people
    count          = Person.count
    max_updated_at = Person.maximum(:updated_at).try(:utc).try(:to_s, :number)
    "people/all-#{count}-#{max_updated_at}"
  end
end
{% endhighlight %}


**app/views/people/index.html.erb**

{% highlight erb %}
<% cache cache_key_for_people do %>
  <h1>Listing People</h1>

  <table>

  [...]

  </table>

  <br>

  <%= link_to 'New Person', new_person_path %>
<% end %>
{% endhighlight %}

As a result the cache_key is much smaller, needs less time to compute and therefor the page is delivered faster:

{% highlight bash %}
Started GET "/people/" for ::1 at 2015-02-24 15:13:46 +0100
Processing by PeopleController#index as HTML
   (0.2ms)  SELECT COUNT(*) FROM "people"
   (0.4ms)  SELECT MAX("people"."updated_at") FROM "people"
  Cache digest for app/views/people/index.html.erb: 71351c5b261729771b8454e4e59344bd
Read fragment views/people/all-1000-20150223194338/71351c5b261729771b8454e4e59344bd (0.4ms)
  Rendered people/index.html.erb within layouts/application (4.0ms)
Completed 200 OK in 116ms (Views: 115.0ms | ActiveRecord: 0.6ms)
{% endhighlight %}

## The Russion Doll Trap

The russion doll is a powerful tool. The idea is to not just cache the whole table but each line of it in an extra cache.

We have to add a fragment cache for each table row:

**app/views/people/index.html.erb**

{% highlight ruby %}
<p id="notice"><%= notice %></p>

<h1>Listing People</h1>

<% cache @people.maximum(:updated_at) do %>
  <table>
    <thead>
      <tr>
        <th>First name</th>
        <th>Last name</th>
        <th>Company</th>
        <th colspan="3"></th>
      </tr>
    </thead>

    <tbody>
      <% @people.each do |person| %>
        <% cache person do %>
          <tr>
            <td><%= person.first_name %></td>
            <td><%= person.last_name %></td>
            <td><%= person.company %></td>
            <td><%= link_to 'Show', person %></td>
            <td><%= link_to 'Edit', edit_person_path(person) %></td>
            <td><%= link_to 'Destroy', person, method: :delete, data: { confirm: 'Are you sure?' } %></td>
          </tr>
        <% end %>
      <% end %>
    </tbody>
  </table>
<% end %>

<br>

<%= link_to 'New Person', new_person_path %>
{% endhighlight %}

The first request will become very slow because Rails has to write 1,000 caches and that takes time. We could life with that. The main problem is that every time we edit a Person the process of reading 999 rows and writing 1 row followed by writing the whole table takes way longer than generating the whole table and write it.

In this case the use of russion doll doesn't make any sense.

**Do not use russion doll without testing and understanding it! It has the potential of either save your life or become a foot gun.**
