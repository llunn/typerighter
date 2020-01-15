.. design_architecture

============
Architecture
============

Typerighter's goal is to provide components that work together to make it easy
to work with the messy data of the real world.

There is a separation between a :code:`Type` and the structures that hold
data, like a :code:`dict` or :code:`View`, to emphasize a separation data and
functions, eg. functional programming principles. That functional principles
are implemented in Python's OO system is an ironic concession worthy of an
absurd name, like *Typerighter*.

To accomodate the expectations of most Python users, a :code:`View` is provided
as a mutable structure modeled after some :code:`Record`. Each view provides
that same functions as a :code:`Record`, but as member functions on the
:code:`View` instance instead.


Components
==========

There are two main ways to think about structuring data. It is either a
:code:`Type` or a :code:`Record`.

A :code:`View` is a mutable container for storing data in the shape of a
:code:`Record`. This simplifies code, especially for complex structures,
requires low overhead, and provides a typical Pythonic, OO-like interface.

The set of config parameters used for describing and controlling the nuances
of type are called the type's :code:`Schematic`.

+ **Type**: a classification of some data which describes how to both verify
  arbitrary data and serialize it both to and from Python
+ **Record**: a structure of data that has type instances, called *fields*, for
  labeled attributes.
+ **View**: a mutable structure that generated from a :code:`Record`.
+ **Schematic**: the parameters used to instantiate a :code:`Type` or
  :code:`Record` instance.

