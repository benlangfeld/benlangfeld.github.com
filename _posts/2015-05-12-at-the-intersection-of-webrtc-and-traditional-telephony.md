---
title: At the intersection of WebRTC and Traditional Telephony

excerpt:

location: Rio de Janeiro, Brasil

layout: default-post

---

Since the beginning, Mojo Lingo's focus has been building real-time communication applications. Our clients have often hired us because of our reputation as innovative and progressive; many of the applications we build are on the forefront of communications technology. One of the technologies we regularly employ, and the focus of this post, is WebRTC.

<a href="https://mojolingo.com/wp-content/uploads/2015/05/webrtc.jpg"><img src="https://mojolingo.com/wp-content/uploads/2015/05/webrtc-150x150.jpg" alt="webrtc" width="150" height="150" class="alignright size-thumbnail wp-image-2526" /></a>Many of our clients have large, complex, expensive platforms in which they have invested much money, time and effort over many years, whose operational properties and business value are known. Often, their regard for WebRTC is less certain. We’re frequently tasked with helping them to figure out how to evolve their product for anywhere from the immediate horizon to the next 5 years. This juxtaposition of stable platforms and modern interface (sometimes so modern that the specifications aren’t even finalized yet) can cause something of a dilemma.

<h2>Legacy Platform Limitations</h2>

While WebRTC is based on many of the technologies already in use for VoIP, it leaves the signaling protocol unspecified, and presents some new requirements which are not satisfied by existing systems. For example: Despite having reached End-Of-Life several years ago, Asterisk 1.4, remains in use by a large number of people. In order to integrate WebRTC with this or other similar older platforms, one must find a way to add support for:

<ul>
<li>Websocket transport, to enable browsers to send SIP messages</li>
<li>DTLS, the media encryption technology mandated by WebRTC (notably: WebRTC disallows the older SDES standard)</li>
<li>ICE, or Interactive Connectivity Establishment, the technology mandated by WebRTC for far-end NAT traversal, including STUN and TURN</li>
<li>New Codecs, including Opus, assuming the platform requires higher quality audio than G711 provides</li>
</ul>

<h2>Gateway Challenges</h2>

In situations where one cannot easily upgrade the core platform to add support for these things, they necessitate a gateway from the new world to the old. Unfortunately, the task of building such a gateway is quite involved, and there are some nasty issues to tackle along the way, for example:

<ul>
<li>SIP over Websockets requires re-using an existing connection to route all traffic. This is relatively easy to deal with for record-routed traffic in the case of a dialog established by the WebRTC user-agent, but what about calls from the old platform to the WebRTC endpoint? The Path (RFC 3327) and Outbound (RFC 5626) specifications exist in order to allow proxies and registrars to properly navigate this problem, but these place many requirements on the registrar in particular. Many target systems don’t support these specifications, including the aforementioned Asterisk 1.4. In such a case, one must bend the rules of SIP slightly in order to accommodate.</p></li>
<li><p>ICE resolution delay. If one simply proxies WebRTC traffic to a legacy system, particularly interconnecting with the PSTN and decrypting the media while performing ICE resolution along the way, one finds that there is often a significant perceived delay between the call being connected and the caller hearing audio. This is because ICE resolution (the process of choosing a path to traverse NAT) can take several seconds. In  order to provide a good user experience, something must paper over this delay within the gateway.</p></li>
</ul>

<h2>We Can Help</h2>

<p>Luckily, Mojo Lingo has dealt with these issues a few times already, and across varying use cases for several customers. We know what works and what doesn’t, and we’re well equipped to help figure out the best path for your business to adopt WebRTC as an extension of your existing platform with a short lead time and a high chance of success of your project. Get in touch to find out how.
