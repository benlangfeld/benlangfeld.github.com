---
title: In-call apps with Adhearsion (part 3 of 4) - Getting interactive

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

<p>This article is part 3 of a series.  If you have not already, check out <a href="/blog/2013/call-apps-adhearsion-matrioska/">Part 1: In-Call Apps with Adhearsion &amp; Matrioska</a> and <a href="/blog/2013/call-apps-please-hold/">Part 2: In-Call Apps: Please Hold</a></p>

<h2>Getting Interactive</h2>

<p>That's all well and good, but a 5 second hold is kind of restrictive, don't you think? Lets add a menu to the in-call app which gives the user the choice of rejoining or hanging up the remote party and continuing to work through <code>InboundController</code>.</p>

<pre><code class="ruby">require 'matrioska/dial_with_apps'

class InCallAppController &lt; Adhearsion::CallController
  def run
    menu 'Press 1 to return to the call, or 2 to hang up on the other person.' do
      match(1) { main_dial.rejoin }
      match(2) { main_dial.cleanup_calls }

      timeout { say 'Sorry, you took too long' }
      invalid { say 'Sorry, that was an invalid choice' }
      failure { say 'Sorry, you failed to select a valid option' }
    end
  end

  private

  def main_dial
    metadata['current_dial']
  end
end

class InboundController &lt; Adhearsion::CallController
  include Matrioska::DialWithApps

  def run
    dial_with_local_apps 'sip:5201996@localphone.com' do |runner, dial|
      runner.map_app '1' do
        place_on_hold dial
      end
    end

    say "Thanks. It seems we're all done. Goodbye."
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

<p>Here, we make use of <a href="http://rubydoc.info/gems/adhearsion/Adhearsion/CallController/Dial/Dial:cleanup_calls"><code>Dial#cleanup_calls</code></a> which will hangup outbound calls created by the dial operation. When these calls are disconnected, the dial operation in <code>InboundController</code> will return, and further logic may be applied to the A leg of the call (here we simply play back a message).</p>

<p>Well, that was easy. Now we have interactive hold, we can start to add some additional features to the app. More to come next week!</p>
