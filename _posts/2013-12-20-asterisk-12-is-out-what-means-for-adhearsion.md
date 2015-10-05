---
title: Asterisk 12 is out. What does it mean for Adhearsion?

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

We've just heard that the Asterisk team has [released Asterisk 12 today](http://blogs.digium.com/2013/12/20/asterisk-12-0-0-now-available/). This is excellent news for the Asterisk and Adhearsion communities. Asterisk 12 introduces a number of important architectural improvements to Asterisk, many of which will benefit Adhearsion in the long run. Some of these are:

* A new extensible and performant SIP channel driver built on the pjsip SIP stack.
* Application of the Asterisk bridging framework throughout the project, providing consistency to management of channels while they are in a bridge.
* Stasis, a new internal message bus that conveys state about channels, bridges, endpoints, devices, and other objects throughout Asterisk.
* A heavily revamped version of AMI, focusing on improved channel semantics and consistency of event information.
* A new interface, the Asterisk REST Interface (ARI), which allows an external application to manipulate channels, bridges, and other telephony primitives directly.

We are aware of a [number of incompatibilities](https://github.com/adhearsion/adhearsion/issues?labels=asterisk-12-compatibility&page=1&state=open) between Asterisk 12 and Adhearsion, and are working on resolving them with a view to a release that will introduce compatibility with Asterisk 12. This release is expected before the end of January.

It should be noted that Asterisk 12 is a Standard Release of Asterisk, which means it will be maintained for only one year. The Asterisk team will cease to provide bugfixes to Asterisk 12 one year from now, on 2014-12-20, with security fixes ceasing on 2015-12-20. We continue to recommend that production applications use an LTS release (Asterisk 11). For applications in development that wish to take advantage of Asterisk 13 when it becomes available, Asterisk 12 is a good testing target.

You can find documentation specific to Asterisk 12 [on the Asterisk wiki](https://wiki.asterisk.org/wiki/display/AST/Asterisk+12+Documentation).

The Adhearsion team recommend testing your application on Asterisk 12 in preparation for a smoother upgrade to Asterisk 13 once it is released, and we appreciate any bug reports to augment our current knowledge of incompatibilities. Reports should be made [on Github](http://github.com/adhearsion/adhearsion/issues/new). Please especially review the [guidelines for reporting bugs](http://adhearsion.com/docs/dealing-with-bugs).

We're on the road to Asterisk 13, and it's shaping up to be very exciting! Keep your eyes open for compatibility fixes with Asterisk 12 and exciting new improvements coming up in Asterisk 13.
