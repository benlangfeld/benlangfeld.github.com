---
title: Stateful fun on Heroku

excerpt: Noting the passing of one of the big names in Bayesian statistics with a discussion some of his work I am personally familiar with.

location: Liverpool, England

layout: default-post

---

So, Heroku recently announced their new [Celadon Cedar](http://devcenter.heroku.com/articles/cedar) stack which is based around [Foreman](http://devcenter.heroku.com/articles/procfile), a new process management gem released by Heroku. You can read more about what those mean in general over on the Heroku documentation, but I'm just going to briefly outline here how easy it is to use the new stack to deploy a long-lived non-web daemon process.

We use XMPP a lot, and increasingly so, and our favourite library for XMPP based apps is Jeff Smick's excellent [Blather](https://github.com/sprsquish/blather). Now, we have the opportunity to deploy Blather based apps to Heroku. OMGWTFYAY!

The process is simple. You create an empty git repository, create a Heroku app on the Cedar stack (`heroku create --stack cedar`), add some files to the repository and push. Firstly, you'll need a Gemfile:
{% highlight ruby %}
source 'http://rubygems.org'

gem 'blather'
gem 'foreman'
{% endhighlight %}

Then just run bundle install as usual to get those dependencies. You'll also need a simple Blather based application:
{% highlight ruby %}
require 'rubygems'
require 'blather/client'

setup ENV['JID'], ENV['JPASSWORD']

# Auto approve subscription requests
subscription :request? do |s|
  write_to_stream s.approve!
end

# Echo back what was said
message :chat?, :body do |m|
  say m.from, "You said: #{m.body}"
end
{% endhighlight %}

Here we're simply defining two callbacks, one to automatically accept roster subscriptions, and another to echo back any messages received. We're first, though, setting up our bot with a JID and a password, which we've done using environment variables here, so lets go ahead and add those:

{% highlight bash %}
{10:55}[ruby-1.9.2]~/Desktop/blather-heroku@master✗✗✗ ben% heroku config:add JID=someone@jabber.org JPASSWORD=somepassword
Adding config vars:
  JID       => someone@jabber.org
  JPASSWORD => somepassword
Restarting app... done, v3.
{% endhighlight %}

Simple. Now comes the important part: the Procfile definition needed to start up our app. This really couldn't be any simpler:
    bot: bundle exec ruby app.rb -D

We are able to define arbitrary process classes (here 'bot') and run arbitrary commands. We've added a -D option here to instruct blather to log at the debug level.

So, we're all set. We now have 4 files (app.rb, Gemfile, Gemfile.lock and Procfile) which we simply commit to our repository and then do `git push heroku master`. Since we've defined a new process class, we'll need to instruct Heroku to scale appropriately. This is as simple as `heroku ps:scale bot=1`, and then our bot will start up. Send it a message, and magic happens!

If anyone cares to recreate this, you will actually notice the bot being disconnected periodically, because jabber.org uses the [XMPP Ping](http://xmpp.org/extensions/xep-0199.html) specification as a keep-alive, and we're not responding, but that's really besides the point here. The point is that Heroku, long the leader in Ruby-based web application hosting (at least for ease of deployment) now has a stack that is flexible enough to do many more interesting things. They even now have a real node.js offering. What more could you want?

Keep your eyes peeled for more exciting developments in this direction. I'm looking at you, [Adhearsion](http://adhearsion.com)...
