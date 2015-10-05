---
title: Adhearsion 2.5 is here

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

Today the Adhearsion core team would like to announce the release of Adhearsion 2.5.0, the latest feature release in the Adhearsion 2 series, which includes a selection of new features:

*   Allow stopping all components executed by a controller when passing from it (`#hard_pass`) or at will (`#stop_all_components`)
*   Generated plugins include a .gitignore file
*   Detect something like "1234#" as characters instead of text
*   Add `--empty` switch to app generator for power users. Generates an app with less fluff.
*   Default generated applications with config appropriate for [Telephony Dev Box][1]
*   Allow preventing automatic call hangup after controller completion. Set `Call#auto_hangup = false` prior to controller termination.
*   Allow specifying a pre-join callback to #dial
*   Allow specifying ringback to `#dial` as either a list or a proc
*   Allow passing `:join_options` parameter to `#dial` to specify the kind of join to perform.
*   Allow passing `:join_target` parameter to `#dial` to specify who to join to (eg a mixer).
*   Generated apps now encourage storing most app code in `app/`, which is in the load path. Nothing in this directory is auto-loaded, but can be easily required without messy relative paths.

 [1]: http://github.com/mojolingo/Telephony-Dev-Box

Adhearsion 2.5 also includes one major compatibility change, and that is that **Ruby 1.9.2 is no longer supported**. Ruby 1.9.2 reached its end of life date last year, and is no longer receiving security fixes from the Ruby core team. For this reason, we encourage anyone who is still using Ruby 1.9.2 to upgrade at least to Ruby 1.9.3, or preferably to Ruby 2.1, since Ruby 1.9.3 [will reach EOL in just over a year's time][2]. For more details on this change, see our [GitHub update][3]. Additionally, see <a href="https://github.com/adhearsion/adhearsion/blob/develop/CHANGELOG.md#250---2014-02-03">the changelog</a> for further details on bugfixes included in this release.

 [2]: https://www.ruby-lang.org/en/news/2014/01/10/ruby-1-9-3-will-end-on-2015/
 [3]: https://github.com/adhearsion/adhearsion/pull/415

We hope you enjoy the new features in this release, and would appreciate your feedback on this release; note that the [Adhearsion Community Survey][4] is currently running, and would be a great place to share your feedback. We've already begun work on changes proposed for [Adhearsion 2.6][5] and even [Adhearsion 3.0][6], so check those out!

 [4]: https://www.surveymonkey.com/s/ZPGSGQK
 [5]: https://github.com/adhearsion/adhearsion/issues?milestone=21&page=1&state=open
 [6]: https://github.com/adhearsion/adhearsion/issues?milestone=18&page=1&state=open
