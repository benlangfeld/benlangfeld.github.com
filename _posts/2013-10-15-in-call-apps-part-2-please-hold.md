---
title: In-call apps with Adhearsion (part 2 of 4) - Please hold

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

<p>Following on our <a href="/blog/2013/call-apps-adhearsion-matrioska/">previous article in the series</a>, let's talk about some of the building blocks for in-call applications.  In this installment: placing callers on hold, and retrieving them.</p>

<h2>Please hold</h2>

<p>Well, first, we'll start by defining the in-call app:</p>

<pre><code class="ruby">require 'matrioska/dial_with_apps'

class InboundController &lt; Adhearsion::CallController
  include Matrioska::DialWithApps

  def run
    dial_with_local_apps 'sip:5201996@localphone.com' do |runner, dial|
      runner.map_app '1' do
        place_on_hold dial
      end
    end
  end

  private

  def place_on_hold(dial)
    logger.info "Splitting calls"
    blocker = Celluloid::Condition.new
    dial.split main: InCallAppController, others: HoldMusicController, main_callback: -&gt;(call) { blocker.broadcast }
    blocker.wait # This prevents the Matrioska listener from starting again until the calls are rejoined
  end
end
</code></pre>

<p>Woah! We've added a lot of code in this example, what is it all doing? Well, we've replaced our compliments with a call to <code>#place_on_hold</code>, which is defined, amongst other things, to split the dial operation in half. We're sending the main call, which is the A leg who originally invoked <code>#dial</code>, to the <code>InCallAppController</code>, and all other calls to <code>HoldMusicController</code> (we'll create these controllers in a moment). We also create a <a href="https://github.com/celluloid/celluloid/wiki/Conditions"><code>Celluloid::Condition</code></a>, which blocks the in-call app from finishing until the InCallAppController is finished on our 'main' call.</p>

<p>So what are <code>HoldMusicController</code> and <code>InCallAppController</code>? Well, HoldMusicController is fairly self-explanatory, and here's how it's defined:</p>

<pre><code class="ruby">class HoldMusicController &lt; Adhearsion::CallController
  def run
    output = play! 'You are on hold.', repeat_times: 1000
    call.on_joined { output.stop! }
  end
end
</code></pre>

<p>This starts an asynchronous playback operation to repeat a thousand times (it should be possible to set this to 0 to make it infinite, but that appears to be broken), and then schedules it to be terminated when the call is joined to another call.</p>

<p>The <code>InCallAppController</code> is almost as simple:</p>

<pre><code class="ruby">class InCallAppController &lt; Adhearsion::CallController
  def run
    say "Hey, the other guy is on hold right now. I'll reconnect you in 5 seconds."
    sleep 5
    main_dial.rejoin
  end

  private

  def main_dial
    metadata['current_dial']
  end
end
</code></pre>

<p>Here, after playing back a simple message, we just wait for 5 seconds and then rejoin the dial operation back together. How did we get hold of that <a href="http://rubydoc.info/gems/adhearsion/Adhearsion/CallController/Dial/Dial"><code>Dial</code></a> operation? Well, it was set in the controller's metadata thanks to <a href="http://rubydoc.info/gems/adhearsion/Adhearsion/CallController/Dial/Dial:split"><code>Dial#split</code></a>, and we defined a private method to easily access this.</p>

<p>Now, after the B leg has been on hold for 5 seconds, the A-leg will rejoin it, the <code>InCallAppController</code> will finish, and the in-call app will be restarted ready to put the caller on hold again a second time. Snazzy, huh?</p>
