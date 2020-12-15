# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds.


## Singularity
The issue concerns the building of a cmake-compiled code in bind mode. This fails because CMake 3.x, running within the container on a UAN, cannot find the MPI libraries (cray-mpich) on the host despite the correct paths having been provided.
