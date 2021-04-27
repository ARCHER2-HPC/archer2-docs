# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

## Open Issues

### HPE Cray `perftools` modules not available by default (Added: 2021-04-27)

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
There are several outstanding issues for the centrally installed Research Software:

- **ChemShell and PyChemShell** are not yet available. We are working with the code developers to address this.
- **Climate Data Operators (CDO) and NCAR Command Language (NCL)** are not yet available, due to issues with dependencies. An investigation is on-going.
- **Paraview** is not yet available. We hope to provide a suitable installation in the near future.
- **VMD** is not yet available. We hope to provide a suitable installation in the near future.
- **PLUMED** is not yet available. Currently, we recommend affected users to install a local version of the software.

Users should also check individual software pages, for known limitations/ caveats, for the use of software on the Cray EX platform and Cray Linux Environment.

### `stat-view` not working
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

Your program compiles and seems to run fine, but after some time (at least 10 
minutes), it crashes with an out-of-memory (OOM) error. The job crashes more 
quickly when run on a smaller number of nodes.

The workaround for this issue is to use the newer HPE Cray Programming Environment
release 21.03. Instructions on using a non-default programming environment release
[are available in the User and Best Practice Guide](../user-guide/dev-environment.md#switching-to-a-different-hpe-cray-programming-environment-release)


## Recently Resolved Issues

### Singularity and CMake 3.x
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
