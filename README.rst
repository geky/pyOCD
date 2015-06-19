pyOCD
=====

pyOCD is an Open Source python 2.7 based library for programming and debugging 
ARM Cortex-M microcontrollers using CMSIS-DAP. Linux, OSX and Windows are 
supported.

The following is provided from just a python interpretor:

-  halt, step, resume execution
-  read/write memory
-  read/write block memory
-  read-write core register
-  set/remove hardware breakpoints
-  flash new binary
-  reset

Installation
------------

To install the latest development version (master branch), you can do
the following:

.. code:: shell

    $ pip install --pre -U https://github.com/<user>/pyOCD/archive/master.zip

Note that you may run into permissions issues running these commands.
You have a few options here:

#. Run with ``sudo -H`` to install pyOCD and dependencies globally
#. Specify the ``--user`` option to install local to your user
#. Run the command in a `virtualenv <https://virtualenv.pypa.io/en/latest/>`__ 
   local to a specific project working set.

You can also install from source by cloning the git repository and running

.. code:: shell

    $ python setup.py install

Development Setup
-----------------

PyOCD developers are recommended to setup a working environment using
`virtualenv <https://virtualenv.pypa.io/en/latest/>`__. After cloning
the code, you can setup a virtualenv and install the PyOCD
dependencies for the current platform by doing the following:

.. code:: console

    $ virtualenv env
    $ source env/bin/activate
    $ pip install -r dev-requirements.txt

On Windows, the virtualenv would be activated by executing
``env\Scripts\activate``.

Examples
--------

Tests
~~~~~

A series of tests are provided in the test directory:

-  basic\_test.py: a simple test that checks:

   -  read/write core registers
   -  read/write memory
   -  stop/resume/step the execution
   -  reset the target
   -  erase pages
   -  flash a binary
    
Hello World example code
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

    from pyOCD.board import MbedBoard

    import logging
    logging.basicConfig(level=logging.INFO)

    board = MbedBoard.chooseBoard()

    target = board.target
    flash = board.flash
    target.resume()
    target.halt()

    print "pc: 0x%X" % target.readCoreRegister("pc")
        pc: 0xA64

    target.step()
    print "pc: 0x%X" % target.readCoreRegister("pc")
        pc: 0xA30

    target.step()
    print "pc: 0x%X" % target.readCoreRegister("pc")
       pc: 0xA32

    flash.flashBinary("binaries/l1_lpc1768.bin")
    print "pc: 0x%X" % target.readCoreRegister("pc")
       pc: 0x10000000

    target.reset()
    target.halt()
    print "pc: 0x%X" % target.readCoreRegister("pc")
       pc: 0xAAC

    board.uninit()

Architecture
------------

Interface
~~~~~~~~~

An interface does the link between the target and the computer.
This module contains basic functionalities to write and read data to and from
an interface. You can inherit from ``Interface`` and overwrite
``read()``, ``write()``, etc

Then declare your interface in ``INTERFACE`` (in ``pyOCD.interface.__init__.py``)

Target
~~~~~~

A target defines basic functionalities such as ``step``, ``resume``, ``halt``,
``readMemory``, etc. You can inherit from Target to implement your own methods.

Then declare your target in TARGET (in ``pyOCD.target.__init__.py``)

Transport
~~~~~~~~~

Defines the transport used to communicate. In particular, you can find CMSIS-DAP.
Implements methods such as ``memWriteAP``, ``memReadAP``, ``writeDP``, ``readDP``, ...

You can inherit from ``Transport`` and implement your own methods.
Then declare your transport in ``TRANSPORT`` (in ``pyOCD.transport.__init__.py``)

Flash
~~~~~

Contains flash algorithm in order to flash a new binary into the target.

