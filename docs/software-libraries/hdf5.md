# HDF5

The Hierarchical Data Format HDF5
(and its parallel manifestation HDF5 parallel) is a standard library
and data format developed and supported by
[The HDF Group](https://www.hdfgroup.org/), and
is released under a BSD-like license. 

Both serial and parallel versions are available on ARCHER2 as
standard modules:

  + `module load cray-hdf5` (serial version)
  + `module load cray-hdf5-parallel` (MPI parallel version)

Use `module help` to locate `cray-`specific release notes on a
particular version.

Known issues:

=== "Upgrade 2023"

    + There are no currently known issues.

=== "Full system"
    
    + There is currently a problem with the module file which means
      `cray-hdf5-parallel` will not operate correctly in `PrgEnv-aocc`.
      One can load module `epcc-cray-hdf5-parallel` instead as a work-around
      if `PrgEnv-aocc` is required.

=== "4-cabinet system"
    
    + There are no currently known issues. 

Some general comments and information on serial and parallel I/O 
to ARCHER2 are given in the section on
[I/O and file systems](../user-guide/io.md).


## Resources

The HDF5 support website includes [general documentation](https://support.hdfgroup.org/documentation/index.html).

For parallel HDF5, some [tutorials and presentations](https://support.hdfgroup.org/documentation/hdf5-docs/hdf5_topics/ParallelHDF5.html) are available.