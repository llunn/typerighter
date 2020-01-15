.. design_metaprogramming

===============
Metaprogramming
===============

The design of both :code:`Type` and :code:`Record` relies on metaprogramming to
collect information about the way they're structured.

Generally speaking, metaprogramming is a way for programs to operate on other
people's programs. For example, a data structure can be synthesized into a
suite of SQL statements for automatically interacting with a database. If done
well, it can be taken much further too, though it can also amplify our mistakes
if we're not careful.

For example, metaprogramming helps Typerighter:

+ understand the nature your data
+ record what it learns as your data's metadata
+ use that metadata to inform other behavior

From this foundation, we can then:

+ create simple systems to validate complex data
+ automate generating interfaces, eg. HTTP or SQL
+ define datatype transformation pipelines
+ convert data between native Python and simple strings & numbers


Attributes
==========

All :code:`Type` and :code:`Record` definitions have the following meta
attributes:

+ :code:`_validate_functions`
+ :code:`_schematic`

Records have two addition fields:

+ :code:`_fields`
+ :code:`_field_functions`


Types
-----

A type's :code:`validate()` function will call each function listed in
:code:`_validate_functions` on its input.

The metaclass can be told about new validation functions by adding functions
with :code:`validate_` as a prefix, ie. :code:`validate_uppercase`.

::

    class StrictStringType(StringType):
        def validate_strict(self, value):
            ...

Records
-------

Records introduce the concept of a field by associating a name with a type. It
adds two fields of metadata to the class definition.

Let's define a record with a string stored as field :code:`s`.

::

    class Foo(Record):
        s = StringType(required=True)

Fields defined like this are stored as :code:`_fields`.

It is also possible to use a function to generate field values.

::

    class Foo(Record):
        def field_s(self):
            return 'an actual string'

Functions that behave like fields have a prefix :code:`field_`, similar to the
behavior for validation functions. This field is stored as
:code:`_field_functions`.
