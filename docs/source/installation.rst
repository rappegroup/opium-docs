Installation
=====

.. _installation:

Installation
------------

To use OPIUM, first clone the github repository:

.. code-block:: console

   $ git clone https://github.com/rappegroup/opium

After navigating to the created ``opium`` directory, build the binary using:

.. code-block:: console

   $ ./configure
   $ make all

.. note::
   OPIUM is tested using `Intel <https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html>` 
   FORTRAN and C compilers. Results may not agree when using GNU, CRAY, or NVIDIA compilers.
