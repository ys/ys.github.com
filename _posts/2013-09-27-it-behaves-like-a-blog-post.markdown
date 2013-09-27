---
layout: post
title: "It behaves like a blog post"
date: 2013-09-27 23:30:00 +0200
categories: ruby
---

As I start to use more and more Rspec, I discovered a pretty neat usage:
Shared examples. They give me a way to generalize some specs.
It acts like concerns for my specs.

I was the kind of guy that copy/paste some generic parts of specs about access to API.
Access to admin interfaces and so on. Recopying parts testing filters.

An example might be that all my admin controllers must be unreachable to regular users.
Instead of copy pasting my tests all over. I can write a shared_examples block in a support file.
This one take a parameter for all actions in the controller we want to test.

{% highlight ruby %}
shared_examples "an admin controller" do |actions|
  context "as a regular user" do
    actions.each_pair do |action, info|
      specify "I should be denied access to ##{action}" do
        controller.set_current_user(double(admin?: false, id: 1, authenticated?: true))
        send(info[:verb], action, info[:params].merge({format: :json}))
        response.status.should be 401
      end
    end
  end
end
{% endhighlight %}

Before, I would just have copied/pasted the specify part as an it part in each action.
Lame, I know, but we all make mistakes and find solutions.

How to use this shared example in a spec is as easy as adding the next block
to all my actions and specify them in a hash, with the method and params needed.
And using that block in each controller spec instead of the whole shared example is far more readable.

{% highlight ruby %}
require 'spec_helper'

describe WebAdmin::UsersController do
  it_behaves_like 'a web admin controller', {
    index:   { verb: :get},
    show:    { verb: :get, params: { id:'bob' }},
    destroy: { verb: :delete, params: { id: 'bob' }},
  }
end
{% endhighlight %}

This is one of the awesome things Rspec can do and that I started to use everywhere.
I hope that some of you will find this tip helpful.

