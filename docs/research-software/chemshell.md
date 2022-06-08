# ChemShell

ChemShell is a script-based chemistry code focusing on hybrid QM/MM
calculations with support for standard quantum chemical or force field
calculations. There are two versions: an older Tcl-based version
Tcl-ChemShell and a more recent python-based version Py-ChemShell.

The advice from <https://www.chemshell.org/licence> on the difference
is:

> We regard Py-ChemShell 19.0 as suitable for production calculations on
> materials systems, although you will find its feature set is more
> limited than Tcl-ChemShell. We do not currently recommend Py-ChemShell
> for calculations on biological systems, as automated import of
> biomolecular force fields is scheduled for a future release.

## Useful Links

  - ChemShell home page <https://www.chemshell.org>
  - ChemShell documentation <https://www.chemshell.org/documentation>
  - ChemShell forums <https://www.chemshell.org/forum>

## Using ChemShell on ARCHER2

!!! warning
    The python-based version of ChemShell is not yet available on 
    ARCHER2

The python-based version of ChemShell is open-source and is freely
available to all users on ARCHER2.

The older version of Tcl-based ChemShell requires a license. Users with
a valid license should request access via the ARCHER2 SAFE.

## Running parallel ChemShell jobs

The following script will run a pure MPI Tcl-based ChemShell job using 8 
nodes (128x8 cores).

=== "Full system"
    We are working with the ChemShell developers to make ChemShell available 
    on the full system as soon as possible.
