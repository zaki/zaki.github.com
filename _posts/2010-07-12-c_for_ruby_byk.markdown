---
author: zaki
date: '2010-07-12 17:34:46'
layout: post
type: code
slug: c_for_ruby_byk
status: publish
title: C for Ruby
wordpress_id: '322'
comments: true
tags:
  - Ruby
---

Last weekend, I did an informal mini-conference on C and Ruby with a few
friends.

## Introduction to C

We started with an introduction to C as some of my friends didn't know it at
all. Of course pointers in C are a little difficult to wrap heads around at
first, but hopefully the things we covered will get them interested enough to
try and follow up on them. One particular example that started many questions
was something along these lines:

{%highlight c%}
void change(int** i)
{
  *i = (int*)malloc(sizeof(int));
  **i = 123;
  // what happens if we do
  // free(*i);
  // here
}

int main()
{
  int *ptr = NULL;
  change(&ptr);
  printf("%d", *ptr);
  // we should use free(ptr); here (if main() wasn't exiting anyway)
}
{%endhighlight%}

Especially the part about free-ing before accessing the variable: what happens
if we access the already free-d memory. On one machine we tried it, the number
was still 123, which showed very well how much we cannot trust the compiler/OS
to actually zero out the memory immediately when it is released and how easily
this can lead to bugs that work sometimes and fail sometimes. Next we talked
about the connections between C arrays and pointers.

{%highlight c%}
int a[10];
int* b = a;

// an array is in fact a pointer to the head
a[0] = 1;
*a = 1; // this is the same as the above
a[1] = 1;
*(a+1) = 1;
// the C compiler knows how long an int is
// so the + 1 will point to the first element
// instead of just plus one byte
{%endhighlight%}

Next up using pointers/arrays, together we implemented a very easy function to
check whether a string was a palindrom:

{%highlight c%}
int test(char* str)
{
  char *lo = str + 0; // lo is set to the first character
  char *hi = str + strlen(str) - 1; // hi is set to the last character
  while (lo < hi)
  {
    if ((*lo) != (*hi))
      return 0; // the chars don't match
    lo++;
    hi--;
    // move lo to the right, hi to the left
  }
  return 1; // every char matched, it is a palindrom
}
{%endhighlight%}

## Understanding Array#reverse

After we discussed the very basics of C, finally we started diving into the
source code of Ruby (latest trunk from github). I wanted to keep it simple, so
to be connected to the previous discussion, I left out the basics of _VALUE_
and the first code we looked at was _Array#reverse_ and _Array#reverse!_ These
functions were fairly easy to understand as  there is no complicated resizing
or in _String#reverse_'s case, complicated encodings to deal with. In fact,
before looking at the code, we discussed how it could be implemented - and to
no surprise the actual implementation was very similar.

{%highlight c%}
VALUE rb_ary_reverse(VALUE ary)
{
  VALUE *p1, *p2;
  VALUE tmp;

  rb_ary_modify(ary);
  if (RARRAY_LEN(ary) > 1)
  {
    p1 = RARRAY_PTR(ary); // get pointer to first item
    p2 = p1 + RARRAY_LEN(ary) - 1; // get pointer to last item

    while (p1 < p2) // imaging p1 as lo and p2 as hi
    {
      tmp = *p1; // swap *p1 with *p2
      *p1++ = *p2;
      *p2-- = tmp;
    }
  }
  return ary;
}
{%endhighlight%}

One thing to remember here is the _*p1++_ notation. To those unfamiliar with
C, _var++_ means that first var is "returned" or in other words, var is used
and only then is it incremented. (the ++ is after the variable). On the other
hand, _++var_ would first increment var and then use this incremented value.
So it is crucial to use _*p1++_ here instead of _*++p1_. Now in ruby, there
are two versions of reverse, one with a bang and one without. From the code in
the above helper function, we can assume that it is for the reverse! type, but
it would not be very useful for _Array#reverse_ as it would change the array
in place. Instead, we saw in the sources that _Array#reverse_ actually
internally duplicates the array before doing the in-place reverse on the
duplicated array. This pointed out that _Array#reverse_ must be slower than
_Array#reverse!_ and of course it must also use more memory. Even though I
didn't go into too much detail about Ruby's GC (which I'm not all that
familiar with anyway beyond knowing it is a mark-and-sweep type collector,
just like the CLR), I mentioned that the GC will be started when a memory
allocation does not have enough memory. So in the end, not only is it slower
and uses more memory, but there is a chance that _Array#reverse_ triggers
garbage collection which will make it even slower and use even more memory.
(As an aside, realizations like these are the reasons I wanted to talk about
the internals, as I believe understanding even just a little about how things
work make us better programmers in the long term)

To understand the
performance implications, we tried implementing _Array#reverse!_ in ruby which
we benchmarked against the native _reverse!_:

{%highlight ruby%}
require 'rubygems'
require 'benchmark'

class Array
  def rb_reverse!
    lo,hi=0,self.length-1
    while lo < hi
      tmp = self[lo]
      self[lo] = self[hi]
      self[hi] = tmp
      lo += 1; hi -= 1
    end
  end
end

arr = (1..10_000_000).to_a
Benchmark.bm do |x|
  x.report { arr.reverse! }
  x.report { arr.reverse }
  x.report { arr.rb_reverse! }
end

{%endhighlight%}

The result was absolutely clear:
        user     system      total        real
    0.030000   0.000000   0.030000 (  0.037438)
    0.100000   0.040000   0.140000 (  0.142042)
    6.290000   0.020000   6.310000 (  6.307183)

## Extending Fixnum#succ

Next we looked at an (easy-looking) item from
the Ruby ToDo file:
* optional stepsize argument for succ()

By default `Fixnum#succ` doesn't take any arguments and just increments
the variable by 1. So let's take a look at what it's doing internally: 

{%highlight c%}
static VALUE fix_succ(VALUE num)
{
  long i = FIX2LONG(num) + 1;
  return LONG2NUM(i);
}
{%endhighlight%}

No surprise here, it takes no arguments (besides a "this") and increments by
1. To fix this, I explained about variable argument list, so we can take one
or zero arguments and naively implemented a first version of _succ_:

{%highlight c%}
static VALUE fix_succ(int argc, VALUE* argv, VALUE num)
{
  VALUE step; // this will hold the value to increment by
  if (argc == 0) // in case we don't receive any arguments, use 1 as step
  {
    step = INT2FIX(1); // just like above
  }
  else
  {
    if (argc == 1) // when there is one argument, use it as the increment
    {
      step = argv[0];
    }
    else // if there are more than 1 arguments, raise error
    {
      rb_raise(rb_eArgError, "wrong number of arguments");
    }
  }
  long i = FIX2LONG(num) + FIX2LONG(step); // add the numbers and return
  return LONG2NUM(i);
}
{%endhighlight%}

Of course this in itself does not work, which gave me a chance to briefly
mention how method lookup tables work. Then together we changed that table to
indicate that _succ_ might take any number of arguments with the entry:

{%highlight c%}
rb_define_method(rb_cFixnum, "succ", fix_succ, -1);
{%endhighlight%}

However that naive implementation has a lot of problems that then gave me an
opportunity to again mention other parts of ruby we should be aware of.

{%highlight ruby%}
# For starters, let's try the new succ method:
1.succ
> 2 # OK
1.succ 2
> 3 # OK
1.succ 1073741823
> 1073741824 # OK
1.succ 1073741824
> 73693943 # Hey, what's going on here

# other errors
1.succ 'a'
> 73021957 # whoa, what's this?
'a'.succ
> b # OK
'a'.succ 2
> in `succ': wrong number of arguments(1 for 0) (ArgumentError) #Ooops
{%endhighlight%}

Of course to understand what the problem is, we should know why  1073741824 is
special. As it turns out, _Fixnum's_ largest representable value is
1073741823, which means that 1073741824 causes an overflow. Even though C
_int/long_ can go twice as high (2^31), _Fixnum_ can hold up to 2^30 only. This
means that values that are higher than 1073741823 will be of type _Bignum_,
rather than _Fixnum_. So one thing we can do is to check if the argument is a
_Fixnum_. Only if it is a _Fixnum_, should we add the values together, but
when it's a _Bignum_, we can call into the regular + method, that will take
care of everything for us. In fact, we could have directly implemented
everything as an addition, but the point here is how to map C types to Ruby
types and how we can deal with the differences and the limitations of the
underlying language.

{%highlight c%}
static VALUE fix_succ(int argc, VALUE* argv, VALUE num)
{
  VALUE step;
  if (argc == 0)
  {
    step = INT2FIX(1);
  }
  else
  {
    if (argc == 1)
    {
      step = argv[0];
    }
    else
    {
      rb_raise(rb_eArgError, "wrong number of arguments");
    }
  }
  if (FIXNUM_P(step)) // We can check if step can fit in Fixnum
  {
    // yes, just add it together in a long
    // remember, that long can be twice as big as fixnum,
    // so no worries about overflow
    long i = FIX2LONG(num) + FIX2LONG(step);
    return LONG2NUM(i);
  }
  // If it didn't fit into Fixnum, so let's just add it naturally
  else
  {
    return rb_funcall(num, '+', 1, step);
  }
}
{%endhighlight%}

This finally works for numbers, but still has some problems. Also, _"'a'.succ
2" _will not work, but the code for _String#succ_ is very complicated because
of the different encodings. As all my friends (except Huan) had to deal with
emoji conversion at one point in their lives, it didn't take much to scare
them away from looking at those codes (or even try to implement _#succ_ - but
they are free to do so if they want to)

## Writing native extensions for Ruby

Finally I wanted to look at implementing Ruby extensions in C (and comparing
performance) but unfortunately the _mkmf_ lib was not found on my machine
(Later I realized that it was because RVM changed to an unusable ruby version
when I wasn't looking), so instead tried implementing binary search together
as an exercise. As a note, here's the final working version:

{%highlight ruby%}
def rbinsrc(ary, src)
  st,en = 0, ary.length-1
  while st <= en
    mid = st + (en - st) / 2 # avoid overflow in (st + en) / 2
    if (src < ary[mid])
      en = mid - 1
    elsif (src > ary[mid])
      st = mid + 1
    else
      return mid
    end
    return -1 # not found
  end
end
{%endhighlight%}

In fact, I created an extension to do the same thing and benchmarked the Ruby
version against the C version

{%highlight c%}
#include "ruby.h"
VALUE Convrt = Qnil;
void Init_convrt();
VALUE method_binsrc(VALUE self, VALUE arr, VALUE src);

void Init_convrt()
{
  Convrt = rb_define_module("Convrt");
  rb_define_method(Convrt, "binsrc", method_binsrc, 2);
}

VALUE method_binsrc(VALUE self, VALUE arr, VALUE src)
{
  int st, en, md; int s;
  if (TYPE(arr) != T_ARRAY || TYPE(src) != T_FIXNUM)
    rb_raise(rb_eTypeError, "Invalid arguments");

  st = 0;
  en = RARRAY(arr)->len-1;
  s = FIX2INT(src);
  while (st <= en)
  {
    md = st + (en-st)/2;
    if (s < FIX2INT(RARRAY(arr)->ptr[md]))
      en=md-1;
    else if (FIX2INT(RARRAY(arr)->ptr[md]) < s)
      st = md+1;
    else
      return (INT2FIX(md));
  }
  return (INT2FIX(-1));
}
{%endhighlight%}


{%highlight ruby%}
require 'benchmark'
require 'convrt'
include Convrt

def rbinsrc(arr,src)
  st,en = 0,arr.length-1
  while (st <= en)
    md = st + (en-st)/2
    if (src < arr[md])
      en = md - 1
    elsif (arr[md] < src)
      st = md + 1
    else
      return md
    end
  end
  return -1
end

N = ARGV[0].to_i a = (1..100000).to_a

Benchmark.bm do |x|
  x.report { 1.upto(N) { binsrc(a,a.length-1)} }
  x.report { 1.upto(N) {rbinsrc(a,a.length-1)} }
end
{%endhighlight%}

And the result again is very clear, which shows how a C extension for Ruby can
improve performance in bottleneck situations but because it complicates things
a lot, how it should be only used with  good judgment.

    $ ruby test_02.rb 10000
          user     system      total        real
      0.010000   0.000000   0.010000 (  0.016662)
      0.430000   0.020000   0.450000 (  0.449629)

