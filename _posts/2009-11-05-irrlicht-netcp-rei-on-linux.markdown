---
author: zaki
date: '2009-11-05 12:49:06'
layout: post
type: blog
slug: irrlicht-netcp-rei-on-linux
status: publish
title: Irrlicht.NETCP and 3Di Rei on Linux
wordpress_id: '116'
tags:
  - irrlicht
  - Opensim
  - Rei
---

<div class="alert-message block-message info">
I have started a cleaner code line which is now rebased to Irrlicht 1.7.1. Find the codes on
<a href="http://github.com/zaki/irrlicht.net" class="btn">Github:Irrlicht.NET</a>
</div>

### Irrlicht.NETCP

I've spent the last couple of weeks working towards rebasing the now abandoned
Irrlicht.NETCP to use the latest Irrlicht released: 1.6. There are multiple
reasons to do so, probably the most straightforward is to get the bugfixes or
some of the new features, like the new light manager. However the less direct
reason to do the rebase is that it includes Mac support out of the box - and
now that I have a macbook sitting on my desk at work (albeit, temporarily),
that is something of very high interest for me. In my dreamland, OpenGL ES
support (for the iPhone) would work without much work too - hey, I can dream,
can't I.

So, for this rebase, I've started a new repo on github, that I hope will soon
become the official repository for 3Di Viewer "Rei", and maybe even for users
of Irrlicht.NETCP (if any are still left, but who knows, maybe this can spark
some new interest). The repository starts with a clean Irrlicht 1.6 SDK and
gradually adds the fixes and features that are necessary for Rei (but of
course those are fixes and features that would probably come up in other
development as well).

On top of the improvements to Irrlicht, there is one huge patch, that makes
the IrrlichtW wrapper compatible with 1.6. This mostly consists of type
changes in the method interfaces and other minor details, but the patch itself
is pretty monstrous, as many things changed at the same time. It is only after
this collective patch, that the .NETCP library is usable again (it won't even
compile before this fix, so be careful with the history).

Later, IrrlichtML fixes were added. ML stands for multilingual and these
changes are required even though things finally started  moving away from c8*
to proper strings and io::paths in the core, stuff like IME support was not
included and the unicode support is still horrible to say the least.

### Getting Irrlicht.NETCP

If you want to give the new Irrlicht.NETCP libraries a try, head to github:
[http://github.com/zaki/irrlicht.netcp](http://github.com/zaki/irrlicht.netcp)
and check out the sources (either using git, or click on download in github).
For the impatient, I've also uploaded binary files for 32-bit windows (Release
mode).

To compile the libraries yourself, you will have to compile three components:
Irrlicht SDK, IrrlichtW and Irrlicht.NET, in basically this order. To compile
Irrlicht SDK, go to Irrlicht SDKsourceIrrlicht and open Irrlicht9.0.sln in
Visual Studio 2008 and build. IrrlichtW's solution is found in IrrlichtW,
named IrrlichtW.sln

Irrlicht.NET uses Prebuild to create its solution files, so before you can
compile, the file runprebuild2008.bat needs to be run. That creates the
solution file Irrlicht.NET.sln, that can be opened and built in VS.

### Rei and Irrlicht.NETCP

To use the new binaries with "Rei", some minor changes need to be made to
Rei's source. I committed these changes to my github repository:
[http://github.com/zaki/3di-viewer-rei](http://github.com/zaki/3di-viewer-rei), however the changes are not in the master branch, which I try to keep as
a clean integration copy (therefore there is a little time delay). Get the
Irrlicht 1.6 supporting sources from the 'linux' branch.

### One more thing...

Ahem... did I just say 'linux' branch? Well, that's probably because I've also
started working on Rei linux support and so far things look quite bright (as
opposed to the MacOSX support, but maybe later about that). I managed to make
Rei run on my Ubuntu machine in standalone mode using Irrlicht.NETCP and mono.
There are still a ton of things that need fixing, but it's only a matter of
time before a consistent framerate is achieved - I had driver issues and
occasional performance problems, probably not unrelated. There are some
problems with JPEG2000 support that I didn't have time to look at yet and
haven't written shaders (OpenGL seems to hate HLSL shaders for some reason :),
but everything in its own time.

So if you feel particularly adventurous, go ahead and grab the sources from
the 'linux' branch - Irrlicht.NETCP's master branch now includes linux support
-, compile them on your linux machine and do let me know how it works for you.


<img src="/images/3di-openviewer/3DiRei_onLinux.png" width="660" />

