---
title: Adhearsion <3 mod_rayo

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

We made some noise last week about how the release of Adhearsion 2.4 brought greater engine portability and how this was a good thing for your applications, but what did we mean?

Well, [FreeSWITCH](http://freeswitch.org) recently gained support for the [Rayo protocol](http://rayo.org), and Adhearsion 2.4 updated its support for Rayo to the [latest version of the specification](http://rayo.org/xep). This means that you can now run Adhearsion applications against FreeSWITCH using its latest native interface, hopefully gaining benefits in performance and reliability, as well as greater long-term stability and some extra features: ASR support, mixers and rendering of basic types like numbers, dates, etc without a TTS engine.

Mod_rayo was contributed to the FreeSWITCH and Adhearsion communities by [Grasshopper](http://grasshopper.com) and built by [Chris Rienzo](https://twitter.com/crienzo), so thanks to those guys for the excellent work!

But how to get started with using this? Well, the first thing you'll need is an installation of FreeSWITCH with [mod_rayo](https://wiki.freeswitch.org/wiki/Mod_rayo) included. Since mod_rayo is rather new, you'll need the freshly released FreeSWITCH 1.2.13. In order to [include mod_rayo in the build](https://wiki.freeswitch.org/wiki/Mod_rayo#Installing), you'll need to include it and its dependencies in modules.conf, and also in the resulting conf/autoload_configs/modules.conf.xml. Lastly, you'll need to [point your dialplan at Rayo](https://wiki.freeswitch.org/wiki/Mod_rayo#rayo_APP) and your FreeSWITCH setup is more or less done. There are [many options to fiddle with](https://wiki.freeswitch.org/wiki/Mod_rayo#rayo.conf.xml_Configuration), but the default config should work well to get started.

If all of that sounds rather daunting, you could check out [Telephony Dev Box](https://mojolingo.github.io/Telephony-Dev-Box), which now includes mod_rayo and makes getting started trivial.

You'll also need Adhearsion 2.4 or greater, which you can [install via Rubygems](http://adhearsion.com/docs/getting-started/installation). You'll then need to [configure your Adhearsion application to connect to your FreeSWITCH instance](http://adhearsion.com/docs/getting-started/freeswitch) and boot your application. Everything should magically spring into action, and you'll be good to go.

This is the best release of Adhearsion yet, and we hope you enjoy using it. Be sure to check out our [documentation](http://adhearsion.com/docs) for help building your application, and if you run into trouble, our [community support](http://adhearsion.com/community) will be sure to lend you a hand.
