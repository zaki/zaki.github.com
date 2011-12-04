---
author: zaki
date: '2010-07-06 00:50:53'
layout: post
type: code
slug: f-discriminated-unions
status: publish
title: F# Discriminated Unions
wordpress_id: '307'
tags:
  - f#
---

In F# discriminated unions are providing a base for the powerful pattern
matching. Let's examine what happens behind the scene when someone enters the
following innocent-looking code:

{%highlight ocaml%}
type Test = | Option1 of float | Option2 of int
{%endhighlight%}

If we take the generated assembly apart, this simple code generates a
relatively large amount of code. First of all, a class (type) named _Test _is
going to be created. What is more surprising is that five nested classes will
also be defined. Of this five, two seems to be crucial as we'll see while the
others seem to be more about administration.

Talking about administration, the resulting type implements a fair amount of
interfaces automatically.

_IEquatable _adds three _Equals _methods: one with the _Test _type, but also
two with object type (one of which allows a second _IEqualityComparer
_parameter). Similarly _IComparable _and _IComparable<Test>_ are implemented.
In addition _IStructuralEquatable_ and _IStructuralComparable _are also
implemented. I won't talk about these methods.

What is more important is that one nested class that is inherited from _Test
_will be created for each option, named _Option1 _and _Option2_. Both of these
classes will contain a property named _Item _and not surprisingly Item will be
_float64 _in _Option1 _and _int32 _in _Option2_.

_Test _will also contain two more boolean properties: _IsOption1 _and
_IsOption2 _which return whether this refers to an instance of the respective
type.

Finally _Test _provides two factory constructors named _NewOption1 _and
_NewOption2 _which create either of the nested option instances. Its
constructor in fact is not public (and doesn't do anything except create the
useless parent _Test _instance).

An implementation in C# therefore (removing much of the automatically
generated interface implementations and helper methods) would look like this.
The generated IL is very similar to that of the original F# code.

{%highlight csharp%}
public class Test
{
  public class Option1 : Test
  {
    public Option1(double v) { item = v; }
    readonly double item;
    double Item { get { return item; } }
  }

  public class Option2 : Test
  {
    public Option2(int v) { item = v; }
    readonly int item;
    int Item { get { return item; } }
  }

  public sealed class Tags
  {
    public const int Option1 = 0;
    public const int Option2 = 1;
  }
  Test() {}

  public static Test NewObject1(float v) { return new Option1(v); }
  public static Test NewObject2(int v) { return new Option2(v); }

  public bool IsOption1 { get { return (this is Option1); } }
  public bool IsOption2 { get { return (this is Option2); } }
  public int Tag { get { return ((this is Option1) ? Tags::Option1 : Tags::Option2); } }
}
{%endhighlight%}

There are two further nested classes named _Option1@DebugTypeProxy_ and
_Option2@DebugTypeProxy_, but I'm not really sure what their functionality is.
Also, there is that _Tags _nested class with its const definitions and the
_Tag _property in _Test _whose role is also not completely clear from this IL
code. It can be related to enums or maybe to do with performance

After comparing the two implementations, it is very easy to appreciate how -
in probably Don Syme's favorite word - succinct F# really is (and that's even
before diving into heavyweight functional programming.

