Introduction
=====

.. _installation:

Installation
------------

To install OPIUM, first clone the `OPIUM repository <https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html>`_:

.. code-block:: console

   $ git clone https://github.com/rappegroup/opium

After navigating to the created ``opium`` directory, build the binary using:

.. code-block:: console

   $ ./configure
   $ make all

OPIUM can now be called from the command line using:

.. code-block:: console

   $ ./opium input.param output.txt command1 command2 ...

To use graphical features of Opium, the plotting program ``Grace`` must also be installed. 
``Grace`` can be downloaded from `its homepage <https://plasma-gate.weizmann.ac.il/Grace/>`_. 

.. note::
   OPIUM is tested using `Intel <https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html>`_ 
   FORTRAN and C compilers. Although attempts have been made to made to allow it to be compatible with other
   compullers like GNU, CRAY, or NVIDIA, there is no guarntee that outputs would align exactly.

If the above does not work, you can also try:

* Downloading the `statically compiled binary <https://sourceforge.net/projects/opium/>`_ for OPIUM
* Trying a different version of OPIUM
* Using a different C or FORTRAN compiler


Tutorial
------------



Examples
------------


1: Hydrogen
~~~~~~~~~~

2: Carbon
~~~~~~~~~~

3: Aluminum
~~~~~~~~~~