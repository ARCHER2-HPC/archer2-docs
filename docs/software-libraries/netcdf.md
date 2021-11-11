# NetCDF

The Network Common Data Form NetCDF
(and its parallel manifestation NetCDF parallel) is a standard library
and data format developed and supported by [UCAR](https://www.unidata.ucar.edu/software/netcdf/)
is released under a BSD-like license. 

Both serial and parallel versions are available on ARCHER2 as
standard modules:

  + `module load cray-netcdf` (serial version)
  + `module load cray-netcdf-hdf5parallel` (MPI parallel version)

Note that one should first load the relevant HDF module file, e.g.,
```
$ module load cray-hdf5
$ module load cray-netcdf
```
for the serial version.

Use `module spider` to locate available versions, and use `module help` to
locate `cray-`specific release notes on a particular version.

Known issues:

=== "Full system"
    
    + There is currently a problem with the module file which means
      `cray-netcdf-hdf5parallel` will not operate correctly in `PrgEnv-aocc`.
      One can load module `epcc-netcdf-hdf5parallel` instead as a work-around
      if `PrgEnv-aocc` is required.

=== "4-cabinet system"
    
    + There are no currently known issues. 

Some general comments and information on serial and parallel I/O 
to ARCHER2 are given in the section on
[I/O and file systems](../user-guide/io.md).


## Resources

The NetCDF [home page](https://www.unidata.ucar.edu/software/netcdf/).
