---
title: In-call apps with Adhearsion (part 4 of 4) - Attended transfers

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

This article is part 4 of a series.  If you have not already, check out [Part 1: In-Call Apps with Adhearsion & Matrioska](/blog/2013/call-apps-adhearsion-matrioska/), [Part 2: In-Call Apps: Please Hold](/blog/2013/call-apps-please-hold/) and [Part 3: In-Call Apps: Getting Interactive](/blog/2013/call-apps-getting-interactive/).

In the previous post of this series, we finished off on-hold functionality. Now we have that working, we can implement something a little more adventurous: Attended Transfers.

## Attended Transfers

Attended transfers follow this sort of flow:

*   We start with two legs talking to each other, called Alice and Bob
*   Alice puts Bob on hold
*   Charlie is called and connected to Alice
*   Alice & Charlie are connected directly for some time until Alice triggers Bob to join into a three-party call
*   Alice may hangup once all three parties are connected, and Bob & Charlie can continue their conversation

~~~ruby
require 'matrioska/dial_with_apps'

class InCallAppController < Adhearsion::CallController
  include Matrioska::DialWithApps

  def run
    menu 'Press 1 to return to the call, 2 to hang up on the other person, or 3 to transfer.' do
      match(1) { main_dial.rejoin }
      match(2) { main_dial.cleanup_calls }
      match(3) { transfer }

      timeout { say 'Sorry, you took too long' }
      invalid { say 'Sorry, that was an invalid choice' }
      failure { say 'Sorry, you failed to select a valid option' }
    end
  end

private

  def transfer
    transfer_to = ask 'Please enter the number to transfer to. Once connected, press 1 to rejoin.', limit: 10
    speak "Transferring to #{transfer_to.response}"
    dial_with_local_apps "tel:#{transfer_to.response}" do |runner, dial|
      runner.map_app '1' do
        dial.skip_cleanup
        main_dial.skip_cleanup
        main_dial.merge dial
      end
    end
  end

  def main_dial
    metadata['current_dial']
  end
end
~~~

What did we do here? Well, we implemented an extra option in the menu to initiate an attended transfer, we asked for a number to dial, and we performed a second dial operation, again with its own in-call apps. This dial operation allows Alice & Charlie to talk for as long as they like while Bob remains on hold. During this second direct call, Alice may press 1 to finish the transfer by merging the second dial operation into the first one (using [`Dial#merge`](http://rubydoc.info/gems/adhearsion/Adhearsion/CallController/Dial/Dial:merge)), thus adding Bob to a three-party call with Alice & Charlie.

If this were all the app did, however, our users would experience a rather unpleasant bug: When Alice would hang up, Bob & Charlie would also be hung up. Why? Well, a dial operation in Adhearsion, be it `Adhearsion::CallController#dial` or the Matrioska variety, links the lifecycle of its calls such that if the main party hangs up, the others are hung up (rather than hearing infinite dead air), or if all outbound parties are hung up, the main call continues with the rest of its original controller and is eventually hung up.

When the transfer (second) dial notices that Alice (its main call) hangs up, it will disconnect Charlie. The original dial also notices that Alice (also its main call) was hung up, and will disconnect Bob. Suddenly no-one is talking to anyone, and that's not much of a transfer!

How do we fix this? Well, very simply, we instruct the transfer dial operation to [skip cleaning up its calls](http://rubydoc.info/gems/adhearsion/Adhearsion/CallController/Dial/Dial:skip_cleanup) such that Charlie remains connected, and do the same for the main dial operation so that Bob remains connected to Charlie. Now everyone is happy, and people don't get disconnected when they should not.

## What next?

What we've shown here is a collection of cases all centred around the party who enters the original dial being the one who should have the applications (ability to place on hold, transfer) available. If the inverse was true, you would have to use Matrioska's `#dial_with_remote_apps` for the main dial instead, and slightly different logic would apply to the hangup handling.

We also noted that the three-party and transfer scenarios won't currently work on Asterisk; this is something that would require implementation in Punchblock, and right now this work is not on the schedule of the Adhearsion core team.

I'm sure everyone has ideas for other in-call apps you might build with Matrioska, even if you aren't interested in these dial use cases. The [Adhearsion community](http://adhearsion.com/community) would love to hear how you're using these tools: some examples we already know of are in use at [RingPlus](http://ringplus.net/home/apps) who have translation and transcription services available, and funded the development of [Matrioska](https://github.com/adhearsion/matrioska), and at [Grasshopper](http://grasshopper.com) who funded the additional dial pieces present in Adhearsion 2.4 to make hold, transfer and three-party calls work.

We hope you enjoy building your ideas on Adhearsion, but if you're struggling or need extra features, do [let us know](https://mojolingo.com/connect/) and we can help you make it happen.
