.. typerighter documentation master file

===========
Typerighter
===========

.. rubric:: Data Types for Cynical Humans.

Welcome to Typerighter, a project that makes it easy to with common types of
data using roughly the same ideas as type systems. Instead of defining what a
string is, it uses strings to define what URLs or datetimes are.

Its concepts are simple and built around ideas that require little effort for
effective data management.


Example
=======

Define a type by instantiating it with config parameters. ::

  >>> string_type = types.StringType(max_length=12)

Validate data with that type definition. ::

  >>> short_string = 'Take Five'
  >>> string_type.validate(short_string)

Define a record with fields and instantiate it. ::

  >>> class Artist(types.Record):
  ...     name = types.StringType(required=True)
  ...     website = types.URLType()
  ...
  >>> artist_type = Artist()

Validate data with that record. ::

  >>> band_data = {
  ...     'name': u'American Food',
  ...     'website': 'https://soundcloud.com/americanfood'
  ... })
  >>> artist_type.validate(band_data)

Use a mutable structure instead of a dict for the data. ::

  >>> american_food = artist_type.make_view(band_data)
  >>> # Promote the reinterpretation of Take On Me
  >>> american_food.website = 'https://soundcloud.com/americanfood/my-take-on-take-on-me'
  >>> american_food.validate()


=============
Documentation
=============

.. toctree::
   :maxdepth: 2
   :caption: User Guide

   Intro <self>
   installation
   quickstart


.. toctree::
   :maxdepth: 2
   :caption: Design Guide

   design/index.rst


.. toctree::
   :maxdepth: 2
   :caption: API Guide

   api/index.rst


.. toctree::
   :maxdepth: 2
   :caption: Development Guide

   developing/index.rst


.. toctree::
   :maxdepth: 2
   :caption: Additional Notes

   notes/contributing
   notes/license
