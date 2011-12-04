---
author: zaki
date: '2009-11-11 18:09:23'
layout: post
type: code
slug: plugins-for-rei
status: publish
title: Plugins for Rei
wordpress_id: '140'
tags:
  - 3Di
  - Opensim
  - Rei
---

## Plugins

In “Rei”, there is a curious little green “health” bar above the avatar’s
head. That bar is not something that is built in to the viewer, rather, a
simple plugin provides the functions that are necessary to have a very
rudimentary health bar. Of course this TestPlugin is only to show what sort of
things are possible with “Rei”.

<ul class="media-grid">
  <li>
    <a>
      <img src="/images/3di-openviewer/TestPlugin.jpg" width="100" />
      <h6 class="portfolio-label">HEALTH BAR</h6>
    </a>
  </li>
  <li>
    <a>
      <img src="/images/3di-openviewer/TestPlugin_Damage.jpg" width="100" />
      <h6 class="portfolio-label">DAMAGE</h6>
    </a>
  </li>
  <li>
    <a>
      <img src="/images/3di-openviewer/TestPlugin_Heal.jpg" width="100" />
      <h6 class="portfolio-label">HEAL</h6>
    </a>
  </li>
  <li>
    <a>
      <img src="/images/3di-openviewer/TestPlugin_Dead.jpg" width="100" />
      <h6 class="portfolio-label">DEAD</h6>
    </a>
  </li>
</ul>


![](/images/3di-openviewer/TestPlugin_JS.jpg)

## What do plugins do?

Plugins are intended for two main purposes. First, to communicate - through
javascript - with the 2D web, and second, to interact with objects within the
3d scene. Of course a plugin is a .NET assembly, so it can pretty much do
anything, but the main functions it is intended for are the above two. For
instance TestPlugin provides two javascript functions: one to inflict damage
and one to heal, basically allowing a regular 2d web application, for instance
an SNS site or an online game, to change the health meter of the avatar within
the 3d world according its own rules. Responding to these javascript functions
then, the plugin interacts with the 3d world by rendering its own little
health bar above the user’s head.

## How to write plugins?

To make things as simple as possible, “Rei” uses the same method for
developing plugins as OpenSim. Essentially one writes an addin for the
_/OpenViewer/Managers_ extension by implementing the IManagerPlugin interface
and then creating and embedding a plugin manifest to the assembly.

### IManagerPlugin

All plugins need to implement IManagerPlugin, which means implementing a few
methods:

{% highlight csharp %}
void Initialise(Viewer viewer);
{% endhighlight %}


-** Initialise **is the method that will be called right after constructing the plugin class. This will be only called once for the life of the plugin and is used to create objects that will persist through the entire lifetime such as static resources (textures, models), configurations.  
{% highlight csharp %}
void Initialize();
{% endhighlight %}


- **Initialize** (note the **z** in the name) will be called when “Rei” is getting ready to enter a region. This happens on the first startup as well as after logout or before entering a new region after a teleport. This is to make sure that unnecessary scene objects do not make it over to a new environment, therefore Initialize would create objects that persist only through the lifetime of one region, such as dynamic resources (textures, meshes) or dynamic information (region data, prim data)  
{% highlight csharp  %}
void Update(uint frame);
{% endhighlight %}


-** Update** is called every frame. This is the place to calculate the next state of the plugin, including objects in the 3d scene. This is the only place where it is allowed to change properties of any object that is within the scenegraph. By using the frame parameter, it is possible to count or skip frames.  
{% highlight  csharp %}
void Draw();
{% endhighlight %}


- **Draw** is also called every frame and it is where drawing directly to the 3d canvas is possible. Generally it is recommended to use proper scenenodes that draw automatically, instead of drawing directly.  
{% highlight csharp %}
void Cleanup();
{% endhighlight %}


- Just like Initialize is called before “Rei” is ready to log in to a new region, **Cleanup** is called every time a region is no longer needed, such as when logging out or when leaving a region by teleport. Cleanup is responsible of releasing resources that were allocated by Initialize (but not Initialise!)  
{% highlight csharp %}
RefController Reference { get; set; }
{% endhighlight %}


- This property is the entry point to "Rei"s core. Through this object it is possible to call into the public api of the viewer or to access javascript. **Reference** must always be initialized from the viewer parameter passed in to Initialise() 

### Communication with Javascript

##### Plugin to Javascript: Dispatch

A plugin can call Reference.Viewer.Adapter.Dispatch(string action, string
message) when it wants to send a message to javascript. If a handler was added
to the javascript library for the particular action, this dispatch will be
processed by passing the message to the handler function. For future
compatibility it is recommended although not required that the message be
passed in json format.

##### Javascript to Plugin: Callback

The Adapter supplies a Callback delegate and a registry to handle calls from
Javascript. A plugin can register a callback method that it is interested in
receiving by calling

{% highlight csharp %}
Reference.Viewer.Adapter.RegisterCallback
{% endhighlight %}


. It must pass the name of the method it wants to listen to and the method to
call when the event arrives. The way a Callback works is very much like
delegates work in the .NET framework.

From the javascript side, one can use

{% highlight javascript %}
ctrl.Callback("method", parameters)
{% endhighlight %}


and all plugins that are listening to “method” will receive a callback to
their registered functions. For an example of Callbacks, you can see the
DoDamage and DoHeal from test.js and TestPlugin.cs

##### Plugin to Plugin: Message

It is also possible to send messages from one plugin to another, even without
being aware of the other plugin. The mechanism is very similar to Callbacks: a
plugin registers a message handler by calling

{% highlight csharp %}
Reference.Viewer.Adapter.RegisterMessage
{% endhighlight %}


. The first parameter must be the name of the message to listen to and the
second one is a message handler delegate.

A plugin can then raise a Message by calling

{% highlight csharp %}
Rerence.Viewer.Adapter.SendMessage("message", parameters)
{% endhighlight %}


and all plugins that subscribed to the message being sent will receive it.

* * *

<div class="alert-message block-message info">
The contents of this post also appear on the github wiki for 3Di Viewer "Rei". For
more up-to-date information, refer to
<a href="http://wiki.github.com/3di/3di-viewer-rei/plugin-development" class="btn">REI PLUGINS WIKI</a>
</div>
