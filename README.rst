===========================================================
  orgparse - Pyton module for reading Emacs org-mode file
===========================================================


Links:

* `Documentation (at Read the Docs) <http://orgparse.readthedocs.org/>`_
* `Repository (at GitHub) <https://github.com/tkf/orgparse>`_
* `Issue tracker (at GitHub) <https://github.com/tkf/orgparse/issues>`_
* `PyPI <http://pypi.python.org/pypi/orgparse>`_
* `Travis CI <https://travis-ci.org/#!/tkf/orgparse>`_ |build-status|

.. |build-status|
   image:: https://secure.travis-ci.org/tkf/orgparse.png?branch=master
   :target: http://travis-ci.org/tkf/orgparse
   :alt: Build Status


Install
-------

You can install `orgparse` from PyPI_::

  pip install orgparse


Usage
-----

Loading org object
^^^^^^^^^^^^^^^^^^
::

    from orgparse import load, loads

    load('PATH/TO/FILE.org')
    load(file_like_object)

    loads('''
    * This is org-mode contents
      You can load org object from string.
    ** Second header
    ''')


Traverse org tree
^^^^^^^^^^^^^^^^^

>>> root = loads('''
... * Heading 1
... ** Heading 2
... *** Heading 3
... ''')
>>> for node in root[1:]:  # [1:] for skipping root itself
...     print(node)
* Heading 1
** Heading 2
*** Heading 3
>>> h1 = root.children[0]
>>> h2 = h1.children[0]
>>> h3 = h2.children[0]
>>> print(h1)
* Heading 1
>>> print(h2)
** Heading 2
>>> print(h3)
*** Heading 3
>>> print(h2.get_parent())
* Heading 1
>>> print(h3.get_parent(max_level=1))
* Heading 1


Accessing to node attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

>>> root = loads('''
... * DONE Heading          :TAG:
...   CLOSED: [2012-02-26 Sun 21:15] SCHEDULED: <2012-02-26 Sun>
...   CLOCK: [2012-02-26 Sun 21:10]--[2012-02-26 Sun 21:15] =>  0:05
...   :PROPERTIES:
...   :Effort:   1:00
...   :OtherProperty:   some text
...   :END:
...   Body texts...
... ''')
>>> node = root.children[0]
>>> node.heading
'Heading'
>>> node.scheduled
OrgDateScheduled((2012, 2, 26))
>>> node.closed
OrgDateClosed((2012, 2, 26, 21, 15, 0))
>>> node.clock
[OrgDateClock((2012, 2, 26, 21, 10, 0), (2012, 2, 26, 21, 15, 0))]
>>> bool(node.deadline)   # it is not specified
False
>>> node.tags == set(['TAG'])
True
>>> node.get_property('Effort')
60
>>> node.get_property('UndefinedProperty')  # returns None
>>> node.get_property('OtherProperty')
'some text'
>>> node.body
'  Body texts...'
