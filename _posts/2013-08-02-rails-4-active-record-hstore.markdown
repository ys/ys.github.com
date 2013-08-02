---
layout: post
title: "Rails 4 Active Record: Hstore"
date: 2013-08-02 23:12
categories: rails
image: http://cl.ly/image/163P0j1k3L3f/content
---
One of the nice thing that rails 4 added to Postgres adapter is support for hstore, arrays and Json types.
I love how easy it is to store a simple flat key-value of ```String```.
If you want a quick intro by Richard Schneeman from Heroku.

[http://schneems.com/post/19298469372/you-got-nosql-in-my-postgres-using-hstore-in-rails](http://schneems.com/post/19298469372/you-got-nosql-in-my-postgres-using-hstore-in-rails)

Lately, I've been using it to store API errors that occurs inside of background jobs. I set an error code on the user to give feedbacks to users like 'you must reauth', 'your password expired' or other information.

The first problem is that each change pass by the callbacks and validations. I just want to do the equivalent of a ```update_column``` but for hstores. That is why I added two methods to my ```User``` model. One to directly add a key to the hstore and one to remove it.

Here is how I implemented it.

{% highlight ruby %}

class User < ActiveRecord::Base
  store_accessor :stored_errors, :instagram_error, :facebook_error, :twitter_error

  def add_error_for_provider(provider, error)
    add_to_hstore = %(stored_errors = stored_errors || hstore(?, ?))
    User.where(id: self.id).update_all([add_to_hstore, "#{provider}_error", error])
  end

  def delete_error_for_provider(provider)
    delete_from_hstore = %(stored_errors = delete("stored_errors", ?))
    User.where(id: self.id).update_all([delete_from_hstore, "#{provider}_error"])
  end
end

{% endhighlight %}

It can be rapidly generalized to use with any structure. With something like this. (Untested)

{% highlight ruby %}

class MyModel < ActiveRecord::Base

  def add_to_hstore(key, value)
    add_to_hstore_string = %(hstore_column = hstore_column || hstore(?, ?))
    self.class.where(id: self.id).update_all([add_to_hstore_string, key, value])
  end

  def delete_from_hstore(key)
    delete_from_hstore_string = %(hstore_column = delete("hstore_column", ?))
    self.class.where(id: self.id).update_all([delete_from_hstore_string, key])
  end
end

{% endhighlight %}

Hope you enjoyed this trick. I plan to find a way to add it to Rails but first I need to validates a few things. Or it is already and I did not found it and need to document it.
