
.. Preparing for Python 3 slides file, created by
   hieroglyph-quickstart on Mon Apr 28 15:26:15 2014.

======================
Preparing for Python 3
======================

When? What? How? Why?
=====================

.. rst-class:: build

- Should I switch to 3? mmm?
- And what about the libs?
- Would we switch to 3?

*Yes!*
======

So, how can my code be forward compatible?

Main resource:

https://docs.python.org/3/howto/pyporting.html

Notable Differences
===================

.. rst-class:: build

- The print statement vs function.
- Division.
- ``raw_input`` vs ``input`` and ``xrange`` vs ``range``.
- Object oriented made simple.
- Unicode.

Print
=====

In python 2:

.. code-block:: python

    print "some text"

or:

.. code-block:: python

    print "Hello", "world!"  # -> Hello world!

.. slide::

    In python 3:

    .. code-block:: python

        print("some text")

    or:
    
    .. code-block:: python

        print("Hello", "world!")  # -> Hello world!

So, why the switch?
===================

.. rst-class:: build

- It make more sense. You've gut an argument to manipulate -> Sounds like a function!
- It still keeps things easy to learn and easy to use.
- It can be "monkey patched" (for example, redirect, ignore, send email, filter, buffer)

    .. code-block:: python

        def print(s):
            send_email_to_admins(content=s)

``print`` new options
=====================

``sep``

    .. code-block:: python

        print('a', 'b', 'c', sep=', ')  # -> a, b, c

``end``

    .. code-block:: python

        print('processing', end='...')
        # do some processing
        print('done!')  # -> processing...done!

``file``

    .. code-block:: python

        print('some text', file='file.txt')

.. slide::

    For comparison, printing to file using python 2

    .. code-block:: python

        f = open('file.txt')
        print > f, 'some text'

    pretty ugly, non pythonic code...

But...
======

If we try to use the new syntax in python 2

.. code-block:: python

    print("Hello", "world!")  # -> ('Hello', 'world!')

Our code is not backward compatible!

===============================================================
So, How can we write code that will run on both python 2 and 3?
===============================================================

Presenting ``__future__``!
==========================

Changing the behavior of your python 2 code

.. code-block:: python

    from __future__ import print_function
    print("Now", "it", "works", "fine!")  # -> Now it works fine!

You can also use ``sep``, ``end`` and ``file``.

Note
====

It looks like an import but it isn't!

.. rst-class:: build

- ``__future__`` "imports" should be the first import on your module.
- It affects only the current module.
- The print statement is not available anymore.

    .. code-block:: python

        from __future__ import print_function
        print "python 2 style print"  # raises a SyntaxException!!!

Another option
==============

Migrate your code to python 3 using |2to3.py|_.

.. |2to3.py| replace:: ``2to3.py``
.. _2to3.py: https://docs.python.org/2/library/2to3.html

Consider the following python 2 code in ``code.py``:

.. code-block:: python

    print "Hello",
    print "world!"

To see the ``2to3`` diff run:

.. code-block:: bash

    $ 2to3 code.py

.. slide::

    To write those changes into ``code.py`` run:

    .. code-block:: bash

        $ 2to3 -w code.py

    The code after the change will be:

    .. code-block:: python

        print("Hello", end=' ')
        print("world!")

Division
========

.. rst-class:: build

- Whole number division should always perform as following (in python 2 and 3 alike):

    .. code-block:: python

        1 // 2  # -> 0
        3 // 2  # -> 1

- In python 2 ``int`` / ``int`` -> ``int``. Always!

    .. code-block:: python

        1 / 2  # -> 0

- In python 3 ``int`` / ``int`` may return a float

    .. code-block:: python

        1 / 2  # -> 0.5

Back to the ``__future__``
==========================

In order to mimic python 3 behavior using python 2 and write code that is backward and forward compatible "import" from ``__future__`` as following:

.. code-block:: python

    from __future__ import division

    1 / 2  # -> 0.5
    1 // 2  # -> 0

Old Friends. New names
======================

.. rst-class:: build

- We have already saw ``raw_input``. Why not just ``input``?

- In python 3 ``raw_input`` renamed to just ``input``.

- Same with ``xrange``. Now it named ``range``.

So how can we keep backward / forward compatibility, again?
===========================================================

.. rst-class:: build

- One option is to use ``2to3`` to migrate to python 3 as we saw earlier (breaks compatibility).

- Another option is to use the python 2 and 3 compatibility library: |six|_.

.. |six| replace:: ``six``
.. _six: https://pythonhosted.org/six/

``six``
=======

.. code-block:: python

    from six.moves import range

    print(type(range(5)))  # -> xrange on 2, range on 3

    from six.moves import input

    name = input('What is your name? ')  # works on both 2 and 3
    print(name)

Read the docs for more ``"moves"``.

===========================
Object oriented made simple
===========================

New-style and classic classes
=============================

.. rst-class:: build

- Until python 2.1 all classes object where of type ``instance``.

- New-style classes where introduced in python 2.2.

- In order to create a new-style class you would need to subclass another new-style class, or at least ``object``:

    .. code-block:: python

        class MyClass(object):
            def __init__(self):
                print("I'm a new style classed object")

.. slide::

    .. rst-class:: build

    - Old-style classes are removed in Python 3, leaving only the semantics of new-style classes.

    - You don't have to subclass ``object`` anymore, although doing so is recommended (in order to keep backward compatibility).

    - More info `here <https://docs.python.org/2/reference/datamodel.html#new-style-and-classic-classes>`_.

``super()``
===========

Look at the code below

.. code-block:: python

    class FunnyJoke(Joke):
        def tell_joke(self):
            joke = super(FunnyJoke, self).tell_joke()
            return joke + ' ha ha ha...'

.. rst-class:: build

- Can you think of any other use for ``super()``?

- No?

- Great! Because there is (almost) no other use case.

.. slide::

    In python 3 you don't have to specify the name of your class, nor giving an instance (although doing it is recommended).

    .. code-block:: python

        class FunnyJoke(Joke):
            def tell_joke(self):
                joke = super().tell_joke()  # <- here is the difference
                return joke + ' ha ha ha...'

=====================
Unicode (at least...)
=====================

Unicode in python 2
===================

.. rst-class:: build

- Strings in python are actually byte arrays.

- So we can use the 'unicode' type with unicode literals

    .. code-block:: python

        hello_hebrew = u'שלום עולם'

- or use the string encode / decode method to convert between byte arrays and unicode.

====================================
In python 3 all strings are unicode!
====================================

Backward / forward compatible code using ``six``
================================================

TODO finish
