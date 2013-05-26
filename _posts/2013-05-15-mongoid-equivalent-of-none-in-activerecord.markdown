---
layout: post
title: "Mongoid equivalent of none in ActiveRecord"
date: 2013-05-15 18:23
categories: rails
---

Have you ever had a scope that takes parameters that could be ```nil```?

In some of these cases, you just want to return an empty array.
But in the case of a scope, you have to be able to continue chaining scopes.

In ActiveRecord, there is the ```none``` scope just for that.

But there is not that possibility yet in Mongoid, or at least I didn't find it.

For that I just monkey patched Mongoid::Document. For the moment and plan to make a pull request.

{% highlight ruby %}
module Mongoid::Document
  module ClassMethods
    def none
      lt(_id: 0)
    end
  end
end
{% endhighlight %}
What do you think of this technique? Any better solution?
