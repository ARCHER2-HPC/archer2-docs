# ARCHER2 Test and Development System (TDS) user notes

The ARCHER2 Test and Development System (TDS) is a small system used for testing
changes before they are rolled out onto the full ARCHER2 system. This page 
contains useful information for people using the TDS on its configuration and
what they can expect from the system.

!!! important
    The TDS is used for testing on a day to day basis. This means that nodes and
    the entire system may be made unavailable or rebooted with little or no warning.

## TDS system details

 - Login node: 2x 64-core AMD EPYC 7742, 512 GB RAM
 - Compute nodes: 8 compute nodes in total
    + 4 standard memory compute nodes: 2x 64-core AMD EPYC 7742, 256 GB RAM
    + 4 high memory compute nodes: 2x 64-core AMD EPYC 7742, 512 GB RAM

 - Slingshot interconnect

 - Storage:
    + /home file system: shared with ARCHER2 main system
    + /work file system: 224 TiB Lustre (2x MDT, 2x OST)

## Slurm scheduler configuration

 - Paritions available:
    + `workq`: includes all compute nodes
 - QoS available:
    + `normal`: no per-user or per-project limits

## Known issues/notes

 - Software modules
    + HPE Cray Programming Environment 22.12
    + ARCHER2 CSE service modules available but not all software installed on TDS `/work` file system - i.e. you may be able to load a module but the software it points to may not be available. Check if the software is actually installed before trying to use it.

 - GCC 12.2.0 (gcc/g++/gfortran) compiler has been shown to give incorrect numerical results for a number of software packages (VASP, CASTEP. CP2K). If you want to use this compiler version we recommend checking output carefully. We may remove this version from the PE software stack installed on the full system as part of the software upgrade.

