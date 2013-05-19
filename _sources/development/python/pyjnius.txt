Python: accessing java classes via pyjnius
##########################################

Some notes about using pyjnius_ to access Java libraries from Python.

.. _pyjnius: https://github.com/kivy/pyjnius

Have a look at `this post on my blog <http://www.hackzine.org/using-apache-tika-from-python-with-jnius.html>`_
for more information on what I did with it.


Installing pyjnius
==================

Pretty straight-forward::

  pip install cython
  pip install git+git://github.com/kivy/pyjnius.git


Importing classes
=================

Just use ``autoclass()`` to import java classes inside Python:

.. code-block:: python

    from jnius import autoclass
    Tika = autoclass('org.apache.tika.Tika')
    Metadata = autoclass('org.apache.tika.metadata.Metadata')
    FileInputStream = autoclass('java.io.FileInputStream')

And then use them right away:

.. code-block:: python

    tika = Tika()
    meta = Metadata()
    text = tika.parseToString(FileInputStream(filename), meta)


.. warning:: I'm currently experiencing memory leaks when using
	     Tika like that.. I'm currently debugging the thing,
	     but maybe I missed something related to garbage 
	     collection..
