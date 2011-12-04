---
author: zaki
date: '2009-11-20 17:56:53'
layout: post
type: blog
slug: wii-controller-plugin-for-3di-rei
status: publish
title: Wii Controller Plugin for 3Di Viewer Rei
wordpress_id: '157'
tags:
  - 3Di
  - Opensim
  - Rei
---
<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="640" height="505" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/g_xum05b6xQ&amp;hl=en_US&amp;fs=1&amp;hd=1" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="640" height="505" src="http://www.youtube.com/v/g_xum05b6xQ&amp;hl=en_US&amp;fs=1&amp;hd=1" allowscriptaccess="always" allowfullscreen="true"></embed></object>

### Wii Controller Plugin

3Di Viewer "Rei" provides a great flexibility when it comes to adding custom
functionality to the core viewer by making it possible to create binary
plugins. Just drop these binaries into the Rei folder and let the viewer find
and load it automatically the next time it starts. While these plugins are
still in their early phase - we can do a lot more to ease the process of
deploying plugins and resolving their dependencies - you can get a glimpse at
their power already.

The WiiControllerPlugin is a fun project, a proof-of-concept, rather than a
commercial solution, so before you dive in, be warned, that it is sometimes
rough around the edges. With that out of the way, if you have Rei, head over
to http://github.com/zaki/ReiWiiControllerPlugin and grab the binary from the
Download section. Just copy the contents into the Rei installation folder
(usually C:Program Files3DiRei). And... that's basically it.

### How to connect the controller?

To use a Wiimote, you will need a Wiimote, a nunchuck and a bluetooth adapter.
I've heard that Toshiba adapters works best, but I have used Broadcomm's chips
successfully as well. It is said that the Microsoft stack doesn't work most of
the time with the Wiimote.

Before you start the viewer, connect/pair the Wiimote via bluetooth. If you
find it difficult to connect, I found that keeping (1) and (2) pressed
simultaneously while connecting helps a lot.

Once the Wiimote is connected, start Rei and if everything goes well, the
primary Wiimote will have its first LED turned on. You are now ready.

### How to use the Wiimote?

The WiiControllerPlugin allows you to use the Wiimote and the nunchuck to
control your avatar and the camera. The nunchuck's joystick, or the
directional controller on the wiimote can be used to make you avatar walk. Up
will always make your avatar walk forward, regardless of the position of the
camera.

The camera is controlled with a combination of the wiimote and the nunchuck.
Zoom in and out by holding down (Z) and tilting the nunchuck forward and
backward. Hold the wiimote straight up; while you press (B), you can tilt the
wiimote in four directions to rotate the camera around your avatar. Remember,
you don't have to look away from the screen to change the camera angle.

If you get lost with the camera, you can always press (C) to go back to behind
your avatar. Pressing (C) also switches to "follow" mode, so your camera will
"stick" to  your avatar and will follow it from behind if you move. Pressing
(C) another time will switch back to the free camera mode.

If you get tired of moving with the wiimote and want to switch back to using
the keyboard or mouse, you can disable avatar movements by pressing (-). Your
wiimote's will light the third LED when it's disabled. Press (-) again to
enable moving by the wii controller.

### About the demo

The video above contains a very dull maze, in which there are colored balls
that hurt or heal you when you touch them (I've never said I was a great
contents designer, have I :)) The demo shows how easy it is to integrate
custom features with an unmodified OpenSim grid. All that was used is simple
LSL script that detects collisions and gives commands via llSay. These
commands can be intercepted by javascript and further dispatched to all
plugins, that can act accordingly. In this case a simple health bar is
adjusted.

As my LSL was sending plaintext commands like "heal,20", the necessary
javascript looks really simple:

{% highlight javascript %}
function DispatchCommand(message)
{
  var strList = new Array();
  strList = message.split(",");

  if (strList.length == 2)
  {
    ctrl.Callback(strList[0], strList[1]);
  }
}
{% endhighlight %}

If you have a question, or have a good idea about plugins, or the
WiiControllerPlugin, please share it in the comments.

* * *

Please note: WiiControllerPlugin is not endorsed by Nintendo.
WiiControllerPlugin depends on  WiiMoteLib, which is release under the license
MS-PL. For more details, see
[http://wiimotelib.codeplex.com/](http://wiimotelib.codeplex.com/)

