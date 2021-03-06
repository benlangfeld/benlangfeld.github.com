---
layout: default
title: Home
section: Home

feed: atom.xml
keywords: Blog, Programming, Ruby, Rails, Adhearsion
---

<img class='inset right' src='/images/ben_langfeld.jpg' title='Ben Langfeld' alt='Photo of Ben Langfeld' width='120px' />

Welcome
=======

I’m Ben Langfeld. I’m a software developer, and I build communications technology because I believe communication is one of humans' most fundamental advantages.

I work a lot with Ruby, tools like Adhearsion, FreeSWITCH and XMPP, among many others. I am a big open-source software advocate, being a founding member of the board of the Adhearsion Foundation.

I am a perfectionist, and seek to improve everything with which I come into contact, including myself. You can find out more about me here.

[![Feed icon](/files/css/feed-icon-14x14.png){:title="Atom feed of recent posts" .right}][feed]
I blog occasionally about my work and occasional other subjects. A [feed][] of the most recent posts is available.

[feed]: /atom.xml

{% for post in site.posts limit:5 %}
<div class="section list">
  <h1>{{ post.date | date_to_string }}</h1>
  <p class="line">
  <a class="title" href="{{ post.url }}">{{ post.title }}</a>
  <a class="comments" href="{{ post.url }}#disqus_thread">View Comments</a>
  </p>
  <p class="excerpt">{{ post.excerpt }}</p>
</div>
{% endfor %}

<p><a href="past.html">Older Posts &rarr;</a></p>

<script type="text/javascript">
//<![CDATA[
(function() {
    var links = document.getElementsByTagName('a');
    var query = '?';
    for(var i = 0; i < links.length; i++) {
      if(links[i].href.indexOf('#disqus_thread') >= 0) {
        query += 'url' + i + '=' + encodeURIComponent(links[i].href) + '&';
      }
    }
    document.write('<script charset="utf-8" type="text/javascript" src="http://disqus.com/forums/structure-and-process/get_num_replies.js' + query + '"></' + 'script>');
  })();
//]]>
</script>
