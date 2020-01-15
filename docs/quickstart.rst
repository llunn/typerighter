.. quickstart

==========
Quickstart
==========

This section of the documentation exists to provide an overview of
Typerighter's fundamentals. It isn't comprehensive, but you will have great
intuition for what to expect from Typerighter by the time we are done.

Types & Records
===============

Types are the foundation of Typerighter, but you will probably spend most of
your time working with records.

A :code:`Type` is an object that can validate and transform a single value. A
:code:`Record` is an object that knows how to validate and transform a
collection of labeled values, or *fields*.

The code for defining a :code:`Record` will probably feel familiar. ::

  from typerighter import types

  class Artist(types.Record):
      name = types.StringType(required=True)
      website = types.URLType()
      epoch = types.DateTimeType()

With just the code above, Typerighter has already anazlyed the :code:`Artist`
structure and generated metadata, attached as private attributes, such as
:code:`_fields`. ::

  >>> Artist._fields
  OrderedDict([('name', <typerighter.types.primitives.StringType object at 0x10816ccf8>), ('website', <typerighter.types.net.URLType object at 0x1086949b0>), ('began', <typerighter.types.timekeeping.DateTimeType object at 0x108694198>)])

Typerighter types and records are both just structures of functions that either
accept or reject some input. Attempts to overwrite their fields will be
ignored. ::

  >>> artist_type = Artist()
  >>> artist_type.name = 'foo'
  >>> artist_type.name
  <typerighter.types.primitives.StringType object at 0x108694eb8>

Validation
==========

We can use our new type instance to validate input has a name (*required*), and
a website (*optional*), and epoch (*optional*). ::

  >>> band_data = {
  ...     'name': u'American Food',
  ...     'website': u'https://soundcloud.com/americanfood'
  ... }
  >>> artist_type.validate(band_data)

Things work smoothly when the data is good, but what if the data is bad? ::

  >>> band_data['website'] = 'this string is not a url'

Validation fails and the error explains that the string does not look like a
URL. ::

  >>> artist_type.validate(band_data)
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/base.py", line 165, in validate
      func(self, native)
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/base.py", line 56, in wrapper
      return method(self, value, *a, **kw)
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/records.py", line 119, in validate_fields
      ti.validate(value[fn])
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/base.py", line 56, in wrapper
      return method(self, value, *a, **kw)
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/net.py", line 175, in validate
      super().validate(value)
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/base.py", line 165, in validate
      func(self, native)
  File "/Users/jmsdnns/Projects/typerighter/typerighter/types/domains.py", line 115, in validate_regex
      err_msg.format(instance.__class__.__name__, value)
  typerighter.exceptions.ValidationException: Value failed URLType regex: this string is not a url

Conversions
===========

Data can be converted seamlessly from native Python to language agnostic
primitives with `to_native()` and `to_primitive()`. ::

  >>> import datetime
  >>> band_data = {
  ...     'name': 'American Food',
  ...     'epoch': datetime.datetime.now(),
  ...     'website': 'https://soundcloud.com/americanfood'
  ... }

Watch the value for :code:`epoch` as we use each function. ::

  >>> artist_type.to_primitive(band_data)
  {'name': 'American Food', 'epoch': '2020-01-15T03:13:00.371106', 'website': 'https://soundcloud.com/americanfood'}
  >>> artist_type.to_native(band_data)
  {'name': 'American Food', 'epoch': datetime.datetime(2020, 1, 15, 3, 13, 0, 371106), 'website': 'https://soundcloud.com/americanfood'}

The conversion process also allows for choosing which fields to include in the
output, helpful for limiting access to particular fields.

  >>> artist_type.to_primitive(band_data, fields=['name'])
  {'name': 'American Food'}


Views
=====

Type instances do not store data. This keeps the namespace for the type's
definition separate from the mess of data we're working with.

This is beneficial from an architectural perspective, but Python is an OO
language. To maintain am appropriate Pythonic feel, an OO-like interface,
called a :code:`View`, is provided. A view is a mutable structure that
combines both a :code:`Record` and some data into a single object.

A :code:`View` is created by calling a record's :code:`to_view()` function. A
view is a mutable structure that combines both a :code:`Record` and some data
into a single object. ::

  >>> american_food = artist_type.to_view(band_data)
  >>> american_food.name
  'American Food'
  >>> american_food.website = 'https://soundcloud.com/americanfood/my-take-on-take-on-me'

Views have a :code:`validate` method, implemented by passing data to the
record's validate function. ::

  >>> american_food.validate()

The functions for converting values to, and from, native Python are there as
methods too ::

  >>> american_food.to_primitive()
  { 'name': 'American Food', 'website': 'https://soundcloud.com/americanfood'}

Views start with the same parameters as the `Record` they are based on, but
these parameters can be changed to alter the behavior of the `View` after
instantiation.
