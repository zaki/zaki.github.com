---
author: zaki
date: '2008-08-28 22:07:49'
layout: post
slug: getting-voice-chat-in-opensim
status: publish
type: blog
title: Voice chat in OpenSim
comments: true
tags:
  - OpenSim
  - VoIP
---

<div class="alert-message block-message info">
  The content of this post is outdated. To learn more about the new
  FreeSWITCH connector in OpenSim, go to<br />
  <a href="/2009/04/28/freeswitch-module-in-opensim/" class="btn primary">FreeSWITCH module in OpenSim</a>
</div>
<hr />

# Overview

If you want to set up voice chat for your grid, here is a rundown on what you
will need and how it works.

OpenSim currently uses the Asterisk PBX server through a dynamic, MySQL-based
configuration that is updated by a "frontend" python script. This frontend
is invoked by an SLVoice replacement when a user REGISTERs to
the SIP channel configured for the region.

For this, SLVoice requires two pieces of information:

* The address of the "frontend"
* The address of asterisk to register with

The region server must provide the asterisk address to the viewer, which in turns
will pass it down to SLVoice. Similarly, the address of the frontend is set in
OpenSim.ini and registration happens via the AsteriskVoice module in
Region.Environment.Modules.Avatar.Voice

However for private chats - by limitation of the SLViewer - SLVoice always receives
a hard-coded SIP URI that then needs to be changed to the real address. For flexibility
this URI is queried at runtime from an HTTP server (the address of which needs to be set
in slvoice.ini).

After SLVoice has registered through the frontend, it can contact the Asterisk service
and start the real voice chat.

# Installation

## Zaptel

**<a href="http://downloads.digium.com/pub/telephony/zaptel/">http://downloads.digium.com/pub/telephony/zaptel/</a>**

Zaptel is a kernel module that allows asterisk to use various telephony hardware, but for
our purposes it is only needed for the MeetMe conference application that depends on a hardware
timer. Because of this, the ztdummy kernel module is what we will need.

Zaptel requires that you have

_bison, bison-devel newt, newt-devel ncurses, ncurses-devel_

Installation is straightforward: _**./configure; make; make install **_

(Note: You need to be root to install zaptel)

After installation, make sure you run **_modprobe ztdummy; modprobe zaptel_**

## Asterisk

[**http://www.asterisk.org/**](http://www.asterisk.org/)

Before you start, please make sure the following are installed:

_mysql, mysql-connector-odbc unixODBC-devel libtool-ltdl-devel kernel-devel_

The latest versions of Asterisk don't include the sources for the codec iLBC
anymore. If you want those supported, please run contrib/scripts/get_ilbc_source.sh

It is advised that you run **_make menuselect_** and configure the modules
before you install asterisk. You will need `res_odbc` to interface properly with
MySQL. Also make sure that `ztdummy` and `app_meetme` are marked for install. Then
**_make; make install_**.

## OpenSim

Get OpenSim and apply [Patch 1689] ([http://opensimulator.org/mantis/view.php?
id=1689](http://opensimulator.org/mantis/view.php?id=1689)) to get private
chat working.

### Asterisk Frontend

The current frontend requires Python2.5 with mysql support
([http://sourceforge.net/projects/mysql-python](http://sourceforge.net/projects/mysql-python) if not included in the
distribution) The frontend can be found in **_share/python/asterisk/_**

# Configuration

## Asterisk

In MySQL, create a database and add an 'asterisk' user by
{% highlight sql %}
    CREATE DATABASE asterisk;
    GRANT ALL ON asterisk.* TO asterisk@<host> IDENTIFIED BY '<password>';
{% endhighlight %}

The frontend will create the schema on first start.

Set up ODBC:

**/etc/odbcinst.ini**
{% highlight ini %}
[MySQL]
  Description = MySQL For Asterisk
  Driver = /usr/lib/libmyodbc3.so
  Setup = /usr/lib/libodbcmyS.so
  FileUsage = 1
{% endhighlight %}

**/etc/odbc.ini**
{% highlight ini %}
  [asterisk]
    Driver = MySQL
    Server =<server address>
    Database = asterisk
    Port = 3306
    User = asterisk
    Password = *********
    Socket =
    Option = 3
    Stmt =
{% endhighlight %}

**/etc/asterisk/extconfig.conf**
{% highlight text %}
    sipusers => odbc,asterisk,ast_sipfriends
    sippeers => odbc,asterisk,ast_sipfriends
    extensions => odbc,asterisk,extensions_table
{% endhighlight %}

**/etc/asterisk/extensions.conf**
{% highlight ini %}
  [avatare]
    switch => Realtime/@
{% endhighlight %}

**/etc/asterisk/modules.conf**
{% highlight ini %}
  [modules]
    autoload => yes
    noload => pbx_gtkconsole.so
    load => res_musiconhold.so
    noload => chan_alsa.so
    preload => res_odbc.so
    preload => res_config_odbc.so
{% endhighlight %}

**/etc/asterisk/res_odbc.conf**
{% highlight ini %}
  [asterisk]
    enabled => yes
    dsn => asterisk
    username => asterisk
    password => *********
    pre_connect => yes
{% endhighlight %}

## Frontend

In asterisk-opensim.cfg, set

{% highlight ini %}
  [xmlrpc]
    baseurl = http://<frontendhost>:<frontendport>

  [mysql]
    server   = <mysqlhost>
    database = asterisk
    user     = asterisk
    password = ************
    debug    = true

  [mysql-templates]
    tables = create-table.sql
    user   = create-user.sql
    region = create-region.sql
{% endhighlight %}

## OpenSim

In OpenSim.ini, add the following section:

{% highlight ini %}
  [AsteriskVoice]
    enabled = true
    sip_domain = <asterisk address>
    conf_domain = <asterisk address>
    asterisk_frontend = http://<frontendhost>:<frontendport>/
    asterisk_password = **********
    asterisk_timeout = 3000
    asterisk_salt = paluempalum
{% endhighlight %}

## SLVoice

Create an HTML page that has a single line stating the SIP URI to be used for
private chats (same as `asterisk address`) Set this page's address in
slvoice.ini and connect to the region. Voice chat should be working now.

# What's next

  * There is an ongoing effort to refactor the frontend code. The resulting architecture will be much cleaner with fewer connections.
  * The new VoiceServer in progress already includes support for SIP proxies like OpenSER, the SLVoice replacement will be updated shortly to add proxy support as well.

