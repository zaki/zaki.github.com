---
author: zaki
date: '2010-07-14 16:50:40'
layout: post
type: code
slug: referrer-spam-in-statpress
status: publish
title: Referrer spam in statpress
wordpress_id: '339'
tags:
  - wordpress
---

Today I got really fed up with statpress referrer spam, so I decided to
implement a dead simple referrer check function into statpress. The function
works very similar to the current IP banning, so it wasn't very difficult to
get it to work. If I get angry again, I might create a new statpress
Option/Page to make the word banning easier or even make it non-destructive by
only marking the referrer as spam. Of course other than the proactive banning,
one might want to sanitize the referrrers in the statpress table as well. For
me, I just cleared all the referrers with bad words in them, but didn't bother
to entirely remove the entries as I wanted to keep count of the actual access
count.

Here are my simple changes to statpress.php:

{%highlight php%}
// A function to check whether a referrer should be
// omitted due to a manual ban
// To add/remove referrer bans, edit
// wp-content/plugins/statpress/def/banreferrers.dat
function iriCheckBanReferrer($arg)
{
  $lines = file(ABSPATH.'wp-content/plugins/'.dirname(plugin_basename(__FILE__)). '/def/banreferrers.dat');
  foreach($lines as $line_num => $banref)
  {
    if(strpos($arg,rtrim($banref,"n"))===FALSE)
      continue;
    return true; // banned
  }
  return false;
}

// In function iriStatAppend()
$referrer = (isset($_SERVER['HTTP_REFERER']) ? htmlentities($_SERVER['HTTP_REFERER']) : '');
// BANNED REFERRERS
if (iriCheckBanReferrer($referrer)) $referrer = '';
// END BANNED REFERRERS
{% endhighlight %}

Then all I needed to do is create a banreferrers.dat file in the
plugins/statpress/def directory containing one bad word/domain per line and
now hopefully a lot less referrer spam will appear in the statpress overview.
For now this will suffice for me.

