---
title: Adhearsion Voice Platforms - A Comparison

excerpt: When I’m at conferences or working with Mojo Lingo clients, I’m often asked about voice application projects that use the Adhearsion framework. Hopefully by providing an overview of the main decision points, you’ll be able to arrive at the most sensible choice of platform for your Adhearsion project.

location: Rio de Janeiro, Brasil

layout: default-post
---

When I’m at conferences or working with Mojo Lingo clients, I’m often asked about voice application projects that use the Adhearsion framework. I’ve noticed that the majority of newcomers to the Adhearsion community have already chosen the VoIP platform they want to use. While I don’t have exact numbers, anecdotal evidence suggests that more often than not, their choice of platform is based almost entirely on their experience and not necessarily on the technical merits of the supported platforms or their suitability to the project at hand.

In an effort to help my fellow developers and their clients, I want to describe the VoIP platforms supported by Adhearsion — Asterisk, FreeSWITCH, and Voxeo PRISM — and highlight the key differences between them. Hopefully by providing an overview of the main decision points, you’ll be able to arrive at the most sensible choice of platform for your Adhearsion project.

## Asterisk

Asterisk is the quintessential open-source PBX. Ask a non-telephony person about open-source telephony and chances are he’ll know about Asterisk. Asterisk has been around for a very long time, and is used by an extraordinary number of people to implement SOHO PBXs, as well as some larger projects. Asterisk is very easy to install, with several appliance-like distributions available such as [FreePBX](http://www.freepbx.org/freepbx-distro) and [Elastix](http://www.elastix.org/). Most people who use Asterisk set it up once and — with the exception of occasionally adding extensions — never touch it again. Used for basic call routing, voicemail, inbound queues and occasionally IVRs, Asterisk can be utilized for more complex scenarios. Unfortunately, elaborate applications (and external integrations) quickly become un-maintainable and/or too complex to understand. And some things are just plain impossible with Asterisk.

Adhearsion, which began as a “niceness” layer on top of Asterisk (helping to avoid the frustration caused by trying to write complex applications in extensions.conf), has always supported Asterisk; in fact, v1 *only* supported Asterisk. The advent of Adhearsion meant developers could write sophisticated Asterisk applications in a real programming language, with all the attendant benefits.

Then developers, myself included, starting asking for FreeSWITCH support.

## FreeSWITCH

FreeSWITCH is ‘the other' open source PBX. While it began as a fork of Asterisk, FreeSWITCH is more than just another open source PBX. A "soft-switch", FreeSWITCH is designed for flexibility. It’s highly configurable, and can be bent to be almost anything you want it to be, although this sometimes means writing C/C++ code.

As a result of the platform-independent design of Adhearsion 2 and Punchblock, FreeSWITCH support was added in Adhearsion 2.1. And it was easy to add, thanks in part to the incredibly clean and comprehensive API of FreeSWITCH (EventSocket). There are, however, still some things incomplete in our implementation, and work is underway to improve this support (Further details on this work will be available soon, so sit tight.). The good news is that, in the near future and over time, FreeSWITCH support will become more comprehensive.

## Voxeo PRISM

Voxeo PRISM is a communications application server with support for SIP, HTTP & XMPP, as well as a rich media server for TTS and ASR, among other things. Produced by Voxeo Labs, a sister company of Voxeo Corporation, one of the biggest providers of IVR technology in the world, Voxeo PRISM is a commercial product and comes with all that being a commercial product entails.

Unlike Asterisk and FreeSWITCH, PRISM *is not* a PBX, although it is possible to write applications for PRISM to provide PBX features such as voicemail, intelligent routing, queuing, and so forth. In the Adhearsion 2.0 release by way of the Rayo protocol, we added support for PRISM. Many of the apps mentioned above can now be written using Adhearsion rather than by dropping down to SIP servlets. Of the three platforms I’ve mentioned — Asterisk, FreeSWITCH and PRISM, users of PRISM enjoy the most comprehensive set of features currently available in Adhearsion.

<table>
  <thead>
    <td colspan="2"><h3>Asterisk</h3></td>
  </thead>
  <thead>
    <td>Benefits</td>
    <td>Drawbacks</td>
  </thead>
  <tr>
    <td>
      <ul>
        <li>Free</li>
        <li>Widely used - large ecosystem of Asterisk compatible software and community of consultants</li>
        <li>Good availability of operating system packages or full distributions bundling Asterisk with an admin UI</li>
        <li>Most error messages can be answered by Google</li>
      </ul>
    </td>
    <td>
      <ul>
        <li>Not designed for complex applications</li>
        <li>Least performant of supported platforms</li>
        <li>Missing API support for some Adhearsion features, especially for TTS and ASR</li>
        <li>No real clustering options</li>
        <li>Scattershot documentation in places</li>
      </ul>
    </td>
  </tr>
  <thead>
    <td colspan="2"><h3>FreeSWITCH</h3></td>
  </thead>
  <thead>
    <td>Benefits</td>
    <td>Drawbacks</td>
  </thead>
  <tr>
    <td>
      <ul>
        <li>Free</li>
        <li>Good performance with Adhearsion</li>
        <li>Well documented</li>
        <li>Good mailing list support</li>
        <li>Implements nearly all Adhearsion features</li>
      </ul>
    </td>
    <td>
      <ul>
        <li>Smaller community than Asterisk</li>
        <li>Official packages not available, must be built from source</li>
        <li>History of poor versioning policy</li>
      </ul>
    </td>
  </tr>
  <thead>
    <td colspan="2"><h3>Voxeo PRISM</h3></td>
  </thead>
  <thead>
    <td>Benefits</td>
    <td>Drawbacks</td>
  </thead>
  <tr>
    <td>
      <ul>
        <li>Vendor supported</li>
        <li>Well documented</li>
        <li>Good clustering capability</li>
        <li>PCI compliant</li>
        <li>Highly scaleable</li>
        <li>Excellent ASR &amp; TTS built in</li>
        <li>Implements all Adhearsion features</li>
      </ul>
    </td>
    <td>
      <ul>
        <li>Non-free</li>
        <li>Closed source</li>
        <li>Few community resources available</li>
      </ul>
    </td>
  </tr>
</table>

## Questions to Help You Choose a Platform

### Are reliability, good support and an SLA more important to you than cost?

If the answer is “Yes”, go for PRISM or be sure to find a support provider for one of the other platforms. Keep in mind that your Adhearsion application will likely not be covered under a platform deal. This is because Adhearsion uses an abstraction on top of the Asterisk and FreeSWITCH APIs that many support providers are unfamiliar with or unable to support. Consider taking out support contracts with a provider that will cover your whole stack, including your application code.

### Do you have a philosophical affinity with Open Source software or require the ability to make modifications to your platform in-house?

If yes, use Asterisk or FreeSWITCH. While Asterisk has the longer open-source legacy, in our experience, getting patches in to FreeSWITCH tends to be quicker and the code easier to understand and modify.

### Do you need easy integration with ASR and TTS engines?

If so, go with Voxeo PRISM or maybe FreeSWITCH (though be aware that not all combinations are known to work, we'll provide more details on this soon). The most comprehensive support for ASR and TTS in Adhearsion is currently only available on Voxeo PRISM; we do not yet support MRCP-based ASR on Asterisk or FreeSWITCH, although this is coming very soon. Unfortunately, Asterisk's UniMRCP adapter is missing several features, and getting it compiled is difficult. The situation is improving, but the other platforms have a head start here.

### Do you need free simple TTS?

Then consider FreeSWITCH. It comes bundled with flite, which produces fairly terrible text to speech, but is completely free.

### Do you have a large deployed infrastructure or significant level of in-house experience with a particular platform?

While it is tempting to 'go with the flow' on new projects, it is my firm belief that your company’s history and comfort level with a particular platform should be carefully and critically weighed against the technical fit of the platform and project. This mostly applies to choosing between Asterisk and FreeSWITCH, and I strongly suggest that anyone tempted to use Asterisk simply out of habit give FreeSWITCH a try and evaluate the fit. One of the benefits of Adhearsion’s platform independence is that the same application should be deployable to any of the supported platforms and should work similarly, save a small collection of differing features (for example, Asterisk and FreeSWITCH currently do not support mixers or whisper functionality). And note also that multiple platforms can happily coexist in a datacenter; just because your other apps run on one platform doesn't mean the next one couldn't run on another ;)

If any of you have input or feel differently about my logic, leave a comment; I’d love to hear from you. Likewise if anyone feels they need further help choosing the correct platform for their application or selecting any other components of their stack, feel free to reach out to us at Mojo Lingo to see how we can help your project succeed.
