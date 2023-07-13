# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

!!! info
    This page was last reviewed on 13 Jul 2023

## Recent issues

### Modules (Added 2023-07-13)

The centrally installed modules `matplotlib/3.4.3`, `scons/4.3.0`, and
`seaborn/0.11.2` are out-of-date and inoperative. They will be removed
in the near future. If you need this functionality, it is recommended
to install the packages locally using `pip` following the process detailed
in the section on [installing your own python packages](/user-guide/python/#installing-your-own-python-packages-with-pip).


## Open Issues

### Slurm `--cpu=freq=X` option is not respected when used with `sbatch` (Added: 2023-01-18)

If you specify the CPU frequency using the `--cpu-freq` option with the `sbatch` command (either using the script `#SBATCH --cpu-freq=X`
method or the `--cpu-freq=X` option directly) then this option will not be respected as the default
setting for ARCHER2 (2.0 GHz) will override the option. You should specify the `--cpu-freq` option to `srun` directly
instead within the job submission script. i.e.:

```
srun --cpu-freq=2250000 ...
```

You can find more information on [setting the CPU frequency in the User Guide](/user-guide/energy/#controlling-cpu-frequency).



### OOM due to memory leak in libfabric (Added: 2022-02-23)

There is an underlying memory leak in the version of libfabric on ARCHER2 (that comes as part of the underlying
SLES operating system) which can cause jobs to fail with an OOM (Out Of Memory) error. This issue will be addressed
in a future upgrade of the ARCHER2 operating system. You can workaround this issue by setting the following
environment variable in your job submission scripts:

```
export FI_MR_CACHE_MAX_COUNT=0
```

This may come with a performance penalty (though in many cases, we have not seen a noticeable performance impact).

If you continue to see OOM errors after setting this environment variable and do not believe that your application
should be requesting too much memory then please
[contact the service desk](https://www.archer2.ac.uk/support-access/servicedesk.html)

### Default FFTW library points to Intel Haswell version rather than AMD Rome at runtime (Added: 2022-02-23)

By default, and at runtime, the standard FFTW library version (from the module `cray-fftw`) will link to a version of the
FFTW library optimised for the Intel Haswell architecture rather than the AMD EPYC architecture. This does not cause
errors as the instruction set is compatible between the two architectures but may not provide optimal performance. The
performance differences observed have been small (&lt; 5%) but if you want to ensure that applications using the `cray-fftw`
module use the correct version of the libraries at runtime, you should add the following lines to your job submission
script:

```
module load cray-fftw
export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
```

The issue arises because the compilers encode a default path to libraries (`/opt/cray/pe/lib64`) into the `RUNPATH` of
the executable so that you do not need to load all the library module dependencies at runtime. The libraries that the
executable finds at `/opt/cray/pe/lib64` are soft links to the default versions of the libraries. There is an error in
the soft link to the FFTW libraries in this directory such they point to the Haswell version of the FFTW libraries rather
than the AMD EPYC (Rome) versions of the libraries.


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

### MPI `UCX ERROR: ivb_reg_mr`

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

### Slurm  `--export` option does not work in job submission script

The option `--export=ALL` propagates all the environment variables from the login node to the compute node. If you include the option in the job submission script, it is wrongly ignored by Slurm. The current workaround is to include the option when the job submission script is launched. For instance:

    sbatch --export=ALL myjob.slurm

## Recently Resolved Issues
