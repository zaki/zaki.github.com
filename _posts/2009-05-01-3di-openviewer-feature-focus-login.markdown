---
author: zaki
date: '2009-05-01 16:04:03'
layout: post
type: blog
slug: 3di-openviewer-feature-focus-login
status: publish
title: '3Di OpenViewer Feature Focus: Login'
tags:
  - 3Di
  - Opensim
  - Rei
---

3Di OpenViewer provides a number of ways to connect to a grid, let's look at
those in a little more detail.

### Manual

Manual login is the normal way, that everyone using
SecondLife is already used to. This mode will show
a login box right within the application and will allow you to specify the
login URI, the first and last name and password. You can set the default
values with additional params as well.

{% highlight html%}
  <param name="LoginMode" value="manual" /> <!-- Optional-->
  <param name="ServerURI" value="http://1.2.3.4:10001" />
  <param name="FirstName" value="first" />
  <param name="LastName" value="last" />
  <param name="Password" value="password" />
{% endhighlight %}

![3Di OpenViewer manual login](/images/3di-openviewer/manual_login.jpg)

### Click

Click login is great if you want to specify all the information for  the login
and not allow the user to change it in any way (for example dynamically
creating the login page based on already existing session information). In
this case, the user can only see an empty screen with no login box; when they
click on this screen, login starts automatically.

Of course in click mode, you will have to specify all the login information
like above and possibly provide explanation somewhere on your site.

{%highlight html%} <param name="LoginMode" value="click" /> {%endhighlight%}

![3Di OpenViewer click login](/images/3di-openviewer/hide_login.jpg)

### Hide

Users can also use a JavaScript function to log in. Sometimes, it is better
not to allow users to specify any information, but at the same time, login
information can dynamically change at any time. In this case, you can set the
login mode to hide, which will not show the login box, but also not allow
login from within the application. Rather, it only allows external JavaScript logins.

{%highlight html%} <param name="LoginMode" value="hide" /> {% endhighlight %}

To log in from JavaScript, use the following function:

{% highlight javascript %}
openviewerCtl.Login(firstname, lastname, password, server_uri, login_location);
{% endhighlight %}

For the JavaScript login method, it is also possible to specify the exact
location to log in to. The format is

{% highlight text %}
// " uri:regionname&x&y&z"
     uri:region1&128&128&25
{% endhighlight %}

