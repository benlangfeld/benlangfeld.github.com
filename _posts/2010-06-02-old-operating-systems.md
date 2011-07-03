---
title: Old Operating Systems

excerpt: Many things hold us back, but none more so than old Microsoft products long past their retirement date

location: Manchester, England

layout: default-post

---

So yesterday I encountered the first major compatibility issue in StudioTalker development - Windows XP.

SSL is an integral part of any web application which transmits sensitive data between the browser and back-end, and StudioTalker does plenty of that. Traditionally, the SSL specification requires a dedicated IP per certificate, and we all know IPv4 is fast running out. Unfortunately in our case (and for many others, see [http://docs.heroku.com/ssl](http://docs.heroku.com/ssl)), a dedicated IP is not cost effective for now, and so we must employ some technical smarts, namely the [SNI](http://en.wikipedia.org/wiki/Server_Name_Indication) extension. This allows the use of SSL in shared hosting environments, such as Heroku.

But there’s a catch. IE, Chrome and Safari on Windows XP don’t support SNI (all modern browsers on newer operating systems do, with the exception of Konqueror). Now, Windows XP was released in October 2001. That makes it almost nine years old. There have been two major Windows releases in that time.

It’s impossible to please everyone, and that’s why we’ve decided not to try. Using StudioTalker on Windows XP is therefore not supported. As it stands, it’s unlikely IE will be supported on any platform. We firmly believe that legacy compatibility is an unreasonable expectation and so if you wish to use StudioTalker, we suggest upgrading from Windows XP and using a modern browser (we love WebKit, so Chrome or Safari are fine by us).

Hopefully we can play a very small part in moving the world forward.
