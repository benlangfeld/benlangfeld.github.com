---
title: Adhearsion 3 Roadmap

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

## Time Flies

It's been almost 5 years since [we released Adhearsion 1.0](https://mojolingo.com/blog/2010/adhearsion_1_0_now_showing/). [Adhearsion 2.0 was released more than 3 years ago](https://mojolingo.com/blog/2012/adhearsion-2-0-its-aliiiive/). In total, Adhearsion has been around for 9 years now.

 Wow, that's a really long time. Along the way, we've built a bunch of cool applications with Adhearsion which now underpin several big businesses and interesting projects, and a lot of the work on those has fed back into the open-source Adhearsion project. [<img src="https://mojolingo.com/wp-content/uploads/2015/06/FreeSWITCH.png" alt="FreeSWITCH" width="260" height="77" class="alignright size-full wp-image-2600" />](https://freeswitch.org) Shortly after the Adhearsion 2.0 release, I got fed up of promising FreeSWITCH support for so long and so [just went ahead and added it](https://mojolingo.com/blog/2012/adhearsion-and-freeswitch-its-about-time/). In collaboration with Grasshopper Inc, and thanks to Chris Rienzo, we managed to replace this implementation a year later with [native support for Rayo in FreeSWITCH](https://mojolingo.com/blog/2013/adhearsion-and-mod-rayo/). [<img src="https://mojolingo.com/wp-content/uploads/2013/02/asterisk_logo-e1361570669560-150x129.png" alt="Asterisk logo" width="150" height="129" class="alignright size-thumbnail wp-image-863" />](http://asterisk.org)

Until recently, we have [lacked support for AMIv2](https://mojolingo.com/blog/2013/asterisk-12-released/), meaning users of Adhearsion were held back from upgrading past Asterisk 11. Thanks to another collaboration with [Power Home Remodeling Group](https://powerhrg.com) we were able to [add AMIv2 compatibility to Adhearsion](https://mojolingo.com/blog/2015/adhearsion-compatible-asterisk-13), the first parts of which were [released in Punchblock 2.7.0](https://github.com/adhearsion/punchblock/blob/develop/CHANGELOG.md#v270---2015-06-09).

<br style="clear:both;">

But let's talk about what's coming down the road.

## What's Coming in Adhearsion 3

Now, on to the other items we are aiming to include in an Adhearsion 3.0 release, which we are hopeful to have ready by the end of the summer (northern hemisphere):

* [Core support for internationalisation](https://github.com/adhearsion/adhearsion/issues/361)
* [Merging adhearsion-asr into core](https://github.com/adhearsion/adhearsion/issues/428) to replace the old `#ask` and `#menu` methods which were found to be lacking
* Integration of [a web server](https://github.com/polysics/virginia) for serving grammars, prompts, recordings, etc, as well as a batteries-included APIs for Adhearsion applications
* Better performance through [simplification](https://github.com/adhearsion/punchblock/issues/199) and [support for Ruby language advancements](https://github.com/adhearsion/adhearsion/pull/552)
* [*Complete replacement of the EventSocket with Rayo for FreeSWITCH support*](https://github.com/adhearsion/punchblock/pull/201) This implementation has long been deprecated in favour of using Rayo, and everyone we have heard from has already migrated. Rayo's many technical advantages means our FreeSWITCH support will continue to be great
* [Better documentation regarding application packaging & deployment](https://github.com/adhearsion/adhearsion-website/pull/35) -- We'll share what we've learned from 3 years of deploying Adhearsion 2 apps

What we need from you: feedback on this list!  The Adhearsion community is core to the success of the project. Reach out to us on the [mailing list](https://groups.google.com/forum/#!forum/adhearsion), via [Github Issue](https://github.com/adhearsion/adhearsion/issues/new) or, better still, a [Pull Request](https://github.com/adhearsion/adhearsion).


## Adhearsion + Asterisk Support Plans

Our new support for Asterisk 13, which is [supported by Digium until October 2019](https://wiki.asterisk.org/wiki/display/AST/Asterisk+Versions), leads neatly to a discussion of how Adhearsion will move forward with compatibility for Asterisk in the future. As [we detailed](https://mojolingo.com/blog/2015/adhearsion-compatible-asterisk-13), there are improvements we can make to our support for Asterisk based on improvements in AMIv2 which are not backwards compatible with Asterisk 11. For now, we are supporting both AMIv1 and AMIv2, but in order to fully take advantage, we would have to drop support for Asterisk 11. When and how can we do this? Adhearsion follows [Semantic Versioning](http://semver.org/), meaning that removing features requires a bump to the major version number. So if that's all we care about, we could remove support for Asterisk 11 in Adhearsion/Punchblock v3.0.0. It's not, though, and we're equally concerned about having a smooth upgrade path for users of Adhearsion and Asterisk. For this reason, we are proposing the following policy:

> A major version of Adhearsion will always support at least two LTS releases of Asterisk. Support for "standard support" Asterisk releases will not be guaranteed, but best effort will be made to use standard releases as an opportunity to introduce support for the following LTS release.

For Adhearsion 2, this has long been Asterisk 1.8 and 11, and now includes Asterisk 13, but Asterisk 1.8 sees its end of life in October of this year, so:

> Adhearsion 3 will drop support for Asterisk 1.8 to anticipate its retirement.

Lastly, Asterisk 14/15 will be released in October 2015/2016, and will hopefully bring further improvements relevant to users of Adhearsion. Therefore:

> Adhearsion 3 will add experimental support for Asterisk 14 around the time of its release. Adhearsion 4 will include full support for Asterisk 15, dropping support for Asterisk 11.

I'd love to hear feedback on this policy; any suggestions from our community who may be impacted by this are much appreciated.

### What about ARI?

P.S.  Yes, we want to support ARI. There's one major blocker though, and that is that ARI does not yet support text-to-speech or speech recognition. Without those key ingredients, the Adhearsion framework would be significantly less useful on Asterisk. As soon as those become available via ARI, Adhearsion will be moving to ARI.  Of course, this change will be transparent for Adhearsion users; suddenly Adhearsion on Asterisk will just start to perform better.

## Where to go for more

Work on Adhearsion 3.0 is starting right now on the [Adhearsion](https://github.com/adhearsion/adhearsion/blob/develop/CHANGELOG.md#develop) and [Punchblock](https://github.com/adhearsion/punchblock/blob/develop/CHANGELOG.md#develop) develop branches, so stay tuned there for details of what goes in, as well as the [3.0.0 milestone](https://github.com/adhearsion/adhearsion/milestones/3.0.0). We'll announce again when we get to an alpha release, but in the mean time, get in touch in the comments below or via [one of our other channels](http://www.adhearsion.com/community).
