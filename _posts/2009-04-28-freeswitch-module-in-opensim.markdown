---
author: zaki
date: '2009-04-28 03:26:01'
layout: post
type: blog
slug: freeswitch-module-in-opensim
status: publish
title: FreeSWITCH module in OpenSim
wordpress_id: '41'
tags:
  - OpenSim
  - VoIP
---

<div class="alert-message block-message info">
<strong>Update</strong>: OpenSim has changed substantially since this post, so my explanation will probably not work anymore.
</div>

It seems that Linden Labs has finally removed the hardcoded vivox.com references
in 1.22 making third party voice chat work with the original slvoice.exe.

At the same time Rob Smart from IBM stepped up the game and
provided a solution for FreeSWITCH that now also works without an SLVoice
replacement. I've also been looking at FreeSWITCH for a while and just when
I finally have some spare time to play around with it, this new module comes out:
and it's really awesome! First of all, it works without having to install kernel
modules or suffer the lack of proper SIP Session-Timer (RFC4028) implementations.

There are three components needed for this setup.

## OpenSim

Because the new FreeSWITCH module is experimental, it
is only found in trunk, and as usual, please note, that trunk is **unstable
**it might have serious bugs, it can corrupt your data, or [eat your
children](https://lists.berlios.de/pipermail/opensim-
users/2009-April/001878.html), especially if you do the unthinkable and try to
install it in a production environment, which you should never do. Ever! No
exceptions. If you are brave enough, you can proceed very carefully and try
out the trunk.

## FreeSWITCH

I tried the Windows binary snapshot from 
[here](http://wiki.freeswitch.org/wiki/Installation_Guide): and it works
well. For configuration instructions take a look at the [OpenSim wiki
page](http://opensimulator.org/wiki/Freeswitch_Module). Linux installation
instructions are also included there.

## SecondLife Viewer

Only versions later than 1.22 are supported, so be sure to grab a newer version from Linden Labs.

## Final touches

After setting up FreeSWITCH and OpenSim, connecting to the grid and enabling
voice chat by default nothing will happen.
Conference chat won't work with the default estate settings, because
parcel voice is disabled. In the video, I'm setting "Use a private spatial
channel", but "Use the estate spatial channel" should work as well. Enable
voice, and it will work now. After this, conference and private chat features
will work. On my setup, I found that private chat is quite clear and easy to
understand, but the spatial conferences are way too low quality, but that might be
a configuration issue on my environment.

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="640" height="505" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/7k_xQQOcYDk&amp;fmt=22&amp;hl=en&amp;fs=1&amp;color1=0x2b405b&amp;color2=0x6b8ab6&amp;hd=1&amp;border=0" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="640" height="505" src="http://www.youtube.com/v/7k_xQQOcYDk&amp;fmt=22&amp;hl=en&amp;fs=1&amp;color1=0x2b405b&amp;color2=0x6b8ab6&amp;hd=1&amp;border=0" allowscriptaccess="always" allowfullscreen="true"></embed></object>

