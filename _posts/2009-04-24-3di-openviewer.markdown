---
author: zaki
date: '2009-04-24 03:31:19'
layout: post
type: blog
slug: 3di-openviewer
status: publish
title: 3Di OpenViewer
type: blog
tags:
  - 3Di
  - Opensim
  - Rei
---

Disclaimer: I am a senior engineer working on multiple products - including
various OpenSim technologies and most recently 3Di OpenViewer - at Japan-based
3Di Inc. This is a personal blog, and my opinion does not necessarily always
represent that of my employer.

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="425" height="344" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/r_lbBw6wFRQ&amp;hl=en&amp;fs=1" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="425" height="344" src="http://www.youtube.com/v/r_lbBw6wFRQ&amp;hl=en&amp;fs=1" allowscriptaccess="always" allowfullscreen="true"></embed></object>

## Introduction

3Di has [released](http://3di-opensim.com/demo-ec/index.html) (Japanese) its
virtual world viewer last week. It is an embedded viewer, that works directly
in a web browser; it connects to the virtual world server solution also
provided by 3Di, called [3Di OpenSim Enterprise 1.0](http://3di-
opensim.com/product/feature/) (Japanese) that has quite a few addition on top
of the [community version](http://opensimulator.org), utilizes much more than
just SL prims to create a nice looking environment even within the limitations
of the web-browser.

## Open?!

Let's get the biggest question out of the way first: the virtual ink on the
press release hasn't even dried, but I was already asked where one could
download the source code. After all, it is called _Open_Viewer isn't it.

The short answer is: it _will_ be released as open source at a later - not too
distant - stage. Personally I think that opening up the viewer will be crucial
for two simple reasons: first, the future of OpenSim depends on a viewer that
opensim developers can look at without running the risk of getting burned by
the license - and serious innovation requires some degree of control (I met
with "we can't do that without changing the viewer" so many times). Second,
tying back to the first reason, personally I think we are in a kind of a
deadlock with the SL protocol. It was never designed for the sort of things
the opensim community dreams about: rather, it is a very centralized, "the
region server does everything" sort of protocol, that needs a fertile testing
ground in the shape of a viewer that you can bend to your own will.

When exactly can you get the source code? I don't really have the answer to
this question, as it is not nearly my decision. For now, just keep an eye on
this viewer because we are going to deliver some features that I hope will
show everyone that by changing the viewer, you can accomplish extraordinary
things.

## Features

The main distinction is of course that this viewer runs directly inside the
browser. Now, does that mean, that it is forever constrained within the limits
of a solitary HTML page? Not really.Internally we sometimes use a standalone
version for testing, so technically there is nothing stopping someone to
"liberate" (as I'm sure many would call it so:) ) the viewer from the evil
browser. Our first target was IE only (boohoo, yes) and at the very least
people can start using it they way they are already used to after years of
experience with ye' olde series of tubes and, equally importantly, provide us
with feedback on the important questions (no, not about our plugin system or
our 3D engine). And the next stop is Firefox and Safari support.

The viewer itself is operated with the mouse only. This makes it simple and
straightforward; on the other hand, there are not so many actions to do: you
can walk around, run about, sit down to objects or touch objects.  More
complicated actions like chat or teleporting are of course in a simplified
menu system. Our first priority is to make OpenSim - and virtual worlds in
general - accessible to a wide audience. This will hopefully help the
community indirectly as well as more and more people get used to using virtual
worlds, more businesses will join the ride. Of course we could have gone to
the other extreme and create a highly customizable meta-GUI system, but for
the uninitiated first-time virtual world user, that wouldn't really help.

The SL Viewer supports prims, which is a great thing if you have a limited
bandwidth. Or if you push every single bit through your region server. But
realistically, getting professional content into SecondLife can pose a
challenge because of this. As realXtend has also realized this before, 3Di
OpenViewer also enables content providers to use their usual 3D content
created with most major 3D modelling software like 3DS Max or Maya with little
or no change. This should further lower the entry barrier to many CPs that
don't really have the time and resources to invest in creating prims for all
of their popular contents. If they can import their content as-is, the cost
goes down and at the same time customer satisfaction goes up. In turn, there
is an increase in virtual world penetration, that is good for 3Di as well as
the community. (Hopefully the trend to make virtual worlds popular and more
accessible can be seen here)

The last new feature for today is the JavaScript API: we are providing ways
for a fair amount of intercommunication with a CP's HTML content and
OpenViewer.You want to create an e-commerce solution in OpenSim supporting
currency? Why go to the trouble of recreating your already existing EC in the
virtual world, when you can quite easily communicate between the 3D world and
the 2D world. The demo site is actually showing a mockup for an EC site that
doesn't require lengthy configurations and recompilation of OpenSim. No, it's
not an end-all-be-all, but it's a simple solution that one can use to quickly
start up a new business in the virtual worlds.

## In closing

There might be two things apparent from this post: first, I tried to convey
why I think a new viewer, and specifically an in-browser viewer is good for
the community. Second, I never talked about technical details. This latter, I
am planning to ammend in a later post, but first I just wanted to make it
clear that 3Di OpenViewer is not a product in isolation, and although it is
only a first step towards a bright future for OpenSim and virtual worlds in
general, I believe that it will be a significant one. So please keep an eye on
the product, and do post any feedback you might have in the comments.

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="425" height="344" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/3dlATmXQCtc&amp;hl=en&amp;fs=1" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="425" height="344" src="http://www.youtube.com/v/3dlATmXQCtc&amp;hl=en&amp;fs=1" allowscriptaccess="always" allowfullscreen="true"></embed></object>
