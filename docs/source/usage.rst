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
     - Generate ``.pwf`` output (for BH)
   * - ``ncpp``
     - Generate ``.ncpp`` output (for PWSCF)
   * - ``recpot``
     - Generate ``.recpot`` output (for CASTEP)


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

\* indicates a mandatory keyblock.


``[Atom]``\*
------------
.. code-block::

  [Atom]
  symbol
  orbitals
  nlm occupation eigenvalue
  nlm occupation eigenvalue
  nlm occupation eigenvalue
  ...

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Name
     - Format
     - Description
   * - ``symbol``
     - 1 or 2 characters
     - Atomic symbol of the atom
   * - ``orbitals``
     - integer
     - Number of reference orbitals
   * - ``nlm``
     - integer
     - Quantum numbers of the orbital
   * - ``occupation``
     - float
     - Occupation of the orbital 
   * - ``eigenvalue``
     - ``-`` or float
     - Initial eigenvalue guess, ``-`` automatically generates a guess

Example:

.. code-block::

  [Atom]
  C
  3
  100 2.0 -
  200 2.0 -
  210 2.3 -0.3

An unbound valence state can be indicated by making the occupation value negative. 
This invokes the Hamman generalized state method and the occupation is set to 0. 
You should also specify an eigenvalue guess (can be positive or negative) for 
the energy of this state. If a ``-`` is in the eigenvalue guess, 
the energy of this state is set to 0.0 

``[Pseudo]``\*
------------

.. code-block::

  [Pseudo]
  orbitals
  rc
  rc
  rc
  ...
  method

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Name
     - Format
     - Description
   * - ``orbitals``
     - integer
     - Number of orbitals in the pseudopotential
   * - ``rc``
     - float
     - Cut of radius for a single pseudo orbital
   * - ``method``
     - ``o``, ``k``, or ``t``
     - Optimized (o), Kerker (k), or Troullier-Martins (t) pseudopotential construction method

.. note::

  The method is determined solely by the first character it reads. For instance,
  putting ``opt`` will also invoke the optimized pseudopotential method as well.

Example:

.. code-block::

  [Pseudo]
  3
  1.5
  1.6
  1.6
  o


``[Optinfo]``
------------

.. note::
  This keyblock is mandatory if the optimized pseudopotential method is used.

.. code-block::

  [Optinfo]
  cut-off bessel-functions
  cut-off bessel-functions
  cut-off bessel-functions
  ...

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Name
     - Format
     - Description
   * - ``cut-off``
     - float
     - Cut-off wavevector qc for an orbital
   * - ``bessel-functions``
     - integer
     - Number of bessel functions for an orbital

Example:

.. code-block::

  [Optinfo]
  6.00 4
  7.07 10
  4.00 5


``[XC]``\*
------------
.. code-block::

  [XC]
  functional

.. list-table::
   :widths: auto
   :header-rows: 1

   * - Name
     - Format
     - Description
   * - ``functional``
     - string
     - Desired exchange-correlation functional

Currently, the following functionals are supported by OPIUM:

.. list-table::
   :widths: auto
   :header-rows: 1

   * - String
     - Functional
   * - ``lda``
     - Perdew-Zunger LDA
   * - ``pwlda``
     - Perdew-Wang LDA
   * - ``gga``
     - Perdew-Burke-Ernzerhof (PBE) GGA
   * - ``hf``
     - Hartree-Fock pseudopotential
   * - ``pbe0``
     - PBE0 hybrid functional
   * - ``wpbe0``
     - wPBE0 range-separated hybrid functional

.. note::

  ``[HFsmooth]`` needs to be used to maintain the coulombic behavior outside the cutoff radius
  for ``hf``, ``pbe0``, and ``wpbe0``. Relativity is also not yet supported for ``pbe0`` and 
  ``wpbe0``.

Example:

.. code-block::

  [XC]
  gga



``[Pcc]``
------------


``[Relativity]``
------------


``[Grid]``
------------


``[Tol]``
------------


``[Configs]``
------------


``[KBDesign]``
------------


``[HFSmooth]``
------------


``[Loginfo]``
------------


.. _output:
Output File
==============================



Log File
==============================
