---
layout: post
title: "Formatting your logs for the sake of visibility"
date: 2013-11-08 22:34
categories: rails
image: https://dl.dropboxusercontent.com/u/3504653/logs.jpg
---

Lately, I have been busy trying to better monitor the app I am working on. That involves a lot of stuff from logging to instrumentation, and other way of collecting all the metrics you need. For the moment, I'll be focusing on the logging part.
I think that having well formatted logs will really helps to extract new metrics and to have a proactive alerting system when stuff like 'Error R14' or other codes from Heroku. You can send alert notifications when that line happens more than x times per minute.

Before extracting metrics from the logs, you have to know what to log. Knowing what to log can be a whole post by itself. This is mostly business centric, query times and benchmarking some code. If you have other ideas don't hesitate to share about that, I'm also preparing a blog post about that subject.

To facilitate the extraction of metrics, it is important to format the logs in an easy and comprehensive way. This will be the subject of this post.
It is easy to find lot of way of formatting them, none is really wrong. Searching on google or Stack Overflow will give you a lot of answers and best practices. As we are on [Heroku](https://devcenter.heroku.com/articles/logging), I already found a simple and easy format in their logs. The router part is formatted as follow.

    2013-11-07T16:40:30.266469+00:00 heroku[router]: \
    at=info method=GET path=/some_path host=some_host.com \
    fwd="some_ip" dyno=web.2 connect=1ms service=27ms status=200 bytes=5854

There are two parts separated by a colon. The date and some information about the program and process in the first part. This is commonly call the context. And it is followed by a serie of concatenations of ```key=value``` pairs on one line.
There is a line per query for the rails router part. That way it is really easy to extract the information and to apply some simple pattern matching to search them.
For example, a grep of ```status=2``` will return all the lines for the successful http queries. It will be simple to know the number of 5XX errors per minute during a certain period.

[@sr](http://twitter.com/sr) gave me the link to heroku's library: [https://github.com/asenchi/scrolls](https://github.com/asenchi/scrolls).
The usage of this gem is as direct as ```Scrolls.log(some_hash)```. You can define a context that will be added to each of your log entries with ```Scrolls.global_context(some_hash)```. And they have a few normalised keys you can find in the ReadMe.
With that gem it is really easy to add new lines in the logs formatted as the ones from Heroku. It will be my solution for custom logs in Rails and Sinatra.

But for Rails common logs, I want to have all the logs reformatted to the key/value format and to avoid some noise that Rails logs might give.. For that, [@roidrage](http://twitter.com/roidrage) published a great gem: [lograge](https://github.com/roidrage/lograge). It does exactly what I want, avoiding logging noise and mimicking Heroku's format. This will then be my complement to ```Scrolls```.

For other usages, there are formatters for a few other log styles like JSON or Logstash's format. It is also feasible to add yours. I didn't tried it but it seems to be as simple as having a class responding to ```#call(data)```. Which is quite close ot the ```Logger::Formatter``` which needs to implement ```#call(severity, time, progname, msg)```. Except that the lograge data is a ```Hash```. Which brings more genericity.

With these two gems, I'll be able to push all my logs to a log aggregator service like papertrail or logentries and easily browse them later. I'll also have the possibility to parse them in real time and send metrics to something like Librato or Graphite. Both services also provide the alerts feature with custom services to alert to, including email, campfire or other services.

The introduction and last paragraph made me realize that this blog post will be part of a tryptic.
One will be about the tools to browse, parse and search the logs and how to use them with Heroku and multiple apps. I will compare probably [Papertrail](http://papertrailapp.com) and [logentries](https://logentries.com).
The other one will be more about what to log and how to bring these information to your logger without interfering with your code, thanks to ```ActiveSupport::Notifications```.

As a last note, I want to link a few resources that helped me in having the right ideas for all that.
• [On Notifications, Log Subscribers, and Bringing Sanity to Rails' Logging, by Mathias Meyer](http://www.paperplanes.de/2012/3/14/on-notifications-logsubscribers-and-bringing-sanity-to-rails-logging.html)
• [It's not how good your app is, it's how good you want it to be, by Josh Kalderimis at Baruco 2012](http://www.youtube.com/watch?v=d8t1F8YDF8w) [[slides]](https://speakerdeck.com/joshk/its-not-how-good-your-app-is-its-how-good-you-want-it-to-be-rubyconf-brazil)
• [Digging Deep with ActiveSupport::Notifications by Matt Sanders at RailsConf 2012](http://www.youtube.com/watch?v=sKrrsF0MZ7Q) [[slides]](https://speakerdeck.com/nextmat/digging-deep-with-activesupportnotifications)


