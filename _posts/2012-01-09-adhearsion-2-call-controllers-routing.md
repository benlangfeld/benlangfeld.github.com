---
title: Adhearsion 2 - Call Controllers & Routing

excerpt: Adhearsion 2 presents a new way to write applications - Call Controllers & the call router. Here we'll explain why these changes have been made and how to make use of the new features.

location: Preston, England

layout: default-post
---

In Adhearsion applications based on Adhearsion 1.x and earlier, the most popular way to write the meat of the application was directly in dialplan.rb, and applications would look something like this:

{% highlight ruby %}
adhearsion {
  case variables[:dnid]
  when /789/
    +calls_to_the_top_secret_number
  else
    +everyone_else
  end
}

calls_to_the_top_secret_number {
  menu 'choose-your-top-secret-operation' do
    link.turn_off_the_lights 1
    link.open_the_pod_bay_doors 2
  end
}

turn_off_the_lights {
  ...
}

open_the_pod_bay_doors {
  ...
}

everyone_else {
  pin = input 4, :speak => "This is not the application you are looking for. Enter the correct PIN or face instant descruction."
  if pin == '1234'
    speak 'Phew!'
  else
    speak 'Picachu, I choose you!'
  end
}
{% endhighlight %}

This is, obviously, a trivialised example, but it demonstrates two points clearly. Firstly, the routing is a mess. Implicit in this example is the idea that all calls come in to the `adhearsion` context in Asterisk, and this invoke the matching block in `dialplan.rb`. We then make a routing decision based on a call variable, and jump to a different dialplan context. Once we're there, the routing logic isn't done. We present appropriately verified (snigger) callers with a menu, which takes a selection and does a further context jump. Also, we're relying on Asterisk telling us where the call should start. This is not portable to other platforms which do not understand the idea of a 'context'.

We can do much better than this. In Adhearsion 2, all calls come in to a single place, the Router. The VoIP platform, be it Asterisk, Voxeo PRISM, or anything else, does not instruct Adhearsion on how to route the call. Instead, we have a DSL for defining routes, which can have some interesting rules, in order to decide what should happen to each individual call. This lives in the app's config file (which is now `adhearsion.rb`, by the way). An equivalent to the above app, minus the body of each context, might look something like this:

{% highlight ruby %}
Adhearsion.router do
  route 'Authorized callers', :to => /789/ do
    menu...
  end

  route 'Everyone else' do
    ...
  end
end
{% endhighlight %}

I've left out the way the menu deals with not having dialplan contexts for now, and we will cover the new Menu DSL in a separate post. Rest assured, it's better.

In this example, I've matched the `to` attribute of the incoming Call object against a regex. I've specified this using hash syntax. This entire matching routine uses a system extracted by Jeff Smick's excellent Blather XMPP client library, called [has-guaded-handlers](https://github.com/adhearsion/has-guarded-handlers). This borrows from the idea of [Guards](http://en.wikibooks.org/wiki/Erlang_Programming/guards) from [Erlang](http://www.erlang.org/). The route will be allowed to match a call only if all of its guards are satisfied. One may specify many different kinds of guards, and here are some examples (the same ideas work for event handlers in Adhearsion and stanza handlers in Blather, by the way):

{% highlight ruby %}
route 'foo', Adhearsion::OutboundCall # This requires the call being routed to be of the type specified.
{% endhighlight %}

{% highlight ruby %}
route 'foo', :active? # A contrived example, but a symbol calls the matching method and requires a truthy response
{% endhighlight %}

{% highlight ruby %}
route 'foo', :from => 'sip:me@there.com' # This calls the method #from and requires an exact match to the string specified (this can be any other type).
{% endhighlight %}

{% highlight ruby %}
route 'foo', :from => ['sip:me@there.com', 'sip:you@other.com'] # An array as the hash key requires the return value of #from to match one of the provided values.
{% endhighlight %}

{% highlight ruby %}
route 'foo', :from => 'sip:me@there.com', :to => 'sip:us@here.com' # Multiple hash keys act like logical AND and thus all must match.
{% endhighlight %}

{% highlight ruby %}
route 'foo', [{:from => 'sip:me@there.com'}, {:to => 'sip:us@here.com'}] # Elements of an array act like logical OR and thus if at least one matches, the guards will pass.
{% endhighlight %}

{% highlight ruby %}
route 'foo', lambda { |call| Time.now.hour < 20 } # One may provide a lambda/Proc which can perform any arbitrary operation upon the call object. A truthy return value passes the guard.
{% endhighlight %}

So, that's routing out of the way, but what other problems were there with the old approach? Unfortunately, `dialplan.rb` contexts and their contents are not particularly testable. It's also not very easy to compose complex operations, and spaghetti code can quickly result. The solution to this in many of our Adhearsion 1.x applications was a structure like this:

`dialplan.rb`:
{% highlight ruby %}
adhearsion { do_super_secret_project_call }
{% endhighlight %}

`component/super_secret_project`:
{% highlight ruby %}
methods_for :dialplan do
  def do_super_secret_project_call
    SuperSecretProjectCall.new(self).run
  end
end

class SuperSecretProjectCall
  def initialize(dialplan)
    @dialplan = dialplan
  end

  def run
    pin = collect_pin
    ...
  end

  def collect_pin
    @dialplan.input 4
  end
end
{% endhighlight %}

This is better, but there's a lot of boilerplate and this is not a well documented way of writing applications. The solution? We've created the standardised concept of Call Controllers in Adhearsion 2...

## Call controllers
No longer are Adhearsion applications limited to being simple scripts. With Call Controllers, Adhearsion applications become real MVC applications. A controller is, well, the controller, the call object is the 'view' (it's the method of interaction between the human and the application, so it qualifies here, even though it's not visible), and one may use whatever models one likes, be they backed by a database, a directory (like LDAP) or anything else. Indeed, one might wish to make use of a second view, such as an XMPP interaction or some kind of push-based rendering to a visual display.

So, how does one write an application based on call controllers? It's simple; one must simply create a class inherited from `Adhearsion::CallController`, ensure it responds to `#run`, place it somewhere in your application and route calls to it. That might look something like this:

`config/adhearsion.rb`:
{% highlight ruby %}
Adhearsion.routes do
  route 'default', SuperSecretProjectCall
end
{% endhighlight %}

`lib/super_secret_project_call.rb`:
{% highlight ruby %}
class SuperSecretProjectCall < Adhearsion::CallController
  def run
    pin = collect_pin
    ...
  end

  def collect_pin
    input 4
  end
end
{% endhighlight %}

As you can see, a route definition can take a class rather than a block, and it will use that call controller for the call (in fact, when you pass a block to #route, you're actually creating a call controller under the covers). The controller class itself, lives in the `lib` directory, which by default is auto-loaded by Adhearsion. You may configure this like so:

`config/adhearsion.rb`:
{% highlight ruby %}
Adhearsion.config do |config|
  config.lib = 'application/call_controllers'
end
{% endhighlight %}

Within a call controller, you have access to all of the usual dialplan DSL methods as instance methods, and you also have access to the call object (`#call`). In addition, it is possible to define some callbacks to be executed at the appropriate time. These are `before_call` and `after_call`, they are class methods, and they take either a block or a symbol (called as an instance method) like so:

`lib/super_secret_project_call.rb`:
{% highlight ruby %}
class SuperSecretProjectCall < Adhearsion::CallController
  before_call do
    @user = User.find_by_phone_number call.from
  end

  after_call :save_user

  def run
    pin = collect_pin
    ...
  end

  def collect_pin
    input 4
  end

  def save_user
    @user.save
  end
end
{% endhighlight %}

Testing call controllers is easy, but we've covered quite enough for today, so we'll visit that some other time. Additionally, there will be other features added to call controllers, probably in Adhearsion 2.1, like ticked timers (which will allow, for example, checking an account balance every minute of the call). Further, Adhearsion 2 will include generators for call controllers as well as some test scaffolding. Watch out for further details coming soon.
