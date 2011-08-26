---
title: Adhearsion's RESTful Click2Call returns

excerpt: Unfortunately, the Adhearsion restful_clicktocall component has been broken for some time. But fear not, we've fixed it up.

location: Preston, England

layout: default-post

---

Unfortunately, the Adhearsion restful\_clicktocall component has been broken for some time. But fear not, we've fixed it up. After several posts to the mailing list reporting the issue, [Mojo Lingo](http://mojolingo.com) stepped up and sponsored getting things fixed. Both the [restful\_clicktocall](https://github.com/adhearsion/restful_clicktocall) component and the [restful_adhearsion](https://github.com/adhearsion/restful_adhearsion) client gem are now official Adhearsion projects. Additionally, the restful_rpc example component shipped with Adhearsion has become [ahn-restful-rpc](https://github.com/adhearsion/ahn-restful-rpc), a separate component gem. The old example component will be removed in the next release of Adhearsion.

So, now that things are all rosy, how do we get things setup? First off, you'll need a brand new Adhearsion 1.2 application:

```bash
{11:50}[ruby-1.9.2]~/Downloads ben% ahn create click2call
      create
      create  components/simon_game
      create  components/disabled/stomp_gateway
      create  components/disabled/xmpp_gateway
      create  components/ami_remote
      create  components/disabled/restful_rpc/spec
      create  config
      create  script
      create  .ahnrc
      create  components/simon_game/simon_game.rb
      create  components/ami_remote/ami_remote.rb
      create  components/disabled/xmpp_gateway/xmpp_gateway.rb
      create  components/disabled/xmpp_gateway/xmpp_gateway.yml
      create  components/disabled/xmpp_gateway/README.markdown
      create  components/disabled/stomp_gateway/stomp_gateway.rb
      create  components/disabled/stomp_gateway/stomp_gateway.yml
      create  components/disabled/stomp_gateway/README.markdown
      create  components/disabled/restful_rpc/restful_rpc.rb
      create  components/disabled/restful_rpc/restful_rpc.yml
      create  components/disabled/restful_rpc/README.markdown
      create  components/disabled/restful_rpc/example-client.rb
      create  components/disabled/restful_rpc/spec/restful_rpc_spec.rb
      create  config/environment.rb
      create  config/startup.rb
      create  dialplan.rb
      create  events.rb
      create  README
      create  Rakefile
      create  Gemfile
      create  script/ahn
```

Next, you'll need to ensure the old restful\_rpc component is removed:

```bash
{11:53}[ruby-1.9.2]~/Downloads ben% cd click2call
{11:54}[ruby-1.9.2]~/Downloads/click2call ben% rm -r components/disabled/restful_rpc
```

Next up, the restful\_clicktocall component:

```bash
{11:54}[ruby-1.9.2]~/Downloads/click2call ben% cd components
{11:55}[ruby-1.9.2]~/Downloads/click2call/components ben% git clone git://github.com/adhearsion/restful_clicktocall.git
Cloning into restful_clicktocall...
remote: Counting objects: 97, done.
remote: Compressing objects: 100% (60/60), done.
remote: Total 97 (delta 46), reused 84 (delta 33)
Receiving objects: 100% (97/97), 44.29 KiB, done.
Resolving deltas: 100% (46/46), done.
```

Now, we need to install the `ahn-restful-rpc` component which the clicktocall app depends on:

```bash
{11:55}[ruby-1.9.2]~/Downloads/click2call/components ben% cd ..
{11:55}[ruby-1.9.2]~/Downloads/click2call/components ben% echo "gem 'ahn-restful-rpc', :group => :components" >> Gemfile
{12:01}[ruby-1.9.2]~/Downloads/click2call ben% mkdir config/components
{12:05}[ruby-1.9.2]~/Downloads/click2call ben% cd config/components
{12:05}[ruby-1.9.2]~/Downloads/click2call/config/components ben% wget https://raw.github.com/adhearsion/ahn-restful-rpc/develop/config/ahn-restful-rpc.yml
--2011-08-26 12:05:42--  https://raw.github.com/adhearsion/ahn-restful-rpc/develop/config/ahn-restful-rpc.yml
Resolving raw.github.com... 207.97.227.243
Connecting to raw.github.com|207.97.227.243|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1279 (1.2K) [text/plain]
Saving to: `ahn-restful-rpc.yml'

100%[================================================================================================>] 1,279       --.-K/s   in 0s

2011-08-26 12:05:43 (136 MB/s) - `ahn-restful-rpc.yml' saved [1279/1279]
```

You'll also need to add the following line to `startup.rb` to enable the component:

```ruby
config.add_component 'ahn-restful-rpc'
```

Once the first leg of your calls get into Adhearsion, you'll need to pass them through. Add the following to your `dialplan.rb`:

```ruby
adhearsion {
  self.call.variables[:destination] = get_variable("destination")
  dial self.call.variables[:destination]
}
```

After configuring AMI, you can now start Adhearsion:

```bash
{12:08}[ruby-1.9.2]~/Downloads/click2call ben% ahn -
[2011-08-26 12:09:18] INFO  WEBrick 1.3.1
[2011-08-26 12:09:18] INFO  ruby 1.9.2 (2011-07-09) [x86_64-darwin10.8.0]
 INFO ahn: Adhearsion v1.2.0 initialized!
[2011-08-26 12:09:18] INFO  WEBrick::HTTPServer#start: pid=8089 port=5000
 INFO ami: Successful AMI actions-only connection into test@127.0.0.1
 INFO ami: Successful AMI events-only connection into test@127.0.0.1
 INFO agi: Adhearsion::VoIP::Asterisk::AGI::Server::RubyServer 0.0.0.0:4573 start
```

The last step is to get the web app up and running:

```bash
{12:09}[ruby-1.9.2]~/Downloads/click2call ben% cd components/restful_clicktocall/web
{12:09}[ruby-1.9.2]~/Downloads/click2call/components/restful_clicktocall/web@master ben% bundle install && bundle exec rackup
Fetching source index for http://rubygems.org/
Using json (1.5.3)
Using mime-types (1.16)
Using rack (1.3.2)
Using rest-client (1.6.7)
Installing restful_adhearsion (0.2.0)
Using tilt (1.3.3)
Using sinatra (1.2.6)
Using bundler (1.0.15)
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
bundle install  47.40s user 0.59s system 95% cpu 50.370 total
[2011-08-26 12:11:01] INFO  WEBrick 1.3.1
[2011-08-26 12:11:01] INFO  ruby 1.9.2 (2011-07-09) [x86_64-darwin10.8.0]
[2011-08-26 12:11:01] INFO  WEBrick::HTTPServer#start: pid=8307 port=9292
```

Now when you visit `http://localhost:9292/`, you'll be able to make sweet click2call goodness, courtesy of Adhearsion.
