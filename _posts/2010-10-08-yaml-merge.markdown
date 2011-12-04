---
author: zaki
date: '2010-10-08 13:07:58'
layout: post
type: code
slug: yaml-merge
status: publish
title: Yaml !!merge
wordpress_id: '417'
tags:
  - ruby
  - yaml
---

There are two interesting constructs I came across in the database.yml in the
[typo](http://github.com/fdv/typo) source. One of them is for creating and
using a named block, but the other one was a lot more interesting technique to
grab parts of the Yaml tree and merge it into another part of the tree, which
works in combination with the named blocks to result in a DRY configuration
file like this:

{%highlight yaml%}
  login: &login
    adapter: mysql
    host: localhost
    username: root
    password:

  development:
    database: typo_dev
    <<: *login

  test:
    database: typo_tests
    <<: *login

  production:
    database: typo
    <<: *login
{%endhighlight%}

OK, so the login: &login part creates a named block and *login simply uses
that block. This is not incredibly difficult so far, but what on earth are
those "<<:" ? Because I haven't seen them before, for a moment I was wondering
if this is some sort of metaprogramming magic, where the << method/operator
will somehow get called and execute with the block given to it. To find out
how it works, I went to read the YAML 1.2
[specification](http://www.yaml.org/spec/1.2/spec.html), but interestingly
this syntax wasn't anywhere in the main doc. As it turns out, << is actually a
shorthand for a tag, namely **!!merge**, and is mentioned in a separate
document named _[Language-Independent Types for YAML™](http://yaml.org/type) _

There are a few other tags mentioned here as well. In ruby, collection types
seem to create a custom _YAML::PrivateType_ (or _Syck::PrivateType_ in 1.9)
object and shove the data in its _@value_. I don't really see much use for
these types in ruby now.

Scalar types on the other hand are better integrated, but tags like
**!!binary**, **!!value** or **!!yaml **still create the _PrivateType_
objects, so I guess they are not  supported - and these are all only
recommendations according to the document.

