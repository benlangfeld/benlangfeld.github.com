---
title: Adhearsion 2.4 is here

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

This is a big week for the Adhearsion community. We're announcing a total of three new releases: [Adhearsion](http://adhearsion.com) 2.4.0, [Punchblock](https://github.com/adhearsion/punchblock) 2.0.0 (our abstraction layer) and [Matrioska](https://github.com/polysics/matrioska) 0.1.0 (our in-call apps wrapper). Those are very round numbers, but what do they mean, and why is this exciting?

## tl;dr

So, what will I get from Adhearsion 2.4?

* [Adhearsion <3 FreeSWITCH](#ahn_freeswitch) (and Adhearsion <3 Rayo too)
* [Adhearsion <3 JRuby](#ahn_jruby)
* [Adhearsion <3 Speed!](#ahn_speed)

Also, Adhearsion 2.4 fully supports the current draft [Rayo specification](http://xmpp.org/extensions/xep-0327.html) to give you better platform portability.  Read on for the full details.

## A little background

When we released Adhearsion 2.0 we promised three major things:

* Cross VoIP-platform support
* More and better standard ways for building things, including consistent method calls across all VoIP platforms
* Support for alternative Ruby interpreters

We addressed those points by including the following things in the initial Adhearsion 2.0 release:

* Support for both Asterisk and Voxeo PRISM (with rayo-server)
* Experimental JRuby support
* A feature rich new version of Adhearsion with a bunch of extra abstractions for asynchronous event handling and allowing for growth into text-to-speech and speech recognition areas.

Ahn 2.0 was a significant refactoring project that allowed for a baseline platform for continued growth of features and improvements. So here we are. Now, what's new?

<a name="ahn_freeswitch"></a>

## Adhearsion <3 FreeSWITCH

<a href="http://freeswitch.org"><img src="/wp-content/uploads/2012/08/FreeSWITCH_logo-300x73.png" alt="FreeSWITCH logo" width="300" height="73" class="alignright size-medium wp-image-552" /></a>With early versions of Adhearsion 2 we therefore delivered a substantial baseline to the promise of the 2.x series, but the work didn't end there. In Adhearsion 2.1, we shipped the first official support Adhearsion has had for FreeSWITCH, one of the longest-standing feature requests for the framework from way back in the days of 0.8.

At this point we had support for three VoIP platforms and the original promise began to really take shape. This also came with a downside, however; we now had essentially three codebases to maintain, and a lot of effort has been expended trying to maintain consistency across the platforms.

But even before 2.0 was released, we announced a long-term solution to this problem: the [Rayo protocol](http://rayo.org). Rayo is designed to be a platform-independent third-party call control protocol taking into account the lessons learned from previous protocol designs and application platforms built on top of them. We intended for Adhearsion to eventually support only one protocol in the core framework, and thankfully earlier this year we found a partner willing to assist us in beginning to realise this vision.

[Grasshopper](http://grasshopper.com) and specifically [Chris Rienzo](https://twitter.com/crienzo) built [mod_rayo](https://wiki.freeswitch.org/wiki/Mod_rayo) for FreeSWITCH, allowing us to treat FreeSWITCH and Voxeo PRISM identically. The development of this second full Rayo server implementation coincided with the [publishing of the Rayo specification](/blog/2013/bringing-rayo-to-reality/) by the XSF, and both it and the new releases of Adhearsion and Punchblock comply with v0.2 of the published specification.

We hope that one day Asterisk will follow suit and add support for the Rayo protocol, for the benefit of the whole open source telephony community to be able to choose the appropriate platform for their deployment needs without having to rewrite applications or frameworks like Adhearsion in order to move between them. For now, we will be maintaining our Asterisk support, and our original FreeSWITCH support remains in Punchblock 2.0, but the core team's future effort will be toward bettering mod_rayo, and our original FreeSWITCH support may be removed in a future version of Adhearsion/Punchblock. We suggest that everyone using FreeSWITCH today with Adhearsion transition to mod_rayo for this reason and all of the other benefits afforded by Rayo including clustering and multi-tenancy support.

<a name="ahn_jruby"></a>

## Adhearsion <3 JRuby

<a href="http://jruby.org"><img src="/wp-content/uploads/2013/08/jruby-logo-300x85.png" alt="JRuby Logo" width="300" height="85" class="alignright size-medium wp-image-1671" /></a>We've always almost supported JRuby; we were so close to production stability it was tantalising, but several of our dependencies and some of our own code had issues that *just* halted the party. Now we're proud to say that, with these latest releases, we have applications in production under load running on JRuby and ticking along nicely. In fact, a large amount of refactoring had to go on to achieve this, particularly simplification of our use of Nokogiri and XML, but that work is now done and the codebase is better for it.

Adhearsion and all of its major dependencies now have passing test suites on JRuby and we are committing to keep it this way; some dependencies are also passing on Rubinius, though this support remains unofficial for now. We hope that at some point soon Rubinius support will become official too, but there is still work to be done to achieve this.

<a name="ahn_speed"></a>

## Adhearsion <3 Speed

<a href="http://public.ifbyphone.com"><img src="/wp-content/uploads/2013/08/ifbyphone-logo.png" alt="Ifbyphone Logo" width="225" height="41" class="alignright size-full wp-image-1672" /></a>We weren't happy with performance equivalent to Adhearsion 1, so earlier this year we got to work to improve this situation. We also had a client who was interested in pushing Adhearsion to these new heights, and had the kind of traffic that could really benefit from the scale: [Ifbyphone](http://public.ifbyphone.com/). Thanks to Ifbyphone's commitment to the Adhearsion project, we were able to spend the time necessary to push performance of Adhearsion 2 to solid multiples of what had been established previously.

The first big win was polishing up our [JRuby](http://jruby.com) support. Moving to the JVM gave us its raw speed owing to things like the JIT compiler and much more efficient [garbage collection](http://en.wikipedia.org/wiki/Java_performance#Garbage_collection).  We also gained true parallelism for our threads, allowing Adhearsion to utilise all cores of a system's CPU for the first time.

But our performance improvement efforts didn't stop at using the JVM; we made several large changes, particularly to Asterisk support, to improve performance, including rewriting our Asterisk AMI parser to gain a 22x improvement in event parsing rate. We also removed an intermediate XML representation for call control messages when communicating with Asterisk (over AMI) or FreeSWITCH (over EventSocket) to reduce the number of allocations and serialisation operations, which has also shown drastic reduction in CPU utilisation and message latency per call.

Overall, this is the best Adhearsion release yet, and we hope you enjoy using it as much as we sweated to build it. You can upgrade by doing a simple `bundle update` in your Adhearsion project, or do `gem install adhearsion && ahn create myproject` to get started on a new project today. For more details on the changes that have been made, check out the [Adhearsion](https://github.com/adhearsion/adhearsion/blob/develop/CHANGELOG.md) and [Punchblock](https://github.com/adhearsion/punchblock/blob/develop/CHANGELOG.md) changelogs, and [API documentation](http://adhearsion.com/api). Also look out for tutorial posts coming up over the next few weeks highlighting some of the new features.
