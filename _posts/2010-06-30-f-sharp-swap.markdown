---
author: zaki
date: '2010-06-30 02:20:46'
layout: post
type: code
slug: f-sharp-swap
status: publish
title: F# Swap
wordpress_id: '217'
tags:
  - f#
---

Reading about functional programming, I got curious the other day: one of the
canonical examples is swapping the elements in a tuple but what exactly
happens internally? After all, whatever language is used, eventually it will
be translated to native code.

F# is very easy to work with in this sense, because it will be compiled to IL
first, so if I only run ILdasm on it, an equivalent C# code could be created.

{%highlight ocaml%}
let a(x,y) = (y,x)
{%endhighlight%}

This innocent short code will generate the following IL code

{%highlight text%}
.method public static class System.Tuple`2<!!b,!!a> a<a,b>(!!a x,!!b y) cil managed
{
  // Code size 9 (0x9)
  .maxstack 4
  IL_0000: nop
  IL_0001: ldarg.1
  IL_0002: ldarg.0
  IL_0003: newobj instance void class System.Tuple`2<!!b,!!a>::.ctor(!0, !1)
  IL_0008: ret
}
{%endhighlight%}

Aha! So we are not fiddling around with temporary variables (which wouldn't
work anyway because variables are immutable by default in F#), instead the
simplest of things happens here: a new variable is constructed with the
parameters swapped and then returned.

In C#, Swap could be written along these lines

{%highlight csharp%}
public static Tuple<T2, T1> Swap<T1, T2>(T1 a0, T2 a1)
{
  return (new Tuple<T2, T1>(a1, a0));
}
{%endhighlight%}

In fact the C# code generates an almost identical IL code (although honestly
I'm not sure what the final branch is supposed to be, as it doesn't do
anything apparently useful)

{%highlight text%}
.method public hidebysig static class System.Tuple`2<!!T2,!!T1> Swap<T1,T2>(!!T1 a0, !!T2 a1) cil managed
{
  // Code size 13 (0xd)
  .maxstack 3
  .locals init ([0] class System.Tuple`2<!!T2,!!T1> CS$1$0000)
  IL_0000: nop
  IL_0001: ldarg.1
  IL_0002: ldarg.0
  IL_0003: newobj instance void class System.Tuple`2<!!T2,!!T1>::.ctor(!0, !1)
  IL_0008: stloc.0
  IL_0009: br.s IL_000b
  IL_000b: ldloc.0
  IL_000c: ret
}
{%endhighlight%}

