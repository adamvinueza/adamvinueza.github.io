---
layout: post
title:  "My God It's Full of Nodes"
date:   2019-04-30 11:23:59 -0600
categories: data-structures
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
features it has: call this the "swiss-army knife" approach to design. Swiss-army
knives are useful because of the number of its attachments: the more things
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
Making these decisions is only possible when you know how these data structures
the way a mechanical engineer understands how watches are like windmills.

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
        node = Node(value)
        node.next = self
        self = node

    def print(self):
        print(self.value, end=' ')
        if self.next is not None:
            self.print(self.next)
```
Let's now transform the Node class to one supporting binary trees. We'll start
by replacing `next` with `left` and `right`, and changing the constructor:
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
            self.print(self.next)
```
The `Node` class is broken now: `Node` instances don't have a `next` property,
but `left` and `right` properties instead, so we need to fix the insert and
print methods.

Inserting into a binary search tree requires finding the right place to put the
value. The rule is simple: if the value is less than the root node's value,
insert it into the left subtree, and if the value is greater than the root
node's value, insert it into the right subtree. (Do nothing if it's equal to the
root node's value, because the value is already in the tree!) Of course, if the
root node is null (`None` in Python), make a `Node` and set it to the root.

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
        if self.left:
            self.left.print()
        print(self.value, end=' ')
        if self.right:
            self.right.print()
```
Doing this is akin to changing the shape of the gears so that they will match
the requirements of the machine you're trying to build. The basic functionality
is _exactly the same_: nodes merely hold values and connect to other nodes.

To see how linked lists relate to binary search trees, merely insert values into
a binary-search-tree-enabled `Node` in ascending order. Only the right side of
the tree will be populated, so the `right` property of the binary-search-enabled
`Node` is effectively the `next` property of the linked-list `Node`.

If you want to create a graph instead of a binary search tree or linked list,
how would you make the transformation? Well, you can have an arbitrary number of
connections to other nodes, so our `Node` class would have a property holding a
_list_ of connections instead of a `next` or `left` or `right` property.
Inserting a node would be a matter of connecting it to a known node, so
precisely how the insertion would work depends on what rules govern the
relationships between nodes--i.e., on what kind of graph it is. Issues like this
are rather like asking questions about how large a gear must be, how many teeth
it must have, and what shape the teeth must be, in order for the mechanism to
work the way it is supposed to work.

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
