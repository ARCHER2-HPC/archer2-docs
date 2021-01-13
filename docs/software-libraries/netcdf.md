# NetCDF

  - Provides: NetCDF, PnetCDF
  - Access: 
    + `module load cray-hdf5` (serial version)
    + `module load cray-hdf5-parallel` (MPI parallel version)

Network Common Data Form
[NetCDF](https://www.unidata.ucar.edu/software/netcdf/), NetCDF built
on top of parallel HDF5 and parallel NetCDF (usually referred to as
[PnetCDF](https://parallel-netcdf.github.io/wiki/Documentation.html))
are available.

For configuration options, including appropriate settings for the
parallel Lustre file system, please see	the [Recommended ARCHER2 I/O
Settings](/user-guide/io##recommended-archer2-io-settings).
