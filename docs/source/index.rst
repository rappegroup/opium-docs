.. opium documentation master file, created by
   sphinx-quickstart on Sun Aug 25 23:13:36 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

OPIUM - Pseudopotential Generation Project
===================================================

.. image:: OPIUM-dark.png
   :scale: 22 %
   :align: center
   :alt: OPIUM logo
   

OPIUM [#]_ is a program used to generate pseudopotentials for use in
first-principles calculations. The current stable release is v4.1.0, which
is available `here <https://github.com/rappegroup/opium>`_.

At present, OPIUM is capable of the following:

* Scalar-relativistic, fully-relativistic, and non-relativistic pseudopotential generation 
* Support for LDA, GGA, Hartree-Fock, hybrid [#]_, and range-separated hybrid [#]_ exchange-correlation (XC) functionals
* Constructing Optimized [#]_ (RRKJ) or Kerker [#]_ pseudopotentials 
* Partial core correction of Louie, Froyen and Cohen [#]_
* Test and generate and test pseudopotentials that support semicore states
* Applying augmentation operators designed non-local method of Ramer and Rappe [#]_
* Ghost state checking following the method suggested by Gonze, Stumpf, and Scheffler [#]_
* Automatic plotting of wave functions, potentials, and density using xmgrace 

OPIUM also supports the following output formats:

* ``.upf`` - Quantum ESPRESSO
* ``.recpot`` - CASTEP
* ``.fhi`` - ABINIT, JDFTx
* ``.ncpp`` - PWSCF
* ``.pwf`` - Bh
* ``.cpi`` - FHI98md

Legacy information regarding OPIUM can be found on the `old sourceforge page <https://opium.sourceforge.net/>`_.

.. [#] Open-source Pseudopotential Interface / Unification Module (OPIUM)
.. [#] Yang, Jing, Liang Z. Tan, and Andrew M. Rappe. "Hybrid functional pseudopotentials." *Physical Review B* 97.8 (2018): 085130.
.. [#] Yang, Yang, et al. "Range-separated hybrid functional pseudopotentials." *Physical Review B* 108.16 (2023): 165142.
.. [#] Rappe, Andrew M., et al. "Optimized pseudopotentials." *Physical Review B* 41.2 (1990): 1227.
.. [#] Kerker, G. P. "Non-singular atomic pseudopotentials for solid state applications." *Journal of Physics C: Solid State Physics* 13.9 (1980): L189.
.. [#] Louie, Steven G., Sverre Froyen, and Marvin L. Cohen. "Nonlinear ionic pseudopotentials in spin-density-functional calculations." *Physical Review B* 26.4 (1982): 1738.
.. [#] Ramer, Nicholas J., and Andrew M. Rappe. "Designed nonlocal pseudopotentials for enhanced transferability." *Physical Review B* 59.19 (1999): 12471.
.. [#] Gonze, Xavier, Peter Käckell, and Matthias Scheffler. "Ghost states for separable, norm-conserving, ab initio pseudopotentials." *Physical Review B* 41.17 (1990): 12264.


.. toctree::
   :maxdepth: 1
   :caption: Contents


   introduction
   usage
   citations
   changelog
   contact

