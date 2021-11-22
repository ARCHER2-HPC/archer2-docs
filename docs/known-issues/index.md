# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

## Open Issues

### Dask Python package missing dependencies (Added: 2021-11-22)

The Dask Python package is missing some dependencies on the latest Programming
Environment (21.09). This can be worked around either by using the default
Programming Environment (21.04), or by following the [instructions](https://docs.archer2.ac.uk/user-guide/python/#adding-your-own-packages)
to install dask in your own user space.

### Warning when compiling Fortran code with CCE and MPI_F08 interface (Added: 2021-11-18)

- **Systems affected:** ARCHER2 full system

When you compile Fortran code using the MPI F08 interface (i.e. `use mpi_f08`) using the default version
of CCE (11.0.4) you will see warnings similar to:

```
  use mpi_f08
      ^       
ftn-1753 crayftn: WARNING INTERFACE_MPI, File = interface_mpi_mod.f90, Line = 8, Column = 7 
  File "/opt/cray/pe/mpich/8.1.4/ofi/cray/9.1/include/MPI_F08.mod" containing [sub]module information for "MPI_F08" was created with a previous compiler release.  It will not be supported by the next major release.  It is version 110 from release 9.0.
```

These warnings can be safely ignored as they do not affect the functioning of the code. If
you wish to avoid the warnings, you can compile using the more recent CCE version (12.0.3)
on the system. To switch to this version, use `module load cpe/21.09` from the default
environment on ARCHER2.

### Occasionally larger jobs (requiring greater than 128 nodes) will run slow or fail (Added: 2021-11-18)

- **Systems affected:** ARCHER2 full system

The CSE team has seen instances when larger jobs (those involving more than 128 nodes) will run slow (perhaps two or three times slower than normally) or not finish (because the slowness causes a time-out). In system testing, we saw this problem for a small number of applications (most notably, LAMMPS) on around 10--20% of jobs: the issue worsens as the job size increases.

The issue is being actively investigated by HPE, as a priority. At this time, there is no known work-around, though it is usually possible to resubmit the affected job successfully.

### Error message: `No space left on device` (Added: 2021-07-20)

- **Systems affected:** ARCHER2 4-cabinet system

Following an issue with te Lustre file system, there may be a number of files on the
system that have stale active file handles on the Lustre server. Attempts to overwrite
or append to these files may lead to error messages such as:

```
No space left on device
```

even though there are no issues with storage quotas. There are two potential workarounds
for this issue:

1. Remove the offending file(s) (e.g. using the `rm` command)
2. Work in a different directory or write to a different file name so you are not trying
   to overwrite or append to the offending file(s)

### PETSc fails when used on more than one node (Added: 2021-06-21)

- **Systems affected:** ARCHER2 4-cabinet system

There is a bug in the default HPE Cray MPICH which leads to failures from PETSc
when running on more than one node.

**Workaround:** switch to a newer version of HPE Cray MPICH. To do this, modify
your job submission script to add the following lines after all your other
`module` commands but before you use `srun` to run the executable:

```
module swap cray-mpich cray-mpich/8.1.3
export LD_LIBRARY_PATH=${CRAY_LD_LIBRARY_PATH}:${LD_LIBRARY_PATH}
```

### HPE Cray `perftools` modules not available by default (Added: 2021-04-27)

- **Systems affected:** ARCHER2 4-cabinet system

The HPE Cray `perftools` modules are no longer available by default on login to
ARCHER2 or on the compute nodes when you run a job. This is being investigated
and we hope to fix the issue soon.

**Workarounds** You can access the `perftools` modules by restoring a different
compiler environment or by switching to a different Programming Environment
release.

*Option 1: Restoring a different compiler environment*

On an ARCHER2 login node you can make the `perftools` modules available with
a command such as:

```
module restore -s PrgEnv-gnu
```

If you need to use the `perftools` modules with the default Cray compilers then
you must first switch to a different compiler suite and then back to the Cray
compiler suite:

```
module restore -s PrgEnv-gnu
module restore -s PrgEnv-cray
```

*Option 2: Switch to a different Programming Environment (PE) release*

You can also restore the `perftools` modules by switching to a different PE
release. For example, if you want to use the Cray compiler suite with the
21.03 PE release, you would use:

```
module load cpe/21.03
```

This would make the `perftools` modules available. More information on
using different PE releases [is available in the User and Best Practice Guide](../user-guide/dev-environment.md#switching-to-a-different-hpe-cray-programming-environment-release)


### Research Software

- **Systems affected:** ARCHER2 full system, ARCHER2 4-cabinet system

There are several outstanding issues for the centrally installed Research Software:

- **PyChemShell** is not yet available. We are working with the code developers to address this.
- **PLUMED** is not yet available. Currently, we recommend affected users to install a local version of the software.

Users should also check individual software pages, for known limitations/ caveats, for the use of software on the Cray EX platform and Cray Linux Environment.

### `stat-view` not working

- **Systems affected:** ARCHER2 4-cabinet system

The `stat-view` utility from the `cray-stat` module does not currently
work due to missing dependencies within the HPE Cray software stack. If you
try to use the tool, you will see errors similar too:

```
auser@uan01:~> module load cray-stat
auser@uan01:~> stat-view
Traceback (most recent call last):
File "/opt/cray/pe/stat/4.7.1/lib/python3.6/site-packages/STATview.py", line 60, in <module>
import xdot
ModuleNotFoundError: No module named 'xdot'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
File "/opt/cray/pe/stat/4.7.1/lib/python3.6/site-packages/STATmain.py", line 73, in <module>
raise import_exception
File "/opt/cray/pe/stat/4.7.1/lib/python3.6/site-packages/STATmain.py", line 40, in <module>
from STATGUI import STATGUI_main
File "/opt/cray/pe/stat/4.7.1/lib/python3.6/site-packages/STATGUI.py", line 37, in <module>
import STATview
File "/opt/cray/pe/stat/4.7.1/lib/python3.6/site-packages/STATview.py", line 62, in <module>
raise Exception('STATview requires xdot\nxdot can be downloaded from https://github.com/jrfonseca/xdot.py\n')
Exception: STATview requires xdot
xdot can be downloaded from https://github.com/jrfonseca/xdot.py
```

The current workaround is to use `/work/y02/shared/stat-view-workaround/stat-view` instead.

### Issues with RPATH for non-default library versions

- **Systems affected:** ARCHER2 full system, ARCHER2 4-cabinet system

When you compile applications against non-default versions of libraries within the HPE
Cray software stack and use the environment variable `CRAY_ADD_RPATH=yes` to try and encode
the paths to these libraries within the binary this will not be respected at runtime and
the binaries will use the default versions instead.

The workaround for this issue is to ensure that you set:

```
export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
```

at both compile and runtime. For more details on using non-default versions of libraries,
see [the description in the User and Best Practice Guide](../user-guide/dev-environment.md#using-non-default-versions-of-hpe-cray-libraries-on-archer2)


### Memory leak leads to job fail by out of memory (OOM) error (Updated: 2021-04-26)

- **Systems affected:** ARCHER2 4-cabinet system

Your program compiles and seems to run fine, but after some time (at least 10
minutes), it crashes with an out-of-memory (OOM) error. The job crashes more
quickly when run on a smaller number of nodes.

The workaround for this issue is to use the newer HPE Cray Programming Environment
release 21.03. Instructions on using a non-default programming environment release
[are available in the User and Best Practice Guide](../user-guide/dev-environment.md#switching-to-a-different-hpe-cray-programming-environment-release)

### MPI `UCX ERROR: ivb_reg_mr`

- **Systems affected:** ARCHER2 4-cabinet system

If you are using the UCX layer for MPI communication you may see an error such as:

```
[1613401128.440695] [nid001192:11838:0] ib_md.c:325 UCX ERROR ibv_reg_mr(address=0xabcf12c0, length=26400, access=0xf) failed: Cannot allocate memory
[1613401128.440768] [nid001192:11838:0] ucp_mm.c:137 UCX ERROR failed to register address 0xabcf12c0 mem_type bit 0x1 length 26400 on md[4]=mlx5_0: Input/output error (md reg_mem_types 0x15)
[1613401128.440773] [nid001192:11838:0] ucp_request.c:269 UCX ERROR failed to register user buffer datatype 0x8 address 0xabcf12c0 len 26400: Input/output error
MPICH ERROR [Rank 1534] [job id 114930.0] [Mon Feb 15 14:58:48 2021] [unknown] [nid001192] - Abort(672797967) (rank 1534 in comm 0): Fatal error in PMPI_Isend: Other MPI error, error stack:
PMPI_Isend(160)......: MPI_Isend(buf=0xabcf12c0, count=3300, MPI_DOUBLE_PRECISION, dest=1612, tag=4, comm=0x84000004, request=0x7fffb38fa0fc) failed
MPID_Isend(416)......:
MPID_isend_unsafe(92):
MPIDI_UCX_send(95)...: returned failed request in UCX netmod(ucx_send.h 95 MPIDI_UCX_send Input/output error)
aborting job:
Fatal error in PMPI_Isend: Other MPI error, error stack:
PMPI_Isend(160)......: MPI_Isend(buf=0xabcf12c0, count=3300, MPI_DOUBLE_PRECISION, dest=1612, tag=4, comm=0x84000004, request=0x7fffb38fa0fc) failed
MPID_Isend(416)......:
MPID_isend_unsafe(92):
MPIDI_UCX_send(95)...: returned failed request in UCX netmod(ucx_send.h 95 MPIDI_UCX_send Input/output error)
[1613401128.457254] [nid001192:11838:0] mm_xpmem.c:82 UCX WARN remote segment id 200002e09 apid 200002e3e is not released, refcount 1
[1613401128.457261] [nid001192:11838:0] mm_xpmem.c:82 UCX WARN remote segment id 200002e08 apid 100002e3e is not released, refcount 1
```

You can add the following line to your job submission script before the `srun` command
to try and workaround this error:

```
export UCX_IB_REG_METHODS=direct
```

!!! note
    Setting this flag may have an impact on code performance.



### AOCC compiler fails to compile with NetCDF (Added: 2021-11-18)

- **Systems affected:** ARCHER2 full system

There is currently a problem with the module file which means cray-netcdf-hdf5parallel will not operate correctly in PrgEnv-aocc. An example of the error seen is:  

```
F90-F-0004-Corrupt or Old Module file /opt/cray/pe/netcdf-hdf5parallel/4.7.4.3/crayclang/9.1/include/netcdf.mod (netcdf.F90: 8)
```

The current workaround for this is to load module epcc-netcdf-hdf5parallel instead if PrgEnv-aocc is required.


## Recently Resolved Issues

### Singularity and CMake 3.x

- **Systems affected:** ARCHER2 4-cabinet system

Certain cmake variables need to be set before a containerised cmake can find
MPI libraries located on the host.

```
MPI_ROOT=/opt/cray/pe/mpich/8.0.16/ofi/gnu/9.1

cmake ...
    -DMPI_C_HEADER_DIR="${MPI_ROOT}/include" -DMPI_CXX_HEADER_DIR="${MPI_ROOT}/include" \
    -DMPI_C_LIB_NAMES=mpi -DMPI_CXX_LIB_NAMES=mpi \
    -DMPI_mpi_LIBRARY="${MPI_ROOT}/lib/libmpi.so" \
    ...
```
