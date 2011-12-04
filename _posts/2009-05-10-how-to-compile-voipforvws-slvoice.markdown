---
author: zaki
date: '2009-05-10 01:18:08'
layout: post
type: code
slug: how-to-compile-voipforvws-slvoice
status: publish
title: How to compile Voipforvw's SLVoice
type: blog
tags:
  - Opensim
  - voip
---

## What is Voipforvw?

With the SecondLife viewer comes an executable named SLVoice.exe. This is the
piece of software that is responsible for the "low level" voice communication,
so while the viewer does the accounting (login information, UI), this part
does all the heavy lifting of contacting the voice servers and of course
translating the voice of the speaker to RTP packets and back.

[voipforvw](http://sourceforge.net/projects/voipforvw/)
(voip for virtual worlds) is a replacement for this executable.
The core of this application was originally developped by
[3Di](http://www.3di.jp/en/index.html)

## Why should I care about it?

The original SLVoice was developped for Linden Labs by a
company named [Vivox](http://www.vivox.com/) and has a proprietary license.
The good news is, that it is insanely high quality (it runs on a couple of
platforms and powers virtual worlds like SecondLife and games like EVE Online).
It also has 3D positional audio.

The bad news is that it's proprietary to
the point that you are explicitly disallowed from using it for OpenSim.
The [FAQ](http://www.vivox.com/open/faq.php) on vivox's home page explicitly says:

> <h4>Can I distribute this to friends?</h4>
> **No**, currently Vivox software and service is offered for individual, non-commercial use only.
>
> <h4>Can I use this outside of SecondLife</h4>
> **No**, the currently offered software is only available for the Second Life Viewer open source community.

## What's the catch?

Well, unfortunately there *is* a catch. The license of the SIP/RTP stack
used in vopforvw is GPL. This has a simple effect on the whole project, that
is, voipforvw automatically becomes GPL. Unfortunately currently there is no
good library that comes with a BSD-like license, the closest we can get is
LGPL. (If you happen to know of one, please do let me know in the comments).
While I'm a fan of C#, there is basically no free SIP/SDP library of any
license for C# either.

Second, at the moment noone is maintaining the Linux version: in theory it
should compile just OK, but that is not something I'd personally guarantee. I
will try to look into that later.

## But it's difficult to use.

I've heard this argument for quite some time: it is probably my failure to not do
much about it . Compiling the SLVoice replacement is not really that difficult. If
you have problems with building, take a look at the screencast below and see
if it can help fixing your problem. And of course, you can leave comments here

**Please note:** trunk contains a version that only works with SLViewer versions
below 1.22 (1.19, 1.20 and 1.21 were tested). For 1.22, I started a branch named
sl_1.22 that contains an updated version that - in turn - only works with 1.22.
As I am not allowed to look at the viewer's source code, many thanks go to yk,
who described the major API changes to me. Unfortunately this also means, that I can't
guarantee that the 1.22 version works properly. At least I had success making conferences
work with that branch, but further testing will be needed for it to be integrated
back into trunk.

<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="640" height="505" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="allowFullScreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="src" value="http://www.youtube.com/v/snEyl-hLhVo&amp;hl=en&amp;fs=1" /><param name="allowfullscreen" value="true" /><embed type="application/x-shockwave-flash" width="640" height="505" src="http://www.youtube.com/v/snEyl-hLhVo&amp;hl=en&amp;fs=1" allowscriptaccess="always" allowfullscreen="true"></embed></object>

## What will I need?

You will need voipforvw, that you can check out from sourceforge.net:

    svn co https://voipforvw.svn.sourceforge.net/svnroot/voipforvw voipforvw

Next, you will need [boost](http://www.boost.org/),
[curl](http://curl.haxx.se/download.html) and
[pjsip](http://www.pjsip.org/download.htm). The versions that I was using are
1.39, 7.19.4 and 1.0.2 respectively. For PjSIP, 1.x series will currently not
work due to API changes.

For compiling on Windows, you will also need the Platform SDK and DirectX SDK.
Voipforvw uses [CMake](http://www.cmake.org/cmake/resources/software.html) for
its configuration too.

### Step 1. Compile dependencies

Set up the platform SDK and DirectX SDK folders in Visual Studio.

Compile boost with bjam. To build bjam first, just run build.bat, which
creates bjam.exe; use that in the boost directory with the following
commandline:

{%highlight bash%}
$ bjam --toolset=msvc link=static runtime-link=static threading=multi --with-thread stage
{%endhighlight%}

To compile cURL, open the solution file, change the build type to Release and
set the Runtime Library to Multithreaded (`/MT`). These changes are necessary,
because we will be using multiple boost state machines from a multithreaded
environment. Build only libcurl.

PjSIP will need you to move the file `pjlib/include/pj/config_site_sample.h`
into `config_site.h` No changes are necessary to this file, the defaults will
work fine.

Open  pjproject-vs8.sln (and convert it as needed) set it to Release and build
the solution.

### Step 2: Configure and build voipforvw

Next, go to the voipforvw trunk and edit CMakeLists.txt You will need to
change the paths for `PJDIR`, `CURLDIR` and `BOOSTDIR`. Don't use backslashes,
replace them with forward slashes. The `CURLLIBS` variable needs to be changed
from curllib.lib to libcurl.lib.

Open up CMake-gui and set the source directory to the voipforvw trunk. The
output directory can be the same. Hit Configure (it will probably complain
about backwards compatibility, but just ignore it and hit Configure again). If
configuring goes well, there will be no red lines in the config window. Hit
Generate, which will ask for the format of the solution file, choose the Visual
Studio version you have and click OK. This creates the main solution file.

After opening this solution, there are three things you will need to check:
first, set the build type to Release. Next check that the Runtime Library is
Multithreaded (`/MT`) to match the other libraries and finally, `LIBCMT` will have
to be removed from the libraries (because it conflicts with `MSVCRT`). Now you
can build SLVoice.exe that you can use as a replacement for the vivox
solution.

### Debug

You probably noticed that we are building everything in Release mode. That's
fine as long as it works, but sometimes you will want to debug (hopefully less
often). In this case you will need to change the Runtime Library to `/MTd` for
PjSIP and voipforvw. Then CMakeLists.txt needs to be updated to reflect this
by setting

`SET (PJTARGET i386-win32-vc8-debug)`

Where you remove the `LIBCMT` library, now you will have to do the same thing
for both `LIBCMT` and `LIBCMTD` and you can build SLVoice.exe in debug mode.

If you only need logging to see what's going on inside, you can change the
code:

{%highlight c%}
// CHANGE FILE: main.h:37
#define LOG
#ifdef LOG
extern FILE *logfp;
#define VFVW_LOGINIT()     logfp = fopen("test.log", "a")
#define VFVW_LOG(fmt, ...) fprintf(logfp, "[VFVW] %s:%d - ", __FILE__,__LINE__); \
                           fprintf(logfp, fmt, ## __VA_ARGS__); \
                           fprintf(logfp, "\r\n"); \
                           fflush(logfp)
#else
#define VFVW_LOGINIT()     void()
#define VFVW_LOG(fmt, ...) void()
#endif
{%endhighlight%}

{%highlight cpp %}
//CHANGE FILE main.cpp:15
#ifdef LOG
FILE *logfp = NULL;
#endif
{%endhighlight%}

