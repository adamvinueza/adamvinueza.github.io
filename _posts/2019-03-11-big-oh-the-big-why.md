---
layout: post
title:  "Big-O: The Big Why"
date:   2019-03-10 11:23:59 -0600
categories: algorithms toolkit
---
### Preface

I attend a weekly study group for budding software engineers, which aims to help
programmers prepare for technical interviews. Wednesday evenings they
collectively work through programming puzzles and study fundamental algorithms
and data structures. I've been fortunate throughout my career in that many
engineers have acted as mentors and resources for me; I go to these meetings as
a way of paying forward.

(Also, I've found that I _like_ mentoring; helping people learn is very
satisfying, and I learn a lot myself, about both engineering and non-engineering
stuff. You should try it!)

One of the first conversations I had with the leaders of that workshop was about
the real-world utility of the algorithms they were studying. The basic question
was: How much do working engineers actually use any of this stuff?

Some engineers spend their careers working on cutting-edge algorithms or on code
demanding great precision in performance analysis. For those engineers,
algorithm analysis is their bread and butter. But the vast majority of
applications being written and maintained today don't require either of those
things. Most of us work on those applications, so can't we just rely on the work
done by others to ensure the fundamental data structures are efficiently
implemented?

As it happens, we can: all the main programming languages have libraries for
doing pretty much anything one gets taught to implement in data-structures and
algorithms classes. For example:
  - Python dictionaries and sets are implemented as hash tables with linear
    probing.
  - The C++ std::map is implemented as a red-black tree.
  - Go's pseudo-generic list container is implemented as a doubly-linked list.

And so on.

That said, I've long thought that algorithm analysis--and, in particular, what's
known as Big-O performance analysis--should be an important component of every
engineer's education, although I struggled to articulate precisely why I thought
so. Luckily for me, about a month ago one of the workshop leaders invited me to
talk briefly about why and in what way it was important to understand Big-O.
This gave me an opportunity to think more carefully about it, and what follows
is an updated, expanded version of the talk I gave to that group.

### Big-O: The Big Why

Why is understanding Big-O concepts important? And just how useful is it in the
average software engineer's working day?

#### Big-O is a rough guide to performance

The main reason to study Big-O is that it's a critical part of understanding
_how programs perform_.

An algorithm with complexity of _O(n<sup>5</sup>)_ will (in the worst
case) involve over 3 million operations when _n_ = 20. This can be crippling to
an application _if this complexity and its source is not known when the
algorithm is being implemented_. (You should of course always know an
algorithm's _worst-case_ conditions, because your application may or may not be
likely to bring those conditions about!)

#### Big-O is a component in the competition between space and time ...

Another reason to study Big-O is to learn about the tradeoffs one can make
between space and time in the effort to optimize performance. Often one can cut
down the time a program runs by increasing the amount of memory it uses. Caching
is the most obvious example: by storing already-computed values in a cache that
can be rapidly searched, one can often significantly increase performance. Below
are two simple examples.

1. _Sorting integers via arrays_. The most efficient sorting algorithms have
   _O(n log n)_ complexity.  But if the items to be sorted are (non-negative)
   integers, one can speed this up to a complexity of _O(n)_, by taking
   advantage of a simple trick from hash tables: store the integer in the array
   whose index is the integer's value.  Because array lookups are in general
   _O(1)_, for _n_ items we can sort in _O(n)_ time.

2. _Memoizing_. The fibonacci function is easy to implement recursively: if _n >
   2_, `fib(n) = fib(n - 1) + fib(n - 2)`. But if that's how we define the
   function, calculating `fib(5)` requires calculating both `fib(4)` and
   `fib(3)`, and calculating `fib(4)` also requires calculating `fib(3)`. We can
   avoid calculating `fib(3)` twice by storing its value in a table and looking
   it up: if a table has a value for the number 3, just use it; otherwise, store
   the value of `fib(3)` with the key 3. Doing this replaces the complexity of
   calculating `fib(n)` with the complexity of looking up the key _n_ in a hash
   table (on average, _O(1)_).

The cost of these speedups, of course, is the _extra space_ required to store
the data, as well as whatever gets dragged along with the use of that space. For
example, if the space is a spinning hard disk, there is the additional cost from
seeking on the disk, as well as possible resource contention issues (e.g.,
having to wait because something else is accessing the disk). These costs have
to be weighed against the speed gains when assessing whether to use the
faster-but-heavier algorithm.

#### ... and maintainability

Another factor that should be considered heavily (but often isn't) is the
_maintainability_ of the code. Don't let trivial performance improvements to
override the need to keep your code readable, clear, and consistent. Sometimes
(though not always!) it's better for a program to be less efficient if the more
efficient alternatives make the code harder to understand.

#### Big-O can ground inquiries into when scale matters ...

If you know the Big-O complexity of an algorithm, you have the basic tools for
assessing at what point your programs begin to degrade in performance. If a
function performs at _O(1)_ regardless of input, you should be able to throw
essentially unlimited amounts of data at the function; but any algorithm whose
performance varies based on the input size allows you to estimate a point at
which you can expect performance degradation. If you're sorting a list with an
_O(n log n)_ algorithm, you can with some simple tests using varying input sizes
plot a graph which will tell you when you should expect the sorting function to
be unacceptably slow.

#### ... but use a profiler anyway

That said, you should probably never test performance with such rudimentary
information. Every programming language can be profiled with automated tools,
and you should take advantage of profilers to discover _exactly_ where the
critical parts of your code lie. Using Big-O analysis, you can make educated
guesses and take basic precautions, but to _accurately_ assess why a program
isn't performing as you expect, you _must_ use a profiler.

Lest you're tempted to disregard this advice and try optimizing in advance of
knowing the critical parts of your code, you don't have believe me. But you
probably should [believe Donald
Knuth](http://www.cs.sjsu.edu/~mak/CS185C/KnuthStructuredProgrammingGoTo.pdf):

>It is often a mistake to make a priori judgments about what parts of a program
>are really critical, since the universal experience of programmers who have
>been using measurement tools has been that their intuitive guesses fail.

#### Coda

Let me end on a less utilitarian note. Most things we truly value knowing aren't
things we _need_ to know. There's the necessary, and there's the _interesting_.
To quote [Randall Munroe](https://www.xkcd.com/1050/):

>The only things you HAVE to know are how to make enough of a living to stay alive
>and how to get your taxes done. All the fun parts of life are optional.

