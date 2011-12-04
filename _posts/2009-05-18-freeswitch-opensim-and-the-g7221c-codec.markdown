---
author: zaki
date: '2009-05-18 17:48:53'
layout: post
type: blog
slug: freeswitch-opensim-and-the-g7221c-codec
status: publish
title: FreeSWITCH, OpenSim and the G722.1C codec
wordpress_id: '79'
tags:
  - Opensim
  - voip
---

[Kai Ludwig](http://www.talentraspel.de/) has recently left a very valuable
comment (thank you, Kai):

> Actually the new FreeSwitch siren14 codec has never been used with SLVoice!
> Everybody having voicechat with OpenSim is using PCMU without knowing it.
> <small>Kai Ludvig</small>

I didn't realize that at first either, but after he pointed this out,
I started looking for a way to enable siren14. I haven't really come
up with a way to use the SLVoice.exe included with the SecondLife viewer,
rather, I modified the
[voipforvw](http://github.com/zaki/slvoice) codes to support it.
Voice quality is now way better than the stock PCMU. ![Connecting to
FreeSWITCH with G722.1](/images/voip/freeswitch_g7221c.jpg)

