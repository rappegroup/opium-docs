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


Pseudopotential construction
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
-------------
Basic information regarding the atom and its orbitals.

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
the energy of this state is set to 0.0.


``[Pseudo]``\*
----------------
Information on the number of orbitals in the pseudopotential,
where the cutoff for the pseudopotential construction should be, as well as what method to 
use when constructing the pseudopotential. The method is determined solely by the first character. 
For instance, putting ``opt`` will also invoke the optimized pseudopotential method. 

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
    - Optimized (``o``), Kerker (``k``), or Troullier-Martins (``t``) pseudopotential construction method

.. note::

  If the optimized pseudopotential method is used, the ``[Optinfo]`` keyblock is
  mandatory.

Example:

.. code-block::

  [Pseudo]
  3
  1.5
  1.6
  1.6
  o


``[Optinfo]``
--------------
Additional information needed for the optimized pseudopotential
construction method. This keyblock is mandatory if the optimized pseudopotential method is used.

.. code-block::

  [Optinfo]
  qc bessel-functions
  qc bessel-functions
  qc bessel-functions
  ...

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``qc``
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
The choice of exchange-correlation functional to use in
the pseudopotential construction.

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
Options for applying a partial core correction. The default core radius is 0.0 
(meaning no partial-core) and the default method, and if a radius but no method is specified,
``lfc`` is used.

.. code-block::

  [Pcc]
  radius
  method

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``radius``
    - float
    - Partial core radius
  * - ``method``
    - ``lfc`` or ``fuchs``
    - Louie, Froyen, and Cohen (``lfc``) or Fuchs and Scheffler (``fuchs``) partial-core method

Example:

.. code-block::

  [Pcc]
  0.50
  lfc


``[Relativity]``
-----------------
Whether relativistic corrections should be applied. Default is no relativistic corrections.

.. code-block::

  [Relativity]
  method

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``method``
    - ``nrl``, ``srl``, or ``frl``
    - Non-relativistic (``nrl``), Scalar-relativistic (``srl``), or fully-relativistic (``frl``)

Scalar-relativistic implements the mass-velocity correction, Darwin correction, and spin-spin interaction.
fully-relativistic also adds spin-orbit coupling.

Example:

.. code-block::

  [Relativity]
  nrl


``[Grid]``
------------
The radial grid the calculation should be done on. Defaults parameters are
``np`` = 1201, ``a`` = 0.0001, and ``b`` = 0.013. 

.. code-block::

  [Grid]
  np a b

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``np``
    - integer
    - Number of grid points
  * - ``a``
    - float
    - Grid parameter
  * - ``b``
    - float
    - Grid parameter

The grid is created by the following formula:

.. math::

  r_i = az^{-\frac{1}{3}}e^{(i-1)b} \text{ for } i = 1, \ldots, np

Example:

.. code-block::

  [Grid]
  3000 0.0001 0.010


``[Tol]``
------------
The convergence tolerance for density-functional procedures. Default parameters
are ``etol`` = 1e-6 and ``vtol`` = 1e-8.

.. code-block::

  [Tol]
  etol vtol

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``etol``
    - float
    - Tolerance for the energy
  * - ``vtol``
    - float
    - Tolerance for the potential

Example:

.. code-block::

  [Tol]
  1e-8 1e-10


``[Configs]``
--------------
Test configurations to see how much the pseudopotential differs from the all-electron
calculations for a set of given states. The valence orbitals must be in same order as the 
reference states in the ``[Atom]`` keyblock.

.. code-block::

  [Configs]
  configurations

  nlm occupation eigenvalue
  nlm occupation eigenvalue
  nlm occupation eigenvalue
  ...

  nlm occupation eigenvalue
  nlm occupation eigenvalue
  nlm occupation eigenvalue
  ...

  ...

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``configurations``
    - integer
    - Number of test configurations
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

  [Configs]
  3

  100 2.0 -
  200 2.0 -
  210 2.0 -

  100 2.0 -
  200 2.0 -
  210 1.5 -
  
  100 2.0 -
  200 2.0 -
  210 1.0 -

``[KBDesign]``
--------------
Parameters for the designed non-local pseudopotential approach of Ramer and Rappe. Default
is no optimization functions.

.. code-block::

  [KBdesign]
  local-orbital
  boxes
  unit left right depth
  unit left right depth
  unit left right depth
  ...

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``local-orbital``
    - integer or char
    - Angular momentum of the local potential 
  * - ``boxes``
    - integer
    - Number of functions for the designed non-local procedure
  * - ``unit``
    - ``au`` or ``gp``
    - Left and right edge will be in units of bohr (``au``) or grid point (``gp``)
  * - ``left``
    - float or integer
    - Left edge for box optimization
  * - ``right``
    - float or integer
    - Right edge for box optimization
  * - ``depth``
    - float
    - Depth for box optimization

Example:

.. code-block::

  [KBdesign]
  s
  3
  au 1.0 1.5 -0.3
  gp 500 750 -0.4
  au 0.0 0.5 -1.3


``[HFSmooth]``
--------------
Trail and Needs self-consistent approach to localize
the ionic pseudopotential beyond cutoff radius. If valence orbital cutoff 
radii are set to 0.0, the cutoff radii from ``[Pseudo]`` will be used.

Default is not dampening and 1e-6 for optimization tolerance. 

.. code-block::

  [HFSmooth]
  option
  tolerance
  cutoff cutoff cutoff ...

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``option``
    - integer
    - Choice of dampening function
  * - ``tolerance``
    - float
    - Optimization tolerance
  * - ``cutoff``
    - Valence orbital cutoff radii, the order of orbitals follows ``[Optinfo]``

The localized ionic potential is defined as

.. math::

  \hat{V}_{\mathrm{loc}-\mathrm{ion}}^{n l}(r)=\left\{\begin{array}{lr} \gamma_{n l}+\hat{V}_{\mathrm{ion}}^{n l}(r) & r<r_{\mathrm{loc}} \\ e^{-\xi\left(r-r_{\mathrm{loc}}\right)^2}\left[\gamma_{n l}(r)+\hat{V}_{\mathrm{ion}}^{n l}(r)+Z / r-\hat{V}_{\mathrm{H}}\left(\rho_c\right)\right]+\hat{V}_{\mathrm{H}}\left(\rho_c\right)-Z / r & r \geqslant r_{\mathrm{loc}} \end{array}\right.

Where

.. math::

  \gamma_{nl} = p_{nl} +q_{nl} f(r,r_{loc})

The options for dampening functions gives the following choices, with 0 being
no dampening.

1:

.. math::

  f(r,r_{loc}) = \begin{cases} r^4\left(1 - \frac{2r^2}{r^2_{loc}} \right)  & r < r_{loc}\\ \frac{r_{loc}^4}{3} & r \geq r_{loc} \end{cases}

2: 

.. math::

  f(r,r_{loc}) = \begin{cases} 1 - \frac{r}{2r_{loc}}  & r < r_{loc}\\ \frac{r_{loc}}{2} & r \geq r_{loc} \end{cases}

3:

.. math::

  f(r,r_{loc}) = \begin{cases} r  & r < r_{loc}\\ r_{loc} & r \geq r_{loc} \end{cases}

4:

.. math::
  f(r,r_{loc}) = \begin{cases} \frac{1}{r} + \frac{r}{r_{loc}^2}  & r < r_{loc}\\ \frac{2}{r_{loc}} & r \geq r_{loc} \end{cases}

Example:

.. code-block::

  [HFSmooth]
  1
  1e-6
  0.0 0.0 0.0


``[Loginfo]``
--------------
Information for the logarithmic derivatives.

.. code-block::

  [Loginfo]
  configuration
  radius min max

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``configuration``
    - integer
    - Configuration number in ``[Configs]`` for logarithmic derivatives plotting, 0 for ``[Atom]`` reference state.
  * - ``radius``
    - float
    - Radius for logarithmic derivatives.
  * - ``min``
    - float
    - Minimum energy
  * - ``max``
    - float
    - Maximum energy
  
Example:

.. code-block::

  [Loginfo]
  1
  2.2 -2.0 2.0

.. _output:
Output File
==============================



Log File
==============================
