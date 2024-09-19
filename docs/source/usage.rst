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
  radius min-energy max-energy

.. list-table::
  :widths: auto
  :header-rows: 1

  * - Name
    - Format
    - Description
  * - ``configuration``
    - integer
    - Configuration number in ``[Configs]`` for logarithmic derivatives plotting, 0 for ``[Atom]`` reference state
  * - ``radius``
    - float
    - Radius for logarithmic derivatives
  * - ``min-energy``
    - float
    - Minimum energy
  * - ``max-energy``
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
The output file is split into the following sections, depending
on what commands were executed. In this section we will use the output
file from the :ref:`Hydrogen tutorial <hydrogen>` as an example.

Welcome
----------------
The log file begins with a welcome message and writes out most of the parameters of the calculation.
Below is an example of a welcome message in OPIUM 4.1.  When running non-interactively, 
this section is at the beginning of every log file. In interactive mode this section 
is written after every carriage return. 

.. code-block::

  ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
          OPIUM  Version:        4.1                             
          =====================================            
  See https://opium-psp.readthedocs.io for help and information                 
  Copyright 2024 : The OPIUM project                         

  Compile host     : Host-Name
  Compile OS       : OS-Lame
  Compile date     : Fri Aug  9 02:36:21 EDT 2024

  Execution host   : wlan.private.upenn.edu
  time of execution: Wed Sep 18 23:20:46 2024

  ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
  Reading parameter file:  <h.param> ... 
  File prefix             : h 
  Element                 : Hydrogen   (H) 
  Z                       : 1 
  
  Number of all-electron orbitals   : 1 
  Number of pseudo       orbitals   : 1 
  
  Pseudopotential is non-relativistic 
  Exchage-correlation functional is : Perdew, Burke, Ernzerhof GGA 
  GGA smoothed from 0 to 0.001000 bohr using switch to LDA 
  The s potential is used for the KB construction 
  Optimized (RRKJ) pseudopotential method will be used 

  nl    cutoff radii    q-max      # bessel functions
  1s       1.400        5.500              4 

  Reference Configuration: core  <-/-> valence 
  <-/-> 1s1 

  Grid definitions: 
  a_grid=1.00e-04    b_grid=1.30e-02    # points=1076 
  r(1)=1.00e-04      r(np)=1.17e+02    

  dEmax tolerance:  1.00e-08      dVmax tolerance:  1.00e-06     

  Starting calculation...


All-Electron (``ae``)
----------------------
The first part of the AE section shows the convergence of the total energy (``Etot``), the orbital energy 
(``Ebs``) and the sum of the Hartree and exchange/correlation energy (``Ehxc``). Also, 
the largest change in the eigenvalues and the potential is printed. Then the file summerizes 
summary of the eigenvalues and partial norm of each wavefunction, which is the integral from the cutoff 
radius to infinity. The partial norm is only calculated for valence states. 

.. code-block::

  ======================================================================== 
  Begin AE calculation
  ======================================================================== 
  Performing non-relativistic AE calculation...  
    Core orbitals - 
    Valence orbitals - 
  |100>   1.000 -1.000 1  

  ---------Eigenvalue guesses---------
  1s            -1.00000000

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -1.4759780      -1.0000000      -0.4759780 0.00E+00 0.20E+01
    2       -1.2066359      -0.7476490      -0.4589869 0.25E+00 0.10E+01
    3       -0.9424939      -0.5007015      -0.4417924 0.33E+00 0.17E-01
    4       -0.9194252      -0.4783007      -0.4411246 0.45E-01 0.42E-02
    5       -0.9177304      -0.4768898      -0.4408407 0.29E-02 0.16E-02
    6       -0.9177335      -0.4770052      -0.4407283 0.24E-03 0.11E-02
    7       -0.9178008      -0.4771188      -0.4406820 0.24E-03 0.39E-03
    8       -0.9178164      -0.4771560      -0.4406605 0.78E-04 0.20E-03
    9       -0.9178308      -0.4771782      -0.4406526 0.47E-04 0.20E-03
   10       -0.9178437      -0.4771932      -0.4406505 0.32E-04 0.14E-03
   11       -0.9178526      -0.4772021      -0.4406505 0.19E-04 0.95E-04
   12       -0.9178576      -0.4772066      -0.4406510 0.96E-05 0.59E-04
   13       -0.9178601      -0.4772087      -0.4406514 0.44E-05 0.36E-04
   14       -0.9178612      -0.4772095      -0.4406517 0.17E-05 0.21E-04
   15       -0.9178616      -0.4772098      -0.4406518 0.56E-06 0.13E-04
   16       -0.9178617      -0.4772098      -0.4406519 0.12E-06 0.75E-05
   17       -0.9178617      -0.4772098      -0.4406519 0.14E-07 0.45E-05
   18       -0.9178617      -0.4772098      -0.4406519 0.32E-07 0.27E-05
   19       -0.9178617      -0.4772098      -0.4406519 0.16E-07 0.16E-05
   20       -0.9178617      -0.4772098      -0.4406519 0.16E-08 0.97E-06

  20 iterations: (units are Ry and bohr)
  Energy:      -0.91786170    Ebs:       0.47720981
  Eh:           0.57961839    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      1.000         -0.477210      0.517325      1.033199

  ======================================================================== 
  End AE calculation
  ======================================================================== 

Pseudopotential (``ps``)
--------------------------
The first section displays the state being pseudized, its eigenvalue, selected wavevector cut-off 
(``qc``), and the number of basis functions used in the bessel expansion. Next, the grid point nearest to 
the desired cut-off (pseudization) radius, the desired cut-off radius ``rc``, and then actual cut-off radius 
``actual rc`` are displayed. The next block shows the parameters which define the soon to be constructed 
pseduo-wavefunction. These are: the AE wavefunction, it's first two derivatives, the Laplacian and 
the logarithmic derivative at the cut-off radius.

The next section shows the convergence of the residual kinetic energy minimization. The step #, 
step size (``theta``), ``slove`` and ``curv`` when ``theta`` is zero, the residual KE (``KEresid``) and 
the sum of the Bessel coefficients (``coeffsum``). After the minimization converges, the final # of
steps, residual KE and the errors in partial norm, value at ``rc,`` and curvature at ``rc`` are all reported.

The last section shows the set of bessel wavevectors, their 
coefficients and the final convergence information. The convergence term is 
the residual kinetic energy. The convergence error is 
the convergence term multiplied by the occupation number.

.. note::
  The convergence error should be checked carefully. This value signifies the level of error that 
  this state contributes to the total energy at the minimal cut-off energy (``Ecut``) is qc^2 Ry.
  Even if you obtain excellent transferability for your pseudoptential, you still must 
  have a small convergence error at this qc for your results to be correct.

.. code-block::
  
  ======================================================================== 
  Begin PS construction
  ======================================================================== 
  Optimized Pseudopotential Generation ...

  ==================
  zeff=   1.0000000000000000     
  Pseudizing state : |100>
  eigenvalue       : -0.477210
  qc               :  5.500000
  # bessel fxns    :  4
  point nearest rc :  735
  rc               :  1.400000
  actual rc        :  1.393279

  Psi(rc)          :     0.4815934518
  Slope at rc      :    -0.4583799300
  Curvature at rc  :     0.4635102682
  Del^2 Psi at rc  :    -0.1944772318
  Log Deriv at rc  :    -0.9517985103

  Starting KE minimization...
    step #    theta       slope       curv       KEresid    coeffsum
      1    0.036333   -0.462954    6.371026    0.000426    1.395387
      2   -0.000806    0.009821    6.096020    0.000422    1.390827
      3   -0.000000    0.000005    6.089608    0.000422    1.390824

  # steps:     3
  Final KE error:    0.0004222771
  Sum of coeffs :    1.3908242008
  ---------------------------------------------------------------
  Coeff:      0.989367  0.378993  0.038394 -0.015929
  ---------------------------------------------------------------
  Resid KE (Ry)     :        0.0004222771
  Norm error        :    -0.222E-15
  Continuity error  :     0.500E-15
  Curvature error   :    -0.283E-14
  ---------------------------------------------------------------
  Bessel wavevectors and final coefficients
    1        1.2593092185        0.9893669185
    2        3.4311174634        0.3789925038
    3        5.6666802791        0.0383938998
    4        7.9130949657       -0.0159291212

  Total KE           (Ry) :     0.8528531031
  0  -> qc contribution   :     0.8524308260
  qc -> oo contribution   :     0.0004222771   (=Convergence term)
  Occupancy of this state :     1.0000000000
  Convergence term (Ry/e) :     0.0004222771
  Convergence error (mRy) :     0.4222771391
  Convergence error (meV) :     5.7454182991

The next part of the ``ps`` output shows total valence and core charge.
The core charge will always be zero unless a partial core correction is 
used, in which case the partial core charge is printed.
Next, the Schrodinger equation is solved for all valence states 
to ensure that the pseudopotential yields the correct eigenvalue for the reference state. 
The final section of the ``ps`` calculation is a loop over all valence states to check
for the existence of ghost states. 

.. code-block::

  ------------------------------
  Descreening potential
  valence charge          :   1.000000
  core    charge          :   0.000000

  ----Solving the Schrodinger equation for all states----
  State: 1s  AE eigenvalue =  -0.477210  PS eigenvalue =  -0.477210

  ---Semilocal ghost testing---
  Local state: 1s

  No ghosts for local potential: 1s
  ------------------------------

  ======================================================================== 
  End PS construction
  ======================================================================== 

Non-local (``nl``)
--------------------------
After the semi-local pseudopotential is constructed, the Kleinman-Bylander non-local form is tested. 
The eigenvalues and partial norms for the ``nl`` section should agree with ``ae`` section.
To create the Kleinman-Bylander form, a local potential must be defined, usually one of
the valence potentials. OPIUM also has the ability to use the 
sum of one or a series of step functions as a valence potential. 

The last part of the ``nl`` section is another round of ghost testing. 
These results should be the same as the ``ps`` ghost testing if the local 
potential is just chosen from the valence. If the designed 
non-local method is used (some function(s) added to a valence potential) 
this can change the ghost behavior.

.. code-block::

  ======================================================================== 
  Begin NL calculation
  ======================================================================== 
  ---------Eigenvalue guesses---------
  1s            -0.47720981
  Performing non-local pseudopotential calculation
  Using the s potential as the local potential

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.9158837      -0.4772098      -0.4386739 0.00E+00 0.34E-07

  Converged in 1 iteration (probably reference state)
  Energy:      -0.91588374    Ebs:       0.47720981
  Eh:           0.57715535    Ex :       0.00000000

    Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
      |100>      1.000          -0.477210      0.517325      0.955672

  ---Non-local ghost testing---
  Local state: 1s

  Test  state: 1s
  ...seems like this angular momentum is the local part!
  ------------------------------

  No ghosts present for local potential


  ======================================================================== 
  End NL calculation
  ======================================================================== 


Test configurations (``tc``)
------------------------------
After the ``ae``, ``ps``, and ``nl`` steps are completed with small convergence errors 
and no ghosts for the desired local potential, the transferability must now be checked. 
Transferability measures how well a pseudopotential performs in 
environments other than the reference configuration 
(the configuration used in the generation step). The all-electron and pseudo-eigenvalues and
partial norms are computed for each test configuration (from the ``[Configs]`` keyblock).

.. code-block::

  ======================================================================== 
  Begin TC calculation
  ======================================================================== 


  ===============Configuration 1 AE Calc=============== 
    Core orbitals - 
    Valence orbitals - 
  |100>   0.750 -1.000 1  

  ---------Eigenvalue guesses---------
  1s            -1.00000000

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.9994324      -0.7500000      -0.2494324 0.00E+00 0.15E+01
    2       -0.8962287      -0.6472996      -0.2489291 0.14E+00 0.75E+00
    3       -0.7837571      -0.5354364      -0.2483207 0.17E+00 0.36E-02
    4       -0.7726194      -0.5243242      -0.2482952 0.21E-01 0.15E-02
    5       -0.7715369      -0.5232529      -0.2482841 0.20E-02 0.54E-03
    6       -0.7714315      -0.5231528      -0.2482786 0.19E-03 0.32E-03
    7       -0.7714232      -0.5231472      -0.2482760 0.11E-04 0.16E-03
    8       -0.7714247      -0.5231498      -0.2482749 0.50E-05 0.11E-03
    9       -0.7714267      -0.5231520      -0.2482746 0.43E-05 0.83E-04
   10       -0.7714280      -0.5231534      -0.2482746 0.26E-05 0.61E-04
   11       -0.7714288      -0.5231541      -0.2482746 0.14E-05 0.42E-04
   12       -0.7714292      -0.5231545      -0.2482747 0.66E-06 0.27E-04
   13       -0.7714293      -0.5231546      -0.2482747 0.28E-06 0.17E-04
   14       -0.7714294      -0.5231547      -0.2482748 0.11E-06 0.11E-04
   15       -0.7714294      -0.5231547      -0.2482748 0.21E-07 0.64E-05
   16       -0.7714295      -0.5231547      -0.2482748 0.10E-07 0.39E-05
   17       -0.7714295      -0.5231547      -0.2482748 0.65E-08 0.23E-05
   18       -0.7714295      -0.5231547      -0.2482748 0.59E-08 0.14E-05
   19       -0.7714295      -0.5231547      -0.2482748 0.40E-08 0.84E-06

  19 iterations: (units are Ry and bohr)
  Energy:      -0.77142946    Ebs:       0.52315468
  Eh:           0.34992297    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.750          -0.697540      0.473785      0.980845


  ===============Configuration 1 NL: Calc ===============
  ---------Eigenvalue guesses---------
  1s            -0.47720981
  Performing non-local pseudopotential calculation
  Using the s potential as the local potential

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.5875933      -0.3579074      -0.2296859 0.00E+00 0.50E+00
    2       -0.6776716      -0.4391304      -0.2385413 0.23E+00 0.25E+00
    3       -0.7609409      -0.5150736      -0.2458673 0.17E+00 0.58E-02
    4       -0.7689786      -0.5227604      -0.2462182 0.15E-01 0.27E-02
    5       -0.7697903      -0.5233659      -0.2464245 0.12E-02 0.16E-02
    6       -0.7697784      -0.5232564      -0.2465220 0.21E-03 0.12E-02
    7       -0.7695752      -0.5230174      -0.2465578 0.46E-03 0.39E-03
    8       -0.7695122      -0.5229410      -0.2465712 0.15E-03 0.12E-03
    9       -0.7695006      -0.5229240      -0.2465766 0.33E-04 0.78E-04
   10       -0.7694938      -0.5229152      -0.2465786 0.17E-04 0.56E-04
   11       -0.7694891      -0.5229098      -0.2465792 0.10E-04 0.35E-04
   12       -0.7694861      -0.5229068      -0.2465793 0.58E-05 0.21E-04
   13       -0.7694845      -0.5229052      -0.2465793 0.31E-05 0.12E-04
   14       -0.7694836      -0.5229044      -0.2465792 0.15E-05 0.65E-05
   15       -0.7694832      -0.5229041      -0.2465791 0.68E-06 0.36E-05
   16       -0.7694830      -0.5229039      -0.2465791 0.28E-06 0.19E-05
   17       -0.7694829      -0.5229039      -0.2465790 0.96E-07 0.10E-05
   18       -0.7694829      -0.5229039      -0.2465790 0.15E-07 0.57E-06
   19       -0.7694829      -0.5229039      -0.2465790 0.54E-08 0.31E-06

  19 iterations: (units are Ry and bohr)
  Energy:      -0.76948289    Ebs:       0.52290387
  Eh:           0.34778629    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.750          -0.697205      0.474543      0.919118

  ---Non-local ghost testing---
  Local state: 1s

  Test  state: 1s
  ...seems like this angular momentum is the local part!
  ------------------------------

  No ghosts present for local potential



  ===============Configuration 2 AE Calc=============== 
    Core orbitals - 
    Valence orbitals - 
  |100>   0.500 -1.000 1  

  ---------Eigenvalue guesses---------
  1s            -1.00000000

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.5961216      -0.5000000      -0.0961216 0.00E+00 0.10E+01
    2       -0.5856248      -0.4868800      -0.0987448 0.26E-01 0.50E+00
    3       -0.5723469      -0.4707497      -0.1015972 0.33E-01 0.17E-01
    4       -0.5685992      -0.4668641      -0.1017352 0.83E-02 0.45E-02
    5       -0.5680290      -0.4662223      -0.1018067 0.14E-02 0.35E-03
    6       -0.5679349      -0.4660928      -0.1018421 0.28E-03 0.18E-03
    7       -0.5679120      -0.4660524      -0.1018596 0.87E-04 0.11E-03
    8       -0.5679075      -0.4660392      -0.1018683 0.28E-04 0.90E-04
    9       -0.5679019      -0.4660294      -0.1018725 0.21E-04 0.68E-04
   10       -0.5678968      -0.4660222      -0.1018746 0.15E-04 0.46E-04
   11       -0.5678931      -0.4660174      -0.1018757 0.10E-04 0.29E-04
   12       -0.5678907      -0.4660145      -0.1018762 0.63E-05 0.17E-04
   13       -0.5678891      -0.4660127      -0.1018764 0.38E-05 0.10E-04
   14       -0.5678882      -0.4660117      -0.1018765 0.22E-05 0.61E-05
   15       -0.5678877      -0.4660112      -0.1018766 0.12E-05 0.35E-05
   16       -0.5678874      -0.4660109      -0.1018766 0.67E-06 0.20E-05
   17       -0.5678873      -0.4660107      -0.1018766 0.37E-06 0.12E-05
   18       -0.5678872      -0.4660106      -0.1018766 0.20E-06 0.67E-06
   19       -0.5678871      -0.4660105      -0.1018766 0.11E-06 0.39E-06
   20       -0.5678871      -0.4660105      -0.1018766 0.51E-07 0.23E-06
   21       -0.5678871      -0.4660105      -0.1018766 0.27E-07 0.15E-06
   22       -0.5678871      -0.4660105      -0.1018766 0.24E-07 0.98E-07
   23       -0.5678871      -0.4660105      -0.1018766 0.75E-08 0.63E-07

  23 iterations: (units are Ry and bohr)
  Energy:      -0.56788709    Ebs:       0.46601049
  Eh:           0.16515056    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.500          -0.932021      0.434722      0.943329


  ===============Configuration 2 NL: Calc ===============
  ---------Eigenvalue guesses---------
  1s            -0.47720981
  Performing non-local pseudopotential calculation
  Using the s potential as the local potential

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.3269159      -0.2386049      -0.0883110 0.00E+00 0.10E+01
    2       -0.4393199      -0.3446770      -0.0946429 0.44E+00 0.50E+00
    3       -0.5536876      -0.4534831      -0.1002045 0.32E+00 0.96E-02
    4       -0.5651507      -0.4646774      -0.1004733 0.25E-01 0.40E-02
    5       -0.5662818      -0.4656465      -0.1006353 0.21E-02 0.28E-02
    6       -0.5663365      -0.4656185      -0.1007180 0.60E-04 0.21E-02
    7       -0.5662224      -0.4654651      -0.1007573 0.33E-03 0.76E-03
    8       -0.5661725      -0.4653964      -0.1007761 0.15E-03 0.20E-03
    9       -0.5661597      -0.4653749      -0.1007848 0.46E-04 0.20E-03
   10       -0.5661503      -0.4653616      -0.1007887 0.29E-04 0.14E-03
   11       -0.5661433      -0.4653528      -0.1007904 0.19E-04 0.93E-04
   12       -0.5661387      -0.4653475      -0.1007912 0.11E-04 0.57E-04
   13       -0.5661359      -0.4653444      -0.1007915 0.67E-05 0.33E-04
   14       -0.5661343      -0.4653427      -0.1007916 0.37E-05 0.19E-04
   15       -0.5661334      -0.4653417      -0.1007916 0.20E-05 0.10E-04
   16       -0.5661329      -0.4653413      -0.1007916 0.11E-05 0.57E-05
   17       -0.5661326      -0.4653410      -0.1007916 0.54E-06 0.31E-05
   18       -0.5661325      -0.4653409      -0.1007916 0.27E-06 0.16E-05
   19       -0.5661324      -0.4653408      -0.1007916 0.14E-06 0.87E-06
   20       -0.5661324      -0.4653408      -0.1007916 0.58E-07 0.46E-06
   21       -0.5661324      -0.4653408      -0.1007916 0.39E-07 0.25E-06
   22       -0.5661324      -0.4653408      -0.1007916 0.12E-07 0.13E-06
   23       -0.5661324      -0.4653408      -0.1007916 0.56E-08 0.69E-07

  23 iterations: (units are Ry and bohr)
  Energy:      -0.56613239    Ebs:       0.46534076
  Eh:           0.16375687    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.500          -0.930682      0.436536      0.895529

  ---Non-local ghost testing---
  Local state: 1s

  Test  state: 1s
  ...seems like this angular momentum is the local part!
  ------------------------------

  No ghosts present for local potential



  ===============Configuration 3 AE Calc=============== 
    Core orbitals - 
    Valence orbitals - 
  |100>   0.350 -1.000 1  

  ---------Eigenvalue guesses---------
  1s            -1.00000000

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.3887058      -0.3500000      -0.0387058 0.00E+00 0.70E+00
    2       -0.4026819      -0.3623204      -0.0403615 0.35E-01 0.35E+00
    3       -0.4173102      -0.3751685      -0.0421417 0.35E-01 0.28E-01
    4       -0.4179770      -0.3757198      -0.0422572 0.15E-02 0.12E-01
    5       -0.4176443      -0.3753171      -0.0423272 0.11E-02 0.98E-03
    6       -0.4175765      -0.3752126      -0.0423639 0.28E-03 0.50E-03
    7       -0.4175531      -0.3751705      -0.0423827 0.11E-03 0.25E-03
    8       -0.4175447      -0.3751525      -0.0423922 0.48E-04 0.13E-03
    9       -0.4175417      -0.3751446      -0.0423970 0.21E-04 0.13E-03
   10       -0.4175381      -0.3751386      -0.0423995 0.16E-04 0.94E-04
   11       -0.4175351      -0.3751343      -0.0424008 0.11E-04 0.63E-04
   12       -0.4175329      -0.3751314      -0.0424014 0.76E-05 0.39E-04
   13       -0.4175314      -0.3751297      -0.0424018 0.48E-05 0.23E-04
   14       -0.4175305      -0.3751286      -0.0424019 0.29E-05 0.14E-04
   15       -0.4175300      -0.3751279      -0.0424020 0.17E-05 0.77E-05
   16       -0.4175296      -0.3751276      -0.0424021 0.99E-06 0.43E-05
   17       -0.4175295      -0.3751273      -0.0424021 0.57E-06 0.23E-05
   18       -0.4175293      -0.3751272      -0.0424021 0.33E-06 0.13E-05
   19       -0.4175293      -0.3751272      -0.0424021 0.18E-06 0.68E-06
   20       -0.4175293      -0.3751271      -0.0424021 0.10E-06 0.36E-06
   21       -0.4175292      -0.3751271      -0.0424021 0.58E-07 0.20E-06
   22       -0.4175292      -0.3751271      -0.0424021 0.33E-07 0.11E-06
   23       -0.4175292      -0.3751271      -0.0424021 0.18E-07 0.66E-07
   24       -0.4175292      -0.3751271      -0.0424021 0.10E-07 0.38E-07
   25       -0.4175292      -0.3751271      -0.0424021 0.57E-08 0.22E-07

  25 iterations: (units are Ry and bohr)
  Energy:      -0.41752921    Ebs:       0.37512707
  Eh:           0.08335897    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.350          -1.071792      0.414987      0.919118


  ===============Configuration 3 NL: Calc ===============
  ---------Eigenvalue guesses---------
  1s            -0.47720981
  Performing non-local pseudopotential calculation
  Using the s potential as the local potential

  iter       Etot             Ebs             Ehxc       de_max   dv_max
    1       -0.2024293      -0.1670234      -0.0354059 0.00E+00 0.13E+01
    2       -0.2999960      -0.2614158      -0.0385802 0.57E+00 0.65E+00
    3       -0.4045130      -0.3630585      -0.0414546 0.39E+00 0.12E-01
    4       -0.4150318      -0.3734416      -0.0415901 0.29E-01 0.42E-02
    5       -0.4160569      -0.3743841      -0.0416728 0.25E-02 0.31E-02
    6       -0.4161286      -0.3744124      -0.0417162 0.76E-04 0.24E-02
    7       -0.4160967      -0.3743584      -0.0417383 0.14E-03 0.90E-03
    8       -0.4160736      -0.3743240      -0.0417495 0.92E-04 0.25E-03
    9       -0.4160667      -0.3743116      -0.0417551 0.33E-04 0.25E-03
   10       -0.4160612      -0.3743033      -0.0417578 0.22E-04 0.19E-03
   11       -0.4160570      -0.3742978      -0.0417592 0.15E-04 0.13E-03
   12       -0.4160541      -0.3742942      -0.0417599 0.95E-05 0.79E-04
   13       -0.4160523      -0.3742921      -0.0417602 0.58E-05 0.48E-04
   14       -0.4160512      -0.3742908      -0.0417604 0.34E-05 0.28E-04
   15       -0.4160506      -0.3742901      -0.0417605 0.20E-05 0.16E-04
   16       -0.4160502      -0.3742897      -0.0417605 0.11E-05 0.93E-05
   17       -0.4160500      -0.3742894      -0.0417606 0.63E-06 0.53E-05
   18       -0.4160499      -0.3742893      -0.0417606 0.35E-06 0.30E-05
   19       -0.4160498      -0.3742892      -0.0417606 0.19E-06 0.16E-05
   20       -0.4160498      -0.3742892      -0.0417606 0.11E-06 0.92E-06
   21       -0.4160497      -0.3742892      -0.0417606 0.59E-07 0.51E-06
   22       -0.4160497      -0.3742891      -0.0417606 0.32E-07 0.28E-06
   23       -0.4160497      -0.3742891      -0.0417606 0.18E-07 0.15E-06
   24       -0.4160497      -0.3742891      -0.0417606 0.97E-08 0.85E-07

  24 iterations: (units are Ry and bohr)
  Energy:      -0.41604973    Ebs:       0.37428913
  Eh:           0.08252601    Ex :       0.00000000

  Orbital    Filling       Eigenvalues    Norm(rc->oo)      Peak
  |100>      0.350          -1.069398      0.417556      0.872546

  ---Non-local ghost testing---
  Local state: 1s

  Test  state: 1s
  ...seems like this angular momentum is the local part!
  ------------------------------

  No ghosts present for local potential


  ======================================================================== 
  End TC calculation
  ======================================================================== 



Report File
==============================
It is convenient to generate a report file using the ``rpt`` command. 
The report command summarizes key information concerning the pseudopotential. 

The first part of the report file is a dump of the parameter file.

.. code-block::

  ##########################################################
  #    Opium Report File                                   #
  ##########################################################
      Opium version:        4.1

  ### copy of the parameter file #######################

  [Atom]
  H
  1
  100 1.00  -

  [Pseudo]
  1 1.40
  opt

  [Optinfo]
  5.50  4

  [XC]
  gga

  [Configs]
  3
  100 0.75 -

  100 0.50 -

  100 0.35 -

Next, the all-electron output is summarized.

.. code-block::

  ### AE report ########################################

  AE:Orbital    Filling       Eigenvalues[Ry]          Norm
     ----------------------------------------------------------
     100   1.000        -0.4772098146    0.5173254813

       E_tot =       -0.9178617009 Ry


Then the convergence error and ghost testing results are printed. The first column is the valence state, 
the second column is the convergence error per electron. Next, the 
error per electron is multiplied by the occupation of the state to yield
the convergence error in the reference state and is reported in mRy as well as meV. 
The last column states whether a ghost was found when this state was used as the local potential. 
If the ghost testing was inconclusive, a ``?`` will be printed. 

.. code-block::

  ### PS report ########################################

    ====================Optimized pseudopotential method====================

                       Pseudopotential convergence error                      
    Orbital      [mRy/e]       [meV/e]         [mRy]        [meV]        Ghost
    --------------------------------------------------------------------------
    100         0.422277      5.745376      0.422277     5.745376        no

                 Tot. error =               0.422277     5.745376


 Next, the NL test results are summarized. The ghost testing column in this table 
 shows whether one of the non-local potentials gives a ghost given the choice of local potential. 

.. code-block::

  ### NL/SL report #####################################


  NL:Orbital    Filling       Eigenvalues[Ry]          Norm       Ghost
     ------------------------------------------------------------------
     100        1.000         -0.4772098146    0.5173254872       no

     ========== No ghosts in potential!!========== 

     E_tot =       -0.9158837390 Ry


Finally, the transferability tests are summarized and the errors are computed and printed. 

The last section is the comparison of the change in energy between configuration ``i`` and ``j`` 
(configuration "0" is the reference) for the AE and NL atoms. 
This is another quantity that can be used to measure transferability. 

.. code-block::

  ### TC report ########################################

  AE:Orbital    Filling       Eigenvalues[Ry]          Norm
     ----------------------------------------------------------
     100        0.750         -0.6975395718    0.4737851899

                E_tot =       -0.7714294559 Ry

  NL:Orbital    Filling       Eigenvalues[Ry]          Norm       Ghost
     ------------------------------------------------------------------
     100        0.750         -0.6972051612    0.4745434506       no

                E_tot =       -0.7694828950 Ry

  AE-NL:Orbital Filling       Eigenvalues[mRy]         Norm[1e-3] 
  AE-NL- --------------------------------------------------------------
  AE-NL-    100   0.750          -0.3344105826      -0.7582606862  
  AE-NL-  total error =           0.3344105826       0.7582606862

  =====================================================================
  AE:Orbital    Filling       Eigenvalues[Ry]          Norm
     ----------------------------------------------------------
     100        0.500        -0.9320209753     0.4347217899

               E_tot =       -0.5678870869 Ry

  NL:Orbital    Filling       Eigenvalues[Ry]          Norm       Ghost
     ------------------------------------------------------------------
     100        0.500        -0.9306815122     0.4365364038       no

        E_tot =       -0.5661323887 Ry

  AE-NL:Orbital Filling       Eigenvalues[mRy]         Norm[1e-3] 
  AE-NL- --------------------------------------------------------------
  AE-NL-    100   0.500         -1.3394630445       -1.8146139118  
  AE-NL-  total error =          1.3394630445        1.8146139118

  =====================================================================
  AE:Orbital    Filling       Eigenvalues[Ry]          Norm
    ----------------------------------------------------------
     100        0.350        -1.0717916312     0.4149873487

               E_tot =       -0.4175292096 Ry

  NL:Orbital    Filling       Eigenvalues[Ry]          Norm       Ghost
     ------------------------------------------------------------------
     100        0.350        -1.0693975109     0.4175563421       no

        E_tot =       -0.4160497262 Ry

  AE-NL:Orbital Filling       Eigenvalues[mRy]         Norm[1e-3] 
  AE-NL- --------------------------------------------------------------
  AE-NL-   100    0.350          -2.3941203051      -2.5689934304  
  AE-NL- total error =            2.3941203051       2.5689934304

  =====================================================================

    Comparison of total energy differences.           
    DD_ij = (E_i - E_j)_AE - (E_i-E_j)_NL     

  AE-NL-   i   j         DD[mRy]        DD[meV] 
  AE-NL- ------------------------------------------
  AE-NL-   0   1        -0.031401      -0.427230
  AE-NL-   0   2        -0.223264      -3.037658
  AE-NL-   0   3        -0.498478      -6.782148
  AE-NL-   1   2        -0.191863      -2.610428
  AE-NL-   1   3        -0.467078      -6.354917
  AE-NL-   2   3        -0.275215      -3.744490