---
author: zaki
date: '2010-06-30 22:00:32'
layout: post
type: code
slug: f-currying
status: publish
title: F# Currying
wordpress_id: '258'
comments: true
tags:
  - F#
---

Let's look at how currying works behind the scenes. It is not very surprising
that this code

{%highlight ocaml%}
let adder x y = x + y
let partial x = adder 2 + y
{%endhighlight%}

is compiled to

{%highlight text%}

.method public static int32 adder(int32 x, int32 y) cil managed 
{
  .custom instance void Microsoft.FSharp.Core.CompilationArgumentCountsAttribute::.ctor(int32[])
    = (01 00 02 00 00 00 01 00 00 00 01 00 00 00 00 00 ) // Code size 5 (0x5)
  .maxstack 4
  IL_0000: nop
  IL_0001: ldarg.0
  IL_0002: ldarg.1
  IL_0003: add
  IL_0004: ret
} // end of method Program::adder

.method public static int32 partial(int32 x) cil managed
{
  // Code size 9 (0x9)
  .maxstack 4
    IL_0000: nop
    IL_0001: ldc.i4.2
    IL_0002: ldarg.0
    IL_0003: call int32 Program::adder(int32,int32)
    IL_0008: ret
} // end of method Program::partial
{%endhighlight%}

which comes down to the following common C# code

{%highlight csharp%}

public class Test { public static int adder(int x, int y) { return (x + y); }
public static int partial(int x) { return (adder(x, 2)); } }
{%endhighlight%}

and it does generate a similar IL code:

{%highlight text%}
.method public hidebysig static int32 adder(int32 x,int32 y) cil managed
{
  // Code size 9 (0x9)
  .maxstack 2
  .locals init ([0] int32 CS$1$0000)
  IL_0000: nop
  IL_0001: ldarg.0
  IL_0002: ldarg.1
  IL_0003: add
  IL_0004: stloc.0
  IL_0005: br.s IL_0007
  IL_0007: ldloc.0
  IL_0008: ret
} // end of method Test::adder .method

public hidebysig static int32 partial(int32 x) cil managed
{
  // Code size 13 (0xd)
  .maxstack 2
  .locals init ([0] int32 CS$1$0000)
  IL_0000: nop
  IL_0001: ldarg.0
  IL_0002: ldc.i4.2
  IL_0003: call int32 CSharpTest.Test::adder(int32,int32)
  IL_0008: stloc.0
  IL_0009: br.s IL_000b
  IL_000b: ldloc.0
  IL_000c: ret
} // end of method Test::partial
{%endhighlight%}

