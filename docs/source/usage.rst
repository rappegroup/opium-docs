Execution Type and Commands
===================================================

Non-interactive execution 
------------
Non-interactive execution is the preferred method of running OPIUM. It is called
using the following:

.. code-block:: console

   $ ./opium input.param output.txt command1 command2 ...

Where

* ``input.param`` is a parameter file, with the necessary keyblocks.
* ``output.txt`` is the output file to be created.
* ``command1``, ``command2``, etc are different :ref:`Commands<Commands>` that can be executed.

For example, given some parameter file ``c.param``, to do the all-electron (AE) solve, generate the pseudopotential, 
generate a report, and write the output to a ``.upf`` file for quantum ESPRESSO, one should execute the following command:

.. code-block:: console

   $ ./opium c.param output.txt ae ps rpt upf

As of the 1.0.1 release, ``.param`` is implied, so it is not required to be specified.


Interactive execution 
------------

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
Interactive mode also has some commands that are not available normally:

* ``v`` - toggles verbosity flag 
* ``comm`` - command line help
* ``plot`` - plotting help
* ``keys`` - keyblock help
* ``exit`` - exits interactive mode

Commands
------------


Parameter Keyblocks
========================================


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