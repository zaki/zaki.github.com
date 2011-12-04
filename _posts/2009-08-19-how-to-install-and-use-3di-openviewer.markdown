---
author: zaki
date: '2009-08-19 02:06:27'
layout: post
type: blog
slug: how-to-install-and-use-3di-openviewer
status: publish
title: How to install and use 3Di OpenViewer
wordpress_id: '86'
tags:
  - 3Di
  - Opensim
  - Rei
---

<div class="alert-message block-message info">
For updates about 3Di Viewer "Rei"'s features (particularly textures
and avatars), also see the post <a href="/2009/12/21 /osgrid-in-rei" class="btn">
OsGrid in Rei</a></div>

## How to install 3Di OpenViewer?

First, please note, that the viewer is currently for Windows only and
3Di only supports Internet Explorer 6+ and Firefox 3.0 and 3.5 at this time.

### Step 1.

###### Internet Explorer

Go to [http://www.3di-opensim.com/openviewer/ie_install.html ](http://www.3di-opensim.com/openviewer/ie_install.html )

The ActiveX bar will appear, click Install

###### Firefox

Go to [http://3di-opensim.com/openviewer/ff_install.html](http://3di-opensim.com/openviewer/ff_install.html)

Click on the orange button with the arrow to download and install the plugin.

### Step 2.

The installer will appear.

![3Di OpenViewer Installer screen 1](/images/3di-openviewer/p_install06.jpg)

The first screen is only an introduction. It asks you to close other
applications before clicking next. The left button says Next, the right one is
Cancel.

![3Di OpenViewer Installer screen 2](/images/3di-openviewer/p_install07.jpg)

The second screen is the EULA. I won't try to translate it to english, you can
get a raw English version by pushing this through any online translation
service. The long and the short of it was summarized in these two sentences:

+ No commercial use
+ No reverse engineering

The first radio button says Agree, the second says Disagree. Again the buttons
are Back, Next and Cancel in that order. I guess it is pretty difficult to
Agree with something you can't read (I always feel that way in Japan :) ).
There are no traps in there and as long as you don't use it to become a
millionaire celebrity without 3Di's consent, you should be alright. (If you
happen to be a millionaire celebrity already, you should definitely contact us
:) )

![3Di OpenViewer Installer screen 3](/images/3di-openviewer/p_install08.jpg)

The next screen has three buttons saying Back, Install and Cancel
respectively. After clicking Install, it will start the automatic install
process. When it finishes, you can click on the Finish button on the next
screen.

## Is it possible to use 3Di OpenViewer in English?

This question is asked many times lately, so here's a short guide on how to do
it. Please do note, that this is sort of a pre-release thing, that might in
some places be a little rough around the edges. And if you find any leftover
Japanese text after the switch, do let me know.

When you install the viewer, and open a page with the viewer embedded, it will
appear in default Japanese locale, but it is possible to change this to
English. There are two ways to do it: you can log in to an OpenSim grid with
the Japanese version and change the locale from the menu, or you could change
the configuration file to make the viewer appear in English from the first
login page. Either method you choose, you will have to start the viewer at
least once (to initialize the necessary directories and configuration files).

Method 1 - Using the menu

![3Di OpenViewer login Japanese](/images/3di-openviewer/openviewer_login_jp.jpg)

Using the login screen, specify the
login URI, first name, last name and password in that order, then click on the
button to log in.

![3Di OpenViewer settings Japanese](/images/3di-openviewer/openviewer_settings_jp.jpg)

After you log in, move the
cursor towards the upper right corner until the menu appears. Click on the
wrench icon to go to settings. The last selectbox on the first tab is the
locale, you should select the first option and press OK. A dialog will appear
telling you that the settings will be effective after you restart the viewer.
Do so, and the UI will appear in English now.

![3Di OpenViewer login English](/images/3di-openviewer/openviewer_login_en.jpg)

Method 2 - Changing the configuration file

After you start the viewer, the configuration file will be created in your
local settings directory. On XP, this will be in
`Documents and Settings/<username>/Local Settings/Application Data/3Di/OpenViewer/`, while on
Vista and Win7, this will be in
`Users/<username>/AppData/LocalLow/3Di/OpenViewer`

Open the file in configs/ called `OpenViewer.ini` and change locale to en

    locale = en

Start the viewer again and it should be in English now.

## Can I connect to OpenSim grids?

The short answer is 'yes'. The long answer is 'yes, but'. You can definitely
log in to OpenSim grids with the current release version and prims will
display. However textures and the avatar will not currently appear. The reason
for this is that assets are not downloaded through the region server. We are
working on a fully compatible version that we will release in the future, but
for the moment to get the full experience, unfortunately you will have to
connect to 3Di's servers. We are also preparing more demo servers, but I have
to ask for just a little more patience on that.

If you try 3Di OpenViewer, your feedback is always appreciated.

