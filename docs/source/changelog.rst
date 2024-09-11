Changelog
========================================

1.0.2
-------------

#. Further improvement of handling of s,p,d ordering problem
#. Code should be totally case insensitive (all strcmp's replaced with strcasecmp).
#. Param file appended to fhi, ncpp files (thanks Wissam!)
#. Plot help 'vi' example corrected.
#. Added fclose() in do_nl.c (thanks Keith!)
#. Configure script modified to allow LDFLAGS to be appended from command line
#. Fixed spelling of Schrodinger in descreen.f
#. AE and PS eigenvalues were actually reversed in descreen.f (they are identical so no big deal).
#. Fixed bug in nllogd for KB log-derivative (missed common block)
#. Adjusted do_logplt so 'ae ps nl' need not be repeated to make plot (thanks Rongqin!)
#. Added line for refernece energy in logder plot (thanks Rongqin!)
#.  Modified treatment of unbound states to work with semicore (thanks Sara!)
#. Modified output for .ncpp and .fhi to indicate PW XC correctly
#. Changed MacOSX configure defs so -r8 included by default
#. Major modification of ghost state reporting, now states whether ghost testing was inconclusive (no bound states for local potential)
#. Ghost testing added for unbound states as local or non-local
#. Interp.f output cleaned up
#. Logder plotting states that no [loginfo] key block is set (not just a core dump!)
#. Fixed legends for plots that were not correct

1.0.3
---------------

#. Ghost testing fixed, removed the '?' output if a local state was not bound. Now this eigenvalue is treated as if its eigenvalue = 0.0
#. Changed the way an unbound state is selected.  Now, if the filling is set to <-1.0e-12, the state is treated as unbound (and treated as if it is empty). The eigenvalue is fixed by the guess or set to 0.0 if no guess specified.
#. Added the .recpot output format routines (contributed by KeithRefson). This format is native to the solid state code CASTEP.
#. Added GNU license information, license header prepended to every .f .c and .h file and added a "COPYING" file which contains the full-text of the license.
#. Fixed bug related to DNL in the PWF format (introduced in the previous version).
#. Added the Fuchs + Scheffler partial core correction method which is implemented in fhi98pp.
#. Fixed bug related to labeling the eigenvalues in the logdplot
#. Changed keyblock help to reflect #2 and #6 above.
#. Configure script changed such that ifort and ifc libcxa gets statically linked (requested by Keith Refson).
#. Added true NULL characters to nwrite calls in writepwf for better compatibility.


2.0.1
---------------

#. Troullier-Martins potential method added 
#. Cleaned up plotting output
#. Added calculation of the convergence of the kinetic energy error, output is sent to both rpt and log file
#. Can now plot the KE convergence
#. Explicit announcement of the presence of ghost states in the rpt file
#. Changed configure script to generate ONE set of makevars such that users can edit them if configure fails
#. Added listing of compile and execution platform in log file
#. Fixed some errors in the element symbol table
#. Increased number of keyblock reads so that keyblocks ending in string reads don't screw things up.
#. Added the ability to set an environmental variable called $XMGRACE_OPTS which is included in the command-line call to xmgrace.
#. Increased the dimensions of all parameters such that 5f elements can be constructed


2.0.2
---------------

#. Bug fix for ncpp (PWSCF) style output.  Wavefunctions had wrong number of r's in 1.0.2, 1.0.3, and 2.0.1. See doc/README_solidstatecodes for more information.


2.0.3
----------------

#. Bug fix for f-projectors in .pwf output format
#. Applied patch from Keith Refson to repair .recpot output format
#. Some (but not all) sprintf statements changed to snprintf
#. BETA level implementation of .psf (siesta) format and .cpmd (CPMD) output format, see 'opium -c' for more information
#. GGA functional allows for smoothing near the origin (see 'opium -k' for how to invoke).
#. Corrections to KE convergence output when semicore states are involved.  Now the KE convergence of all valence orbitals is computed.

2.0.4
----------------

#. .pwf output file now contains the wavefunctions from the MOST RECENT configuration at the time of generation. This is done since often the reference is not the desired ground state atomic configuration.
#. Included the rudimentary emacs OPIUM mode file "_emacs_mode". This can be appended to ones ~/.emacs init file to included highlighting of the acceptable keyblocks.

2.0.5
----------------

#. Bugfix for new .pwf output (eigenvalues match the configuration used for the output wavefunctions).
#. Useful information (compile and execution host, os, date, version, and command line used for psp generation) written at the end of most psp output file types.
#. Bugfix in do_ke.c and kcomp.F (dimensioning error)
#. Bugfix for 'all' command -- no longer attempts 'fc' step.

2.0.6
----------------

#. 2.0.5 left the core-correction out of .pwf format.  This is now fixed.

3.0
----------------

#. Change in versioning scheme (3 numbers is too many).
#. Removal of relativistic grid, now srl potentials use the same grid as the nrl grid.  This means that the following routines have been removed: (atm.F, difrel.F, etotal.F, dsolv2.F, velect.F)
#. Ability to perform unbound states in a srl potential has been added
#. A modified version of the Hartree-Fock code by C. Froese-Fischer and G. Gaigalas has been incorporated into OPIUM to construct HF psps.
#. A modified version of the Dirac-Fock code by A.L. Ankudinov, S.I. Zabinsky, J.J. Rehr has been incorporated into OPIUM to construct srl HF psps.
#. Two new routines, hfindesc.F and hfsmooth.F have been added to pseudo/ (hfindesc.F, hfsmooth.F)
#. Each psp and ps-wavefunction is written to its own file (labeled by .l=<nnn>).  This allows for one to (for now by hand) use a different reference state for each psp.
#. Most plots have been changed to show more construction info in the title and subtitle.
#. Submissions from K. Refson have been incorporated (adds a partial core correction to the .recpot format).
#. W. A. Al-saidi added the .champ format for the QMC CHAMP code.
#. .casino format added for the QMC CASINO code (not thoroughly tested)
#. Internal changes to redundant fortran common blocks.  Now much fortran info is in the aorb, adat, and aval common blocks.
#. Default grid now extends to 120 au. 
#. Ability to do semi-local tests in DFT resurrected.  For now, HF tests can only be done via "sl", trying "nl" throws an warning and reverts to sl.  The "tc" command performs "nl" tests for DFT psps and "sl" tests for HF psps.
#. The Wu-Cohen GGA and Perdew-Wang 91 GGA functionals were added to excorr.
#. GGA XC functionals are changed to LDA smoothly starting at 0.001 bohr and ending at 0.0 bohr.  This is due to heavier atoms resulting in "spikes" occurring near r=0.0 (this comes from the GGA enhancement terms behaving badly where the density gets very small).  This is only done automatically for Z>36, for Z<=36 no smoothing is done.
#. The behavior of unbound states has changed.  Now a occupation number < 0 signals that the state should be treated as unbound.  If the state is NOT marked as unbound and the solver can't find a bound state, the run fails (it used to mark such a state as unbound and keep going).  If the state is constructed as unbound and a test case DOES NOT indicate and unbound state, it will use the psp to try and find a bound state.
#. Instead of having each part of the code figure out the correct states for a semicore calculation, the parameter.c routine now REORDERS the param file on the fly to put all states in s,p,d,f,s,p,d,f etc. order, i.e. the first instance of each 'l' is put in increasing 'l' order, then the next instance of each 'l' is put in increasing 'l' order, etc. until there are no more valence states. The Atom, Configs, Pseudo, and Optinfo keyblocks are all reordered the same way. 


3.1
----------------

#. .ncpp output no longer lists "-1" electrons when a state is unbound.
#.  ghost reporting in .rpt fixed (used to always say "no ghosts")


3.2
----------------

#. Cleanup of src/atom/average.f 
#. Scalar relativistic averaging now defaults to being done correctly for GGA XC potentials. Previous versions of OPIUM would average the rel-wavefunctions from infinity -> rc for each channel.  This is ok and still the default for LDA, however, for GGA ALL wavefunctions must be averaged down to the same radius.  This is the minimum radius by default.
#. Due to the change in the srl averaging mentioned in the previous change, a new keyblock "[Average]", has been add to allow users to try the old behavior on GGA potentials.
#. A new string variable has been added to the [Relativity] keyblock. This string refers to whether the relativistic corrections to the XC functional are used or not. If the value is "rxc" then the corrections are used, if it is "nxc" then they are not used.  If the calculation is not relativistic, this is ignored.
#. The eigenvalue guesses supplied in the param file are ignored for atomic SRL calculations. This was always done prior to version 3.0, the srl solve is not robust enough to take a poor user guess and still converge in all cases.
#. A small bug in the smoothing of the GGA XC functional has been corrected. Now the  correct transition to LDA XC is done as you approach the origin from the smoothing radius.  
#. A newer GGA XC smoothing method is introduced which simply fits the XC potential to a quadratic near some user defined radius and then continues the analytic form back to the origin. To access this smoothing method, simply make the GGA smoothing radius negative.
#. A semilocal solve and test can be done instead of the non-local solve from the command line. See "opium -c" for more info.
#. The bug which caused a command line like: opium mn log ae nl fhi to core dump (the 'ps' step is missing ; this is necessary for psps using potentials from different reference states) has been fixed.
#. An update of the "champ" output has been done. The header was changed, and instead of interpolating onto the unshifted grid from the shifted grid, the  shifted grid output is just written out.
#. Changed .ncpp output to no longer print element name after XC functional due to conflict between "HF" - hartree-fock and "Hf" - Hafnium.
#. The ability to use augmentation functions has been added to "ncpp" output
#.  An error is now thrown if the SIESTA output is tried for a psp with augmentation functions. Also, a warning is written about the fact that the SIESTA code redefines the form of the local potential within the target calculation.


3.3
----------------

Bugfix release:

#. In version 3.2, the relativistic XC corrections were NOT applied to the test pseudo (NL or SL) configurations. This bug does not effect the reference configuration or the output pseudo files (.fhi, .pwf etc.) This bug was also pretty small, most changes are less than 1 mRy in the transferability error.
#. Added a new variable to the [HFsmooth] keyblock to allow for less strict tolerances on the conservation of the eigenvalues when smoothing the HF potentials.


3.4
----------------

#. Patch to enable partial core corrections for the .recpot (castep) format


3.5
----------------
#. Fixed casino output - Significant help from William Parker
#. PBEsol xc functional added by William Parker
#. precision on xmgrace files increased
#. modified logplt to plot semi-core correctly
#. cpmd output fixed for DNL
#. worked more on gfortran compatibility


3.6
----------------

#. Added fully-relativistic psps -- needed significant modifications which resulted in many routines needed changing
#. Restored most if not all of the support for separate command line action (>>opium al log ae ; >>opium al log ps ; etc..) which had been lost in previous releases.
#. Implemented QuantumESPRESSO's upf version 1 psp output.  Version 2 is in progress.
#. Added DNL modifications from Ben Truscott
#. Added FlexLib corrections submitted by Keith Refson 


3.7
----------------

#. Added "teter" output which is format usable for frl/spin-orbit in ABINIT
#. Added "upf" (version 1) output which can be used for Quantum-Espresso (including frl/spin-orbit calculations)
#. Added "qso" output which is suitable for "QBOX" calculations
#. Added "QSOMesh" keyblock to define a linear mesh upon which the psp can be defined
#. Cleaned up a number of warning and error message output
#. Changed the printing in pwf and recpot output so gfortran will now work.


3.8 
----------------

#. Restored the PCC version number for the recpot output.
#. Added PCC for the QE upf output.
#. Cleaned up a lot of the printing errors for the gfortran version.


4.0
----------------

#. PBE0 density functional pseudopotentials can now be constructed , but only for non-relativitic atoms.
#. Documentation is updated, including the undocumented computational flags usage
#. HF pseudopotentials are available for Quantum ESPRESSO use. 

4.1
----------------

#. Add designed non-local transformation to PBE0 pseudopotentials generation
#. Fix bugs for some convergence problems
