---
layout: post
title: "Your logs need some format"
date: 2013-11-07 10:34
categories: rails
image: https://dl.dropboxusercontent.com/u/3504653/logs.jpg
---

Lately, I have been busy trying to better monitor the app I'm working on. That involves a lot of stuff from logging to instrumentation, and other way of collecting all the metrics you need.   
And when you have all that you still have to find a good way to browse that. For the moment, I will just talk about better logging.

The first thing to do is knowing what to log. This is mostly business centric, query times and measuring some parts of your code. If you have other ideas don't hesitate to share about that, I'm preparing a blog post about that also.

The second one, is how to format the logs to search/browse them easily later. As we are on Heroku, I already had a simple example in some of their logs. The router part is formatted as follow.

```
2013-11-07T16:40:30.266469+00:00 heroku[router]: \
at=info method=GET path=/p/8glg6aOh host=frontback.me \ 
fwd="some_ip" dyno=web.2 connect=1ms service=27ms status=200 bytes=5854
```

A concatenation of ```key=value``` on one line per information shared (mostly one per query at the beginning).

[@sr](http://twitter.com/sr) gave me the link to their library: [https://github.com/asenchi/scrolls](https://github.com/asenchi/scrolls).   
It is as direct as ```Scrolls.log(some_hash)```. You can define a context that will be added to each of your log entry with ```Scrolls.global_context(some_hash)```. And they have a few normalised names you can find in the ReadMe.  
It might be really useful for simple log in a Sinatra app. I will probably use it in the small sinatra apps we have beside the big Rails one.

But for Rails, I want to have all the normal logs they already give reformatted. For that, [@roidrage](http://twitter.com/roidrage) published a great gem: [lograge](https://github.com/roidrage/lograge).
It avoids logging noise and mimicks Heroku's format.

There are formatters for a few log style like JSON or Logstash's format. It is also feasable to add yours. I didn't tried it but it seems to be as simple as having a class responding to ```#call(data)```.

Thanks to these two gems, I'll be able to push all my logs to a provider and easily browse them later. And I'll also be able to parse them in real time and send metrics to something like Librato or Graphite.

There will be at least two other parts to this post.  
One will be about the tools to browse the logs and how to set them. (Probably Papertrail and logentries)  
The other one will be more about the what to log and how to bring these information to your logger thanks to ```ActiveSupport::Notifications```

As a last note, I want to link a few resources that helped me in having the right ideas for all that.  
• [On Notifications, Log Subscribers, and Bringing Sanity to Rails' Logging, by Mathias Meyer](http://www.paperplanes.de/2012/3/14/on-notifications-logsubscribers-and-bringing-sanity-to-rails-logging.html)  
• [It's not how good your app is, it's how good you want it to be, by Josh Kalderimis](http://www.youtube.com/watch?v=d8t1F8YDF8w) + [slides](https://speakerdeck.com/joshk/its-not-how-good-your-app-is-its-how-good-you-want-it-to-be-rubyconf-brazil)

 