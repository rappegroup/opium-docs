Installation
=====

To install OPIUM, first clone the OPIUM `repository <https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html>`_:

.. code-block:: console

   $ git clone https://github.com/rappegroup/opium

After navigating to the created ``opium`` directory, build the binary using

.. code-block:: console

   $ ./configure
   $ make all

OPIUM can now be called using

.. code-block:: console

   $ ./opium input.param output.txt command1 command2 ...

.. note::
   OPIUM is tested using `Intel <https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html>`_ 
   FORTRAN and C compilers. Results may not agree when using GNU, CRAY, or NVIDIA compilers.
