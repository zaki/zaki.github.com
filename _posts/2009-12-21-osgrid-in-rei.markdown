---
author: zaki
date: '2009-12-21 19:26:02'
layout: post
type: blog
slug: osgrid-in-rei
status: publish
title: OSGrid in Rei
wordpress_id: '163'
tags:
  - 3Di
  - Opensim
  - Rei
---

## Textures

Before Rei was released at the end of September, it was called 3Di OpenViewer.
That version didn't need to download textures through the region
server, rather, it would download assets (textures, meshes, avatars) directly
from dedicated asset servers. For this reason, when someone logged in to an upstream
OpenSim grid with 3Di OpenViewer, textures and avatars would not appear.

But that changed when we released Rei as an open-source viewer at the end of
September. At that time, textures were already being fetched from OpenSim. You
can go ahead and grab the installer from github and it will display textures.
What it won't do with textures is correctly apply UV coordinates in some
(fortunately very rare) twisted-hollow-prim cases (but it was more about
PrimMesher than Rei). Since September, we have done many improvements to
PrimMesher - and Rei too, so now even those rare cases appear correctly (big
thanks to the bug reporters). Texture downloads are slow unfortunately, but
more on that later.

## Avatars

Now this is a very delicate issue. The heart of the issue is that currently
there is no non-LL viewer that supports SecondLife™ avatars. Let me say
that again: there are no viewers that support SL™ avatars. Naali has a
built-in mesh (the same as their old viewer), IdealistViewer has a built-in
mesh, LookingGlass doesn't yet have avatars (correct me if it does), Xenki
doesn't have an avatar yet as far as I know either.

The closest a viewer gets to SL avatar support is the PandaViewer by dahlia
([http://vimeo.com/6812213](http://vimeo.com/6812213)) , which supports avatar
shapes and actually looks really nice. Unfortunately it's not open source
(yet?), nor is it available in any form.

The thing with avatar support is that it's pretty difficult to do right. No,
not only technically, but there's the issue of using or not using Linden
Labs™'s original mesh. You can find [downloadable
meshes](http://secondlife.com/community/avatar.php), but there is a nice
warning hidden neatly inside an obj file:

> This model is released for use by residents of Second Life.
> It may be imported into external 3D modeling, texturing, and
> animation applications to aid in the creation of character
> animations and textures for Second Life, as well as for
> any non-commercial purpose.
> Use or modification of this model for any other commercial purpose is prohibited
> without the express written consent of Linden Lab.

With all this said, we are looking at SL™ avatar support. It definitely
won't happen before the end of this year, the target is early next year.

## OpenSim support

Of course Rei still has issues with upstream OpenSim support. This is
partially due to the very very bad way textures are downloaded (through a
single server, in UDP). The truth is, even the SL™ viewer chokes on those: in
heavy regions it would download the low resolution image, but the full image
is never actually downloaded. My personal opinion is that the sooner we can
break free from that way of transferring assets to clients, the better (guess
how 3Di OpenViewer delivers content, through dedicated asset servers).
Hopefully OpenSim will not remain an
SL™ clone and there will be lots of innovations in the virtual world space
coming from OpenSim. Rei is supposed to show what is possible when we can
control both the viewer and the server side without the fear of not being able
to work on one or the other for six months.

## The current state

#### LBSA Plaza

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="640" height="505" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/cgNL4-oMLOc&amp;hl=en_US&amp;fs=1&amp;" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="640" height="505" src="http://www.youtube.com/v/cgNL4-oMLOc&amp;hl=en_US&amp;fs=1&amp;" allowscriptaccess="always" allowfullscreen="true"></embed></object>

#### Wright Plaza

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="640" height="505" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/FBbR4txM9M4&amp;hl=en_US&amp;fs=1&amp;" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="640" height="505" src="http://www.youtube.com/v/FBbR4txM9M4&amp;hl=en_US&amp;fs=1&amp;" allowscriptaccess="always" allowfullscreen="true"></embed></object>

So, what do you think of the viewer so far? What sort of improvements do you
think would be nice? What do you think is missing, other than the avatar
support?

In general, what are the things you absolutely expect from a virtual world
viewer?

Let me know in the comments.

