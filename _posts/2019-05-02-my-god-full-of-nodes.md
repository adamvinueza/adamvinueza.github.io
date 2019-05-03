---
layout: post
title:  "My God It's Full of Nodes"
date:   2019-05-02 11:23:59 -0600
categories: data-structures
description: "Some remarks on the difference between coding and engineering."
---
Lots of data structures make use of a simple data structure known as a _node_.
A node has two properties: a _value_--some kind of manipulatable content--and
one or more pointers to other nodes: 

<p style="text-align: center"><img src="/assets/img/nodes.png" alt="Nodes
diagram" /></p>

Nodes are literally _everywhere_ in computing--from mobile apps, to databases,
to operating systems. That's because they tend to be found inside the following
fundamental data structures:
- lists
- stacks
- queues
- trees
- hash tables
- graphs

This post is about _why_ they are so ubiquitous: what is it about nodes that
makes them so useful?

# Nodes as Building Blocks

What can you do with nodes?

On the surface, not much: you can give a node a value, or you can point it at
another node. But it's the ability of nodes to point at one another that gives them
their power: how they point to one another reveals the larger structure being
built, and it is knowing how to manipulate them--inserting them, iterating over them,
finding their values, removing them--that gives the programmer an ability to
think at a _higher level of abstraction_, beyond the code, to the design of the
program itself. Here are a few examples of the kinds of structures it's easy to
build with a basic understanding of nodes:

1. If you have a node named "root" with pointers named "left" and "right", where
   the left node must have a value less than the root node's value, and the
   right node must have a value greater than the root node's value, you can make
   a binary search tree.
2. If you have a node with pointers named "next" and "previous", you can make a
   doubly-linked list; and from a doubly-linked list you can make a stack or a
   queue.
3. If you have a node whose pointers can only point to nodes that ultimately
   don't point back to them, you can make a directed acyclic graph.

# Swiss Army Knives vs Gears

So far, we've been thinking about nodes at a fairly low level--about very basic
data structures. Let's pull ourselves back a bit to see the larger picture.

It's easy to think that the usefulness of a thing is evident in the number of
features it has: call this the "swiss-army knife" approach to design. A swiss-army
knife is useful because of the number of its attachments: the more things
attached to it, the more useful it is. This way of understanding usefulness has
its place, to be sure--swiss-army knives _are_ useful--but nodes show us a very
different approach to utility.

The nodes of binary trees differ from the nodes of linked lists, which differ
from those of tries and of acyclic directed graphs. But what they have in common
is that they connect to one another in ways that can make for powerful complex
structures.  Thus, nodes are less like swiss-army knives than they are like
_gears_: simple physical objects that work with other physical objects to do
everything from pumping water to unlocking doors to keeping time. And when
you're building software, it's vital to have simple, powerful tools available to
you for solving complex problems.

If you understand how nodes fit into larger data structures, you have a deeper
understanding of those data structures. Knowing that linked lists form a
degenerate case of binary search trees, and that both are instances of directed
acyclic graphs, gives you power over both the code you're writing _and the
problems you're solving_. For example, it may be useful to treat a data
structure in one context like a linked list and in another context like a DAG.
Making these decisions is only possible when you understand these data
structures the way a mechanical engineer understands how watches are like
windmills.

# Transformations

To see how this way of thinking works, let's transform a linked list into a
binary search tree. We start with a simple linked-list class (we'll use Python
for the examples, but we could just as easily have chosen C or PHP or
Javascript):
```python
class Node:
    __slots__ = [ 'next', 'value' ]

    def __init__(self, value):
        self.value = value
        self.next = None

    def insert(self, value):
        if self.next is None:
            self.next = Node(value)
        else:
            self.next.insert(value)

    def print(self):
        print(self.value, end=' ')
        if self.next is not None:
            self.next.print()
```
A key thing to notice here is that a linked list is literally being made out of
`Node` instances. A linked list simply _is_ one or more `Node`s linked together.
Another way of putting this is to say that a linked list is either null, or a
`Node` whose `next` value is a linked list. This recursive way of understanding
linked lists is important, because we'll be understanding binary search trees in
the same way.

Let's now transform the Node class to one supporting binary search trees. First,
let's be explicit about what a binary search tree is: a binary search tree is a
collection of nodes, where each node has a value and left and right subtrees,
where the subtrees of that node (referred to as the _root_ node) have the
following property: every value on the left subtree is less than the value of
the root, and every value on the right subtree is greater than the value of the
root.

This means that inserting a new value into a binary search tree, and traversing
it to print its values, is going to be more complicated than doing the same
thing with a linked list. So let's start very simply, by replacing `next` with
`left` and `right`, and changing the constructor:
```python
class Node:
    __slots__ = [ 'left', 'right', 'value' ]

    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    def insert(self, value):
        if self.next is None:
            self.next = Node(value)
        else:
            self.next.insert(value)

    def print(self):
        print(self.value, end=' ')
        if self.next is not None:
            self.next.print()
```
We should see that the `Node` class is broken now: `Node` instances don't have a
`next` property, but `left` and `right` properties instead, so we need to fix
the insert and print methods.

Let's remember now the recursive feature of linked lists, and apply it to binary
search trees. We'll say that a binary search tree is either null, or a `Node`
whose `left` and `right` values are binary search trees. This means that
whenever we're doing _anything_ with a binary search tree anywhere in the
tree, what we're dealing with _is itself a binary search tree_.

Our linked-list insert method is recursive in the same way. If you want to
insert a value into a list, which is a `Node`, check to see if we're at the end
of the list--i.e., if its `next` property is null: if it is, make a new `Node`
with that value and set it as the value of `next`. Otherwise, just call `insert`
on the value of `next`. This helps us understand how to change this method for
use with binary search trees.

What makes insertion into a binary search tree different is that, because there
are `left` and `right` nodes instead of a `next` node, there has to be a rule
determining when to choose which way to go (left or right). The rule is simple:
if the value is less than the current node's value, insert it into its left
subtree, and if the value is greater than the current node's value, insert it
into its right subtree. (Do nothing if it's equal to the current node's value,
because the value is already in the tree!) Of course, if the current node is
null (`None` in Python), make it into a new `Node`.

Printing the tree follows a similar principle. We'll print the values in
increasing order of value, which means calling the print method on the left
subtree, printing the root's value,  then calling the print method on the right
subtree.

The result gives us code like this:
```python
class Node:
    __slots__ = [ 'left', 'right', 'value' ]

    def insert(self, value):
        if value <  self.value:
            if self.left is None:
                self.left = Node(value)
            else:
                self.left.insert(value)
        elif value > self.value:
            if self.right is None:
                self.right = Node(value)
            else:
                self.right.insert(value)

    def print(self):
        if self.left is not None:
            self.left.print()
        print(self.value, end=' ')
        if self.right is not None:
            self.right.print()
```
Doing this is akin to changing the shape of the gears so that they will match
the requirements of the machine you're trying to build. The basic functionality
is _exactly the same_: nodes merely hold values and connect to other nodes.

If you want to create a graph instead of a binary search tree or linked list,
how would you make the transformation? Well, you can have an arbitrary number of
connections to other nodes, so our `Node` class would have a property holding a
_list_ of connections instead of a `next` or `left` or `right` property.
Inserting a node would be a matter of connecting it to a known node, so
precisely how the insertion would work depends on what rules govern the
relationships between nodes--i.e., on what kind of graph it is.

I hope it's clearer now why issues like this are similar to issues of how large
a gear must be, how many teeth it must have, and what shape the teeth must be,
in order for some mechanism to work the way it is supposed to work.

# Relative Importance of Data Structures and Code

There's a [famous footnote](https://lwn.net/Articles/193245/) by Linus Torvalds
about the importance of data structures in the engineering mindset:
>I will, in fact, claim that the difference between a bad programmer and a good
>one is whether he considers his code or his data structures more important. Bad
>programmers worry about the code. Good programmers worry about data structures
>and their relationships.

Programmers have been debating the merits of this remark, but as we're armed
with the example of nodes and the analogy to mechanical engineering with gears,
we're in a position to properly understand his point. Linus is saying that it's
not the code but the data structures that form the basic tools in the
programmer's toolbox. The good programmer must of course be concerned with the
code: it must be clean and correct. But she is no more concerned with that than
is the watchmaker with whether the underlying machinery is built properly: for
the programmer as it is for the watchmaker and the windmill designer, it is the
_design_--the way the disparate parts work together to form a single functional
object--that should be foremost in mind.

# Learning to Think Like an Engineer

One big problem beginning programmers face is that they simply _don't know how_
to think in this way. They're still trying to master basic programming concepts
and techniques, and can't yet see the larger patterns--or to the extent they
can, they don't know _how to make intelligent judgments about which designs to
choose or why_. This is completely normal. Beginning engineers and architects and designers
don't start out knowing how to think in this way, either. Everyone has to learn
the mechanics of their trade.

But so many programmers don't even realize that this way of thinking is actually
valuable, that it makes them better at what they do. Design, according to this
very common way of thinking, is a "soft" skill, akin to the ability to make
things look pretty. They don't recognize design as vital to the creative process
of making functional things.

The first, most critical step to take is to see that thinking this way _has
value_. If you can see that it has value, you will strive to master the concepts
and techniques, so you can stop focusing on the minutae of coding and start
focusing on the larger aspects of what makes good programs good: on what it
means for a program to be well or poorly designed. It'll be a gradual
process; there's no set of classes to take or books to read to master this
stuff, you mainly have to practice and think. (Though classes can be great, and
you should read as much as you can!) Eventually, though, you'll realize that
while thinking through a problem you almost unconsciously rejected a whole
category of data structures to settle on, say, priority queues, because you
could just _see_ that they helped with the particular kind of problem you had.
And that's what makes all that study and practice worthwhile.

