# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

## Open Issues

### Issue with CASTEP calculations using `num_proc_in_smp` setting

When using the `num_proc_in_smp` input option to CASTEP users will sometimes encounter an 
error such as:

```
    Error comms_setup_smp: Failed to initialise IPC
```

This is beacuse shared memory segments from previous (failed) CASTEP calculations remain on
the node using up resources. We are investigating with HPE Cray how this issue can be 
addressed. The current workaround is to resubmit without the `num_proc_in_smp` option
set though we are aware that this leads to reduced CASTEP performance.

### Job failures with `MPI_Init` errors

If you see failures with an error message similar to:

```
MPICH ERROR [Rank 7] [job id 65233.0] [Mon Jan 11 16:15:34 2021] [unknown] [nid001066] - Abort(1115279) (rank 7 in comm 0): Fatal error in PMPI_Init: Other MPI error, error stack:
MPIR_Init_thread(632)......: 
MPID_Init(516).............: 
MPIDI_NM_mpi_init_hook(575): **ofid_getinfo ofi_init.h 575 MPIDI_NM_mpi_init_hook No data available
...
```

this indicates an problem with the node. Please [contact the ARCHER2 Service Desk](mailto:support@archer2.ac.uk)
with the job ID for your failed job.

### Singularity and CMake
The issue concerns the building of a cmake-compiled code in bind mode in
Singularity containers. This fails because CMake 3.x, running within the
container on a UAN, cannot find the MPI libraries (cray-mpich) on the host
despite the correct paths having been provided.

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

### Issues with RPATH for non-default library versions
When you compile applications against non-default versions of libraries within the HPE
Cray software stack and use the environment variable `CRAY_ADD_RPATH=yes` to try and encode
the paths to these libraries within the binary this will not be respected at runtime and
the binaries will use the default versions instead.


### Memory leak leads to job fail by out of memory (OOM) error
Your program compiles and seems to run fine, but after some time (at least 10 
minutes), it crashes with an out-of-memory (OOM) error. The job crashes more 
quickly when run on a smaller number of nodes.

**Known workaround**
This may be caused by a known problem with the default version of MPICH, 
which is in the process of being resolved. In the meantime, you can get your 
jobs to run by switching to UCF MPICH instead. In your Slurm submission 
script, you will need to remove the following line:

```
module load epcc-job-env
```

Instead, you will need to load the programming environment used to 
compile your program, and switch from the default (OFI) version of MPICH to 
the UCX version of MPICH. So, if your program was compiled using the GNU 
programming environment, you should include the following lines in your Slurm 
submission script before loading any other module:

```
module restore /etc/cray-pe.d/PrgEnv-gnu
module switch cray-mpich/8.0.16 cray-mpich-ucx/8.0.16
module switch craype-network-ofi craype-network-ucx
```

If your program was compiled in a different programming environment, make sure 
to change the first line to load the correct environment.


## Recently Resolved Issues


### Problem accessing Package Accounts from compute nodes

We were aware of problems, since 16th January, for people who have requested access to Package Accounts or who are trying to access sub-project accounts. 

The authorisation changes were not being picked up by the compute nodes. Affected users would see `Permission Denied` or equivalent errors, when attempting to access package accounts on the compute nodes or access files within a sub-project account. 

We were able to resolve the issue with our colleagues in HPE Cray.

(Raised on 20th Jan, closed on 22nd Jan)

