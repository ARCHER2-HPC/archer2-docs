# Main differences between ARCHER2 4-cabinet system and ARCHER2 full system

This section provides an overview of the main differences between
the ARCHER2 4-cabinet system that all users have been using up until
now and the full ARCHER2 system along with links to more information where 
appropriate.

## For all users

- There are 5860 compute nodes in total on the full ARCHER2 system rather 
  than just the 1024 on the 4-cabinet ARCHER2 system.
- Of the 5860 compute nodes, 584 are *high memory nodes*, these nodes have
  512 GiB of memory rather than the 256 GiB available on standard memory
  nodes.
- There are two data analysis nodes available and these are shared by multiple
  users. When using these nodes, users typically request the number of cores
  and the amount of memory they require (unlike compute nodes where you always
  have access to all the cores and all the memory on a node and do not share
  with any other users). See [the Data Analysis section of the User Guide](../user-guide/analysis.md)
  for more information.
- Software is provided by the [Lmod](https://lmod.readthedocs.io/) module
  system (on the 4-cabinet system, TCL environment modules are used instead). Many
  commands are similar but there are some differences, see 
  [the Software Environment section of the User Guide](../user-guide/sw-environment.md)
  for more information.
- Not all versions of all software have been ported over to the full system from
  the 4-cabinet system, some older versions of software have not been installed.
- The scheduler layout has been expanded to provide more functionality and 
  flexibility. You can find details of the new QoS available in
  [the Submitting Jobs on ARCHER2 section of the User Guide](../user-guide/scheduler.md).
- You no longer need to specify `--reservation=shortqos` when using the 
  `short` QoS.
- Jobs running in a reservation can now run for longer than the maximum wall time available
  in any of the normal QoS defined in the scheduler. Reservations must use the `reservation`
  QoS.
- You should no longer add the `module load epcc-job-env` command to job submission
  scripts.

## For users compiling and developing software on ARCHER2

- The HPE Programming Environment (PE) version has been updated to the
  21.04 release with the 21.09 release also available. These releases make
  newer versions of compilers and MPI libraries available.
- The change to Lmod modules means that some modules are hidden by default
  until dependencies have been loaded (for example, you will not be able
  to load the `cray-netcdf` or `cray-netcdf-hdf5parallel` modules until
  you have loaded the appropriate `cray-hdf5` or `cray-hdf5-parallel` modules).
  You can use the `module spider` command to see all available modules, including
  hidden ones.
- There are two MPI transport layers available: OpenFabrics (OFI) and UCX.
  In some cases, you may see performance and/or scaling improvements by switching
  to UCX rather than the default OFI transport layer. For more information on
  when to switch and how to switch, see
  [the Application Development Environment section of the User Guide](../user-guide/dev-environment.md)
