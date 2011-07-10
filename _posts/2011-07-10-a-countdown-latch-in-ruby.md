---
title: A Countdown Latch in Ruby

excerpt: Java's CountDownLatch has many uses in concurrent applications. I've ported the API to Ruby and highlight some potential uses here.

location: Preston, England

layout: default-post

---

Ruby is often said to not be an appropriate language for concurrent applications, and this criticism tends to come as a result of the [Global Interpreter Lock](http://en.wikipedia.org/wiki/Global_Interpreter_Lock) (GIL) which restricts both MRI (Ruby 1.8.x) and YARV (Ruby 1.9+) to running a single thread at a time, despite the fact that [YARV maps Ruby threads to real system threads](http://www.igvita.com/2008/11/13/concurrency-is-a-myth-in-ruby/).

It is true that Ruby's primary implementation cannot, then, do real parallelism. JRuby, on the other hand, does concurrency just fine, since JRuby threads are backed by Java threads, which are in turn scheduled by the operating system. Additionally, there is a [difference between concurrency and parallelism](http://stackoverflow.com/questions/1050222/concurrency-vs-parallelism-what-is-the-difference). Ruby has many built in features that make concurrency easy to achieve and manage safely.

One issue with concurrent applications is signalling progress among multiple threads of execution. A simple solution can be borrowed from [Java's `CountDownLatch`](http://download.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/CountDownLatch.html). An object of this type can be used to synchronise threads in time. A `CountDownLatch` instance is created with a count, and then a method is called on it to decrement that count. Meanwhile, another thread may call a method on it which blocks until the count reaches zero, at which point the `CountDownLatch` is released and the waiting threads may continue execution.

If this sounds somewhat similar to Ruby's [`ConditionVariable`](vhttp://www.ruby-doc.org/stdlib/libdoc/thread/rdoc/classes/ConditionVariable.html), that's because it is. A `ConditionVariable`, however, is essentially a `CountDownLatch` with a constant count of one. If we were to need to count down more than one action in Ruby, we'd need to implement an API similar to `CountDownLatch`. And so [that's what I've done](https://github.com/benlangfeld/countdownlatch). A simple usage example given in the README goes something like this:

{% highlight ruby %}
require 'countdownlatch'

latch = CountDownLatch.new 2

Thread.new do
  2.times do
    sleep 1
    latch.countdown!
  end
end

latch.wait 10
{% endhighlight %}

So, what's happening here? Well, we're first creating a latch which expects to be counted down twice before it releases. Then, we spin up a new thread which sleeps for one second, then counts down the latch; it does this twice, in fact. While this is executing, we block the main thread by calling `#wait` on the latch. Additionally, we pass an optional parameter of 10, which is the number of seconds after which we should give up waiting. In this case, the latch will be released after 2 seconds or so, and so you'll see the call to `wait` return `true` after that time.

So, some use cases:

* Waiting for several threads to finish before terminating the main thread.
  {% highlight ruby %}
  require 'countdownlatch'

  latch = CountDownLatch.new 2

  2.times do
    Thread.new do
      ...some long running process...
      latch.countdown!
    end
  end

  latch.wait
  {% endhighlight %}
  This is useful if your threads get to a point that you want to be notified of, part of the way through a long execution time. If you only care about when they finish, you could do `threads.each &:join`, but this is slightly inflexible. With `CountDownLatch` you could easily create a thread pool to which threads could be added and removed, and then await the entire pool finishing. This allows significant fluidity, and is something I'll demonstrate in a future post.

* Queuing multiple threads to start simultaneously.
  {% highlight ruby %}
  require 'countdownlatch'

  latch = CountDownLatch.new 1

  10.times do
    Thread.new do
      latch.wait
      ...do something useful, but not before the rest do so also...
    end
  end

  latch.countdown!
  {% endhighlight %}
  Here, each thread's first operation is to wait on the latch, and once all threads had been created, you release the latch, allowing them to begin operation.

* [Testing asynchronous code](http://cantina.co/2011/04/27/using-a-latch-to-test-asynchronous-code/)

There are many more complex situations where a `CountDownLatch` would be useful, and these are highly application specific. It's true that many of the use cases for `CountDownLatch` are the same as those for `ConditionVariable` and you could just use the latter, however there are three things that favour the use of `CountDownLatch`:
* **Consistency**: If you have two things which do the same thing, but one is more flexible, why not use the more flexible one all the time, as long as it's not more complicated?
* **Encapsulation**: A `CountDownLatch` keeps its own Mutex which it uses in combination with the `ConditionVariable`, so you don't have to.
* **Identity**: A `CountDownLatch` can tell you if it's been released or not. A `ConditionVariable` cannot.
* **Determinism**: If the latch has previously been released, waiting for it will have no effect and return immediately. Thus, it's impossible to "miss" the latch being released.
