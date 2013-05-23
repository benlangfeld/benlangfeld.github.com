---
title: Adhearsion Survey Results

excerpt: We surveyed Adhearsion users to find out how it is being used in the wild, and what factors are important to the people and projects using it. Here are the results.

location: Rio de Janeiro, Brasil

layout: default-post
---

A few weeks ago we put together a survey to gather data on how Adhearsion is being used in the wild, and what factors are important to the people and projects using it.

This survey closed on Friday 10th May, and we've got the results for you right here in full (minus identifying information) to browse through. Below please find our high-level takeaways from the survey responses that we are now using to process our next steps in Adhearsion and to consider the content for Adhearsion Conf 2013 (stay tuned):

* Respondents appear to love Adhearsion – it’s true.  (We have not missed the fact that the only people who knew about the survey may have been Adhearsion fans by definition as subscribers of our dist list…but, still!)
* While Asterisk is still the most-used platform, FreeSWITCH shows a lot of growth in a short amount of time, and many people appear to be considering a move from Asterisk to FreeSWITCH
* Roughly half of you have moved an application from some other solution (mostly Asterisk dialplan or Perl scripts) to Adhearsion
* Dynamic IVR and Dynamic routing are the most popular uses of Adhearsion, compared to their static counterparts
* Manual integration testing is still the most common approach, with automated unit testing second in line
* A large majority of people have found that Adhearsion has saved them time and/or money compared to alternatives they used or considered
* You guys love Ruby, but don't love our documentation. We are still trying to figure out where the gap lies - is it that people are not finding [the long-form documentation](http://adhearsion.com/docs) that was launched with Adhearsion 2, or [our API documentation](http://adhearsion.com/api)?  Or is this documentation easy enough to find, but unsatisfactory to you.  In either event, this is a great way to contribute to the project in a non-technical way. Please do feel free to [file documentation bug reports](http://github.com/adhearsion/adhearsion/issues) or even pull requests
* Most of you work in small teams (between 1-3 people) on Adhearsion projects
* A lot more of you use JRuby than we had realised - this is awesome
* A Web UI is a very popular app component, but native (desktop & mobile) apps are rare
* There are still a lot of Asterisk pre-1.8 deployments out there. This is surprising, since [Asterisk 1.6.2 was EOL'ed more than a year ago](https://wiki.asterisk.org/wiki/display/AST/Asterisk+Versions). You guys who admitted to still using something prior to 1.8, be wary! There are known security issues and other bugs.  The longer you wait to upgrade, the harder that upgrade will be.  For those of you using Asterisk 1.8, note that 11 is now the current LTS release.  We know from experience that the upgrade is pretty straightforward, especially if you are using Asterisk with Adhearsion exclusively.  You'll also benefit from a bunch of bugs fixed that are still present in 1.8, along with many other improvements (notably ConfBridge!).

Over the next few weeks and months we'll be studying this data in more detail to extract any lessons we can, and apply those to our decision-making process with regards to the future of the project. This is an exciting time, and we look forward to where this may lead us, as well as keeping you all in the loop.

Please don't hesitate to continue to send us feedback about Adhearsion whenever you like. You can [post on the mailing list](https://groups.google.com/forum/?fromgroups#!forum/adhearsion), [file feature requests or bug reports](http://github.com/adhearsion/adhearsion/issues) or [get in touch with Mojo Lingo](http://adhearsion.com/get-help) to discuss how we might help your project further.

Go ahead and take a [look through the results](http://mojolingo.com/wp-content/uploads/2013/05/Adhearsion-Survey-April-2013.pdf) for yourself. Let us know if you spot anything interesting.
