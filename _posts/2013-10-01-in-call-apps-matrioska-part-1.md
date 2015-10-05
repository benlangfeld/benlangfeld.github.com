---
title: In-call apps with Adhearsion & Matrioska (part 1)

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

<p>On the <a href="http://adhearsion.com/community">Adhearsion mailing list</a> there are frequent requests for information on how to use Adhearsion to put calls on hold, do call transfers, and manage 3-way calls. Thanks to the generous support of Adhearsion community members and Mojo Lingo's clients, now in Adhearsion 2.4 we have everything you need to implement these scenarios.</p>

<p>Lets start our journey with a simple call controller to receive an inbound call and dial out to a SIP endpoint...</p>

<pre class="brush: ruby"># file: lib/inbound_controller.rb
class InboundController &lt; Adhearsion::CallController
  def run
    dial 'sip:foobar@example.com'
  end
end
</pre>

<p>and hook it up in the router so all inbound calls are sent to it...</p>

<pre class="brush: ruby"># file: config/adhearsion.rb
Adhearsion.router do
  route 'dial', InboundController
end
</pre>

<p>With that boilerplate in place, let's get to the interesting bits.</p>

<h2>In-Call Apps</h2>

<p>Lovely, now we have Adhearsion sitting in the middle of our calls and we can start to think about what magic we'd like to add to them. We like to call this kind of magic, 'in-call apps', and we have an Adhearsion plugin for it developed by our very own <a href="https://mojolingo.com/blog/author/lpradovera/">Luca Pradovera</a>, called <a href="http://github.com/adhearsion/matrioska">Matrioska</a>. Matrioska provides functionality for triggering arbitrary code/controllers from DTMF input during a call. To use Matrioska, add it to your application's Gemfile, do <code>bundle install</code>, and begin adding some magic to <em>your</em> calls:</p>

<pre class="brush: ruby"># file: Gemfile
gem 'matrioska'
</pre>

<p>What might a very basic in-call app look like? Let's imagine: while you're on the phone to your other half, he or she tends not to pay you enough compliments, so you want to be able to get some from your phone provider at the push of a button. You can add this ego-stroking service with a small amount of code...</p>

<pre class="brush: ruby"># file: lib/inbound_controller.rb
require 'matrioska/dial_with_apps'

class InboundController &lt; Adhearsion::CallController
  include Matrioska::DialWithApps

  def run
    dial_with_local_apps 'sip:foobar@example.com' do |runner, dial|
      runner.map_app '1' do
        say 'Gosh you sound stunning today.'
      end
    end
  end
end
</pre>

<p>Now you can press 1 during a call as many times as you like, and you'll get all the compliments you can handle.</p>

<h2>More about Matrioska</h2>

<p>Matrioska, and the potential for in-calls apps, is a great place to start thinking about how we can change what it means to use the phone. For more about Matrioska, check out its <a href="https://github.com/adhearsion/matrioska">README</a>. In future posts, we'll look at other uses for Matrioska and for in-call apps generally.</p>
