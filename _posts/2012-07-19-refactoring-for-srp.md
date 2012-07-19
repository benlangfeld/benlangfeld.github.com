---
title: Refactoring for SRP - an example

excerpt: There is much material available online that covers the theory of the Single Responsibility Principle (SRP), but few real world examples. Here's one taken from a medium size open-source codebase just this week.

location: Tübingen, Deutchland

layout: default-post
---

There is much material available online that covers the theory of the Single Responsibility Principle (SRP), and many have examples. Unfortunately, these examples are contrived and generally have something to do with a shopping cart or money transfer between accounts, and there are very few real-world examples. Here is one taken from the Punchblock and RubyAMI ruby libraries.

RubyAMI is based on an extraction of code from Adhearsion 1, and provides connection management, parsing, and a bunch of helpers around the Asterisk AMI socket interface. It is designed to allow simple interaction with Asterisk, via AMI, in a rubyish way.

Punchblock is an abstraction layer atop several VoIP platforms, providing a consistent third party call control interface. It currently works with Voxeo PRISM (the Rayo protocol), Asterisk (via RubyAMI) and is getting support for FreeSWITCH shortly.

# The smell

While working on the Asterisk related parts of Punchblock this week, I discovered some rather ugly code (that, admittedly, came from my keyboard). Here's an excerpt, slightly modified for clarity, with irrelevant parts removed (the actual modifications to Punchblock can be seen [here](https://github.com/adhearsion/punchblock/commit/85c992b5251bf04a31def1455fc0ddeb03fb716b)):

{% highlight ruby %}
class Call
  class << self
    def parse_environment(agi_env)
      agi_env_as_array(agi_env).inject({}) do |accumulator, element|
        accumulator[element[0].to_sym] = element[1] || ''
        accumulator
      end
    end

    def agi_env_as_array(agi_env)
      CGI.unescape(agi_env).split("\n").map { |p| p.split ': ' }
    end
  end

  def initialize
    # Expensive operation to setup new call state and processing
  end
end

class Dispatcher
  def process_new_call_event(event)
    environment = Call.parse_environment event['Env']
    return if env[:agi_extension] == 'h'
    Call.new event['Channel'], environment
  end
end
{% endhighlight %}

So, what was happening here, and why? Well, to begin with, note that creating a new Call object is an expensive operation (details on that later). Consequently, we want to avoid having to create one unless it's actually necessary. Asterisk, however, may fire events at us for calls we are not interested in (eg calls to the 'h' extension). We want to avoid processing these kind of calls.

The extension a call is directed to is part of the AGI environment, which is just a set of key-value pairs of data, which we receive in a URL encoded string that looks something like this:

{% highlight ruby %}
"agi_request%3A%20async%0Aagi_channel%3A%20SIP%2F1234-00000000%0Aagi_language%3A%20en%0Aagi_type%3A%20SIP%0Aagi_uniqueid%3A%201320835995.0%0Aagi_version%3A%201.8.4.1%0Aagi_callerid%3A%205678%0Aagi_calleridname%3A%20Jane%20Smith%0Aagi_callingpres%3A%200%0Aagi_callingani2%3A%200%0Aagi_callington%3A%200%0Aagi_callingtns%3A%200%0Aagi_dnid%3A%201000%0Aagi_rdnis%3A%20unknown%0Aagi_context%3A%20default%0Aagi_extension%3A%201000%0Aagi_priority%3A%201%0Aagi_enhanced%3A%200.0%0Aagi_accountcode%3A%20%0Aagi_threadid%3A%204366221312%0A%0A"
{% endhighlight %}

In order to extract the extension from this string, we could do a simple regex match, but we want the full hash version for later processing anyway, so we just go ahead and parse it all to begin with (yes, there is possibly room for optimisation here).

# Why is this bad, and how do we fix it?

Our parsing code was contained in a class method on Call. There are several reasons this is bad:

* Parsing of a string of data into a usable representation (a hash) is not the responsibility of a Call object, or the abstract template for a call. It is a responsibility in and of itself.
* Such parsing should be unit tested in isolation. This is possible as a class method, but clutters otherwise unrelated code.
* Parsing of this string is requested by an entity which is not a Call, so it could not be a private instance method, and considered an implementation detail of Call (which, correspondingly, would not need isolated unit testing).
* The parsing of such AGI environment strings is a distinctly Asterisk related responsibility, and should therefore be moved to the RubyAMI library, where such low-level functionality belongs.

For all of these reasons, I decided this code was ripe for a move elsewhere, and so after about 45 minutes I had it [in its new home](https://github.com/adhearsion/ruby_ami/commit/e538c274d4e0478a2feedb9ab97e95366f2b612a#L3L-1). The code now is significantly cleaner, and provides a much more appropriate, friendly and usable interface than it did before, and is more reusable. I promptly removed the old version from Punchblock and depended on the new interface in RubyAMI.

# Are we making this mistake elsewhere too?

I didn't stop here though. I had parsing of ugly strings on my brain and remembered one other place where we did something similar: Parsing of AGI results. These strings are even uglier, and so is the appropriate regex. You can see from the commit on RubyAMI the kinds of variations in strings we get, and how we need to split them up. Previously, this code was an instance method on an object with responsibility for responding to these events. Because of all of those variations, we had [tests on that object](https://github.com/adhearsion/punchblock/commit/85c992b5251bf04a31def1455fc0ddeb03fb716b) which just did the parsing of that string. That was a complete waste of cycles during the test run, spinning up a new (again heavy) object just to get at one of its instance methods. Additionally, although such parsing is an internal concern of that object, we had to do one of two things to test the parsing in isolation:

* Make the instance method public
* Use #send to call a private method

This stinks of the fact that the original home of this code was an object which violated the SRP. Again, this code ended up in a [much better place](https://github.com/adhearsion/ruby_ami/commit/e538c274d4e0478a2feedb9ab97e95366f2b612a#L2L-1) and lives happily ever after.

## Why are new Call instances expensive?

The Call class in Punchblock is responsible for tracking call state and concurrent operations on calls. As such, it is implemented as a Celluloid actor. At this time, Celluloid actors require a thread each, and are not garbage collected in the normal way.
