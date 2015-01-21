---
layout: screencast
title: "Nested Forms with Ruby on Rails 4.2"
lang: en
date: 2015-01-21 18:00:00
tags: Rails Ruby
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: "How to nest forms with Ruby on Rails 4.2."
youtube_video_id: jngp1kdKTwA
---

In this screencast I show how to create nested forms. A nested form is a form within an other form. The aim is to generate a nicer user experience because the user doesn't have to jump back and forth between parent and child forms. To show the effect I create a database of hotels which have different room categories. A hotel should be editable in one form which includes all the room categories.

## New project

We start a new Rails project with the name *travel-agency* and `cd` into that.

{% highlight bash %}
rails new travel-agency
cd travel-agency
{% endhighlight %}

## A scaffold for Hotel and RoomCategory

We need the following two scaffolds for this example project.

{% highlight bash %}
rails g scaffold Hotel name
rails g scaffold RoomCategory name hotel:references
rake db:migrate
{% endhighlight %}

## Associations

Because a hotel and a room category needs a name we add validation for that in the models. While we are there we add a `to_s` method which prints that name and add the needed associations.

**app/models/hotel.rb**

{% highlight ruby %}
class Hotel < ActiveRecord::Base
  has_many :room_categories, dependent: :destroy

  validates :name,
            presence: true

  def to_s
    name
  end
end
{% endhighlight %}

**app/models/room_category.rb**

{% highlight ruby %}
class RoomCategory < ActiveRecord::Base
  belongs_to :hotel

  validates :name,
            presence: true

  def to_s
    name
  end
end
{% endhighlight %}

## Seed data

Let's include two example hotels with a couple of room categories in the seeds.

**db/seeds.rb**

{% highlight ruby %}
ritz_carlton = Hotel.create(name: 'The Ritz-Carlton New York, Central Park')
mandarin_oriental = Hotel.create(name: 'Mandarin Oriental, New York')

['City View Guestroom', 'Deluxe Parkview Room'].each do |rc|
  ritz_carlton.room_categories.create(name: rc)
end

['City View Room', 'Skyline View Room', 'Hudson River View Room'].each do |rc|
  mandarin_oriental.room_categories.create(name: rc)
end
{% endhighlight %}

## Start the Rails server

Reset the database which runs the seeds automatically and start Rails.

{% highlight bash %}
rake db:reset
rails s
{% endhighlight %}

## Hotel Show View

We want to list all room categories for a hotel in the show view of that hotel.

**app/views/hotels/show.html.erb**

{% highlight erb %}
<p id="notice"><%= notice %></p>

<p>
  <strong>Name:</strong>
  <%= @hotel.name %>
</p>

<h2>Room Categories</h2>

<% if @hotel.room_categories.any? %>
  <ul>
  <% @hotel.room_categories.each do |room_category| %>
    <li><%= link_to room_category, room_category %></li>
  <% end %>
  </ul>
<% else %>
  <p>none available</p>
<% end %>

<%= link_to 'Edit', edit_hotel_path(@hotel) %> |
<%= link_to 'Back', hotels_path %>
{% endhighlight %}

## Hotel Form

Now we use the [fields_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-fields_for) Helper within the Hotel form to add fields for the `room_categories` of that hotel (code line 7). Have a look at [ActionView::Helpers::FormHelper Documentation](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-fields_for) for more examples.

**app/views/hotels/_form.html.erb**

{% highlight erb linenos %}
<div class="field">
  <%= f.label :name %><br>
  <%= f.text_field :name %>
</div>

<h2>Room Categories</h2>
<%= f.fields_for :room_categories do |room_category| %>
  <div class="room_category_fields">
    <div class="field">
      <%= room_category.label :name %><br>
      <%= room_category.text_field :name %>
    </div>
  </div>
<% end %>
{% endhighlight %}

## accepts_nested_attributes_for

But that form doesn't show any nested attributes. To achieve that we have to add `accepts_nested_attributes_for` in the hotel model.

**app/models/hotel.rb**

{% highlight ruby %}
class Hotel < ActiveRecord::Base
  has_many :room_categories, dependent: :destroy

  accepts_nested_attributes_for :room_categories

  validates :name,
            presence: true

  def to_s
    name
  end
end
{% endhighlight %}

## Change the name of hotel categories

Any change within the nested attributes doesn't work.

Let's have a look at the console output while we update a hotel category name within the hotel form:
{% highlight bash %}
Unpermitted parameters: room_categories_attributes
{% endhighlight %}

The `room_categories_attributes` are not in the controller strong parameters white list. This is a security feature of Rails 4 (have a look at the [Strong Parameters plugin docu](https://github.com/rails/strong_parameters) for more information about strong parameters).

**app/controllers/hotels_controller.rb**

{% highlight ruby %}
  private
    # Use callbacks to share common setup or constraints between actions.
    def set_hotel
      @hotel = Hotel.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def hotel_params
      params.require(:hotel).permit(:name, room_categories_attributes: [ :id, :name ])
    end
end
{% endhighlight %}

## Add New Room Categories in the Hotel Edit View

It probably would be useful to add an empty room category in the edit view just to be able to add one when ever you edit a hotel. For that we have to add a `@hotel.room_categories.build` into the edit method of the hotel controller.

**app/controllers/hotels_controller.rb**

{% highlight ruby %}
# GET /hotels/1/edit
def edit
  @hotel.room_categories.build
end
{% endhighlight %}

But that triggers a side effect of our validation. Because of the `name` validation in the `hotel_category` model we can not save an empty `name` - it is just not valid. We can not change the validation but we can add a `reject_if` to the `accepts_nested_attributes_for` to reject an empty `name` attribute.

**app/models/hotel.rb**

{% highlight ruby %}
class Hotel < ActiveRecord::Base
  has_many :room_categories, dependent: :destroy

  accepts_nested_attributes_for :room_categories,
                                reject_if: proc { |attributes| attributes['name'].blank? }

  validates :name,
            presence: true

  def to_s
    name
  end
end
{% endhighlight %}

Now let's add the same functionality to the hotel new form by adding `@hotel.room_categories.build` to the new method.

**app/controllers/hotels_controller.rb**

{% highlight ruby %}
# GET /hotels/new
def new
  @hotel = Hotel.new
  @hotel.room_categories.build
end
{% endhighlight %}

BTW: We could DRY this code by creating an `after_filter` for the `edit` and `new` method to run a `@hotel.room_categories.build`.

## Destroy a Room Category in the Hotel Form

Now we are able to edit and create nested room categories in the hotel form. But we have no way to destroy a room category in that form. We need to add `allow_destroy: true` to the `accepts_nested_attributes_for` for that functionality.

**app/models/hotel.rb**

{% highlight ruby %}
class Hotel < ActiveRecord::Base
  has_many :room_categories, dependent: :destroy

  accepts_nested_attributes_for :room_categories,
                                reject_if: proc { |attributes| attributes['name'].blank? },
                                allow_destroy: true

  validates :name,
            presence: true

  def to_s
    name
  end
end
{% endhighlight %}

For that to work we have to add a `room_category.check_box :_destroy` field in the hotel form.

**app/views/hotels/_form.html.erb**

{% highlight erb %}
<div class="field">
  <%= f.label :name %><br>
  <%= f.text_field :name %>
</div>

<h2>Room Categories</h2>
<%= f.fields_for :room_categories do |room_category| %>
  <div class="room_category_fields">
    <div class="field">
      <%= room_category.label :name %><br>
      <%= room_category.text_field :name %>
      <%= room_category.check_box :_destroy %>
      <%= room_category.label :_destroy, 'remove' %>
    </div>
  </div>
<% end %>
{% endhighlight %}

And of course we have to add this attribute in the hotel controller strong parameter whitelist.

**app/controllers/hotels_controller.rb**

{% highlight ruby %}
def hotel_params
  params.require(:hotel).permit(:name, room_categories_attributes: [ :id, :name, :_destroy ])
end
{% endhighlight %}

Now we can add, edit and remove room categories to in the hotel from.
