---
title: Simply The Best...Why Ruby wins the happy programmer test every time

excerpt: Ruby just makes everything more concise, readable and understandable

location: Manchester, England

layout: default-post

---

Disclaimer: There may be better implementations in either language, but I am a relative newbie to both Ruby and Objective-J (similar in many ways to Objective-C), so I believe this to be a fair test.

Ruby makes happy programmers. You can avoid hackish routines like the Obj-J example:
{% highlight objc %}
var conversationAsString = "";

for(var i=0; i < [aMessages count]; i++) {
    m = aMessages[i];
    var messageTime = [CPDate dateWithTimeIntervalSinceNow:0];
    var messageBody = [[m firstChildWithName:@"body"] text];

    var messageAsString = "[" + messageTime + "] " + [m from] + ": " + messageBody;

    conversationAsString += messageAsString + "\n";
}

CPLog.info(conversationAsString);
{% endhighlight %}

by following the ruby way:

{% highlight ruby %}
puts messages.map { |m| "[#{Time.now}] #{m.from}: #{m.body.value}" }.join("\n")
{% endhighlight %}

Nice, concise and self explanatory. Point proven.
