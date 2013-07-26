---
layout: post
title: "Active Record Collections"
date: 2013-07-27 20:18
categories: rails
image: http://cl.ly/image/163P0j1k3L3f/content
---
Lately, I tried to avoid having more and more code relative to collections
inside of my models. That helps to avoid a lot of magical mystic code with
bidirectional relationships.

That's why a new directory appeared in my ```/app```. It's a ```/app/collections``` directory.
{% highlight ruby %}
class User < ActiveRecord::Base
  has_many :posts

  def public_posts
    posts.public
  end
end

class Post < ActiveRecord::Base
  belongs_to :user

  scope :public, -> { where(active: true) }
end
{% endhighlight %}

Instead of this kind of code, I have something like
{% highlight ruby %}
class User < ActiveRecord::Base
end

class UserPosts
  attr_reader :user

  def initialize(user)
    @user = user
  end

  def public
    Post.where(user_id: user.id).public
  end
end
{% endhighlight %}

And the ```Post``` class stay the same.

What do you think of this organization of code?
