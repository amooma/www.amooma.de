---
layout: post
title: "Paperclip with Fingerprints"
lang: en
date: 2014-11-07 10:00:00
tags: Rails
author: Stefan Wintermeyer
email: stefan.wintermeyer@amooma.de
homepage: http://www.wintermeyer.de
abstract: How to configure a paperclip's has_attached_file to use filenames with a proper MD5 sum fingerprint.
---
You are using the [Paperclip Gem](https://github.com/thoughtbot/paperclip) with a Ruby on Rails model and want to setup a proper HTTP-Cache with fingerprinted files (see ["What is Fingerprinting and Why Should I Care?"](http://guides.rubyonrails.org/asset_pipeline.html#what-is-fingerprinting-and-why-should-i-care-questionmark) for some background on fingerprinting assets)?

Let me save you a couple of hours searching with this example config:

{% highlight ruby %}
class User < ActiveRecord::Base
  has_attached_file :avatar,
                    :styles => {
                                 :medium => "x300>",
                                 :thumb => "100x100>"
                               },
                    :url  => "/system/:attachment/:id/:style/:basename-:fingerprint.:extension",
                    :path => ":rails_root/public/system/:attachment/:id/:style/:basename-:fingerprint.:extension",
                    :use_timestamp => false
end
{% endhighlight %}

Additionally you'll have to add the attribute `avatar_fingerprint` to your model. You can add a migration with this command

{% highlight bash %}
rails generate migration AddAvatarFingerprintToUser avatar_fingerprint
{% endhighlight %}

That will result in the following migration:

{% highlight ruby %}
class AddAvatarFingerprintToUser < ActiveRecord::Migration
  def change
    add_column :users, :avatar_fingerprint, :string
  end
end
{% endhighlight %}

After that you'll have to

{% highlight bash %}
rake db:migrate
{% endhighlight %}

Obviously that only works for any new attachment. You'll have to migrate existing attachments by hand.
