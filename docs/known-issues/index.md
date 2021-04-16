# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

## Open Issues


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

The current workaround is to use `/work/y02/shared/stat-view-workaround/stat-view` instead.

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
jobs to run by switching to UCX MPICH instead. In your Slurm submission 
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

### MPI `UCX ERROR: ivb_reg_mr`

If you are using the UCX layer for MPI communication you may see an error such
as:

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

Setting this flag may have an impact on code performance.

## Recently Resolved Issues

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

