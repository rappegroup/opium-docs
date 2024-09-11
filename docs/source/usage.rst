Execution Types
===================================================

Non-interactive execution 
--------------------------

Non-interactive execution is the preferred method of running OPIUM. It is called
using the following:

.. code-block:: console

   $ ./opium input.param output.txt command1 command2 ...

Where

* ``input.param`` is a :ref:`parameter file <parameter>` with the necessary keyblocks
* ``output.txt`` is the :ref:`output file <output>` to be created
* ``command1``, ``command2``, etc are different :ref:`commands <commands>` that can be executed

For example, given some parameter file ``c.param``, to do the all-electron (AE) solve, generate the pseudopotential, 
generate a report, and write the output to a ``.upf`` file for quantum ESPRESSO, one should execute the following command:

.. code-block:: console

   $ ./opium c.param output.txt ae ps rpt upf

As of the 1.0.1 release, ``.param`` is implied, so it is not required to be specified.


Interactive execution 
-----------------------

.. note::
    Interactive execution is not tested as thoroughly as non-interactive use and 
    should be avoided if possible.

To call OPIUM in interactive mode, do not place any commands after the log file:

.. code-block:: console

   $ ./opium input.param output.txt

This will create a prompt where commands can be specified dynamically as the program runs:

.. code-block:: console

    $ opium[param=input.param|log=output.txt|verb=0]>> command1 command2
    $ opium[param=input.param|log=output.txt|verb=0]>> command3

The output will be identical to if the commands were called by the non-interactive mode.
Interactive mode also has :ref:`some commands <interactive mode commands>` that are not available normally. 
To exit the interactive mode, type the command ``exit``.

.. _commands:
Commands
=============================
The following commands are available in the current release of OPIUM. 


Solve and pseudopotential construction
-----------------------------------------


.. list-table::
   :widths: auto
   :header-rows: 1

   * - Command
     - Description
   * - ``ae``
     - Performs all electron (AE) solve of the atom
   * - ``ps``
     - Construct a pseudopotential from the AE solve
   * - ``nl``
     - Solve using the pseudopotential for a reference state
   * - ``tc``
     - Solve using the pseudopotential and AE for test configurations
   * - ``ke``
     - Optimize the kinetic energy using the RRKJ method
   * - ``aa``
     - Shorthand for ``ae ps nl tc``

Output style
--------------------

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Command
     - Description
   * - ``upf``
     - Generate v2.0.1 ``.upf`` output (for Quantum ESPRESSO)
   * - ``oldupf``
     - Generate legacy ``.upf`` output (for older versions of Quantum ESPRESSO)
   * - ``fhi``
     - Generates ``.fhi`` and ``.cpi`` outputs (for ABINIT and FHI98MD)
   * - ``pwf``
     - Generate \*.pwf output (for BH)
   * - ``ncpp``
     - Generate \*.ncpp output (for PWSCF)
   * - ``recpot``
     - Generate \*.recpot output (for CASTEP)


Plotting
--------------------

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Command
     - Description
   * - ``plot wa``
     - Plots all-electron wavefunctions
   * - ``plot wp``
     - Plots pseudo and all-electron wavefunctions
   * - ``plot pcc``
     - Plots core, valence, and partial core densities
   * - ``plot vs``
     - Plots screened pseudopotentials
   * - ``plot vi``
     - Plots the ionic (descreened) pseudopotentials
   * - ``plot qp``
     - Plots the q-space pseudo-wavefunctions and potentials
   * - ``plot logd``
     - Plots logarithmic derivative state in the ``[Loginfo]`` keyblock

.. _interactive mode commands:
Interactive mode
--------------------

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Command
     - Description
   * - ``v``
     - Toggles verbosity flag in interactive mode
   * - ``comm``
     - Prints command line help
   * - ``plot``
     - Prints plotting help
   * - ``keys``
     - Prints keyblock help
   * - ``exit``
     - Exits the interactive mode

Reports
--------------------

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Command
     - Description
   * - ``rpt``
     - Generate a report file


.. _parameter:
Parameter File
==============================

OPIUM parameter files are structured using keyblocks that are parsed
by the `FlexiLib <https://spinor.sourceforge.net/FlexiLib/index.html>`_ library. 


[Atom]
------------


[Pseudo]
------------


[Optinfo]
------------


[XC]
------------


[PCC]
------------


[Relativity]
------------


[Grid]
------------


[Tol]
------------


[Configs]
------------



[KBDesign]
------------

[HFSmooth]
------------


[Loginfo]
------------

.. _output:
Output File
==============================
