# ARCHER2 Known Issues

This section highlights known issues on ARCHER2, their potential
impacts and any known workarounds. Many of these issues are under
active investigation by HPE Cray and the wider service.

!!! info
    This page was last reviewed on 9 November 2023

## Open Issues

### e-mail alerts from Slurm do not work (Added: 2023-11-09)

Email alerts from Slurm (`--mail-type` and `--mail-user` options) do not produce emails to users. We are investigating
with Universtiy of Edinburgh Information Services to enable this Slurm feature in the future.

### Excessive memory use when using UCX communications protocol (Added: 2023-07-20)

We have seen cases when using the (non-default) UCX communications protocol where the peak in memory use is
much higher than would be expected. This leads to jobs failing unexpectedly with an OOM (Out Of Memory) error.
The workaround is to use Open Fabrics (OFI) communication protocol instead. OFI is the default protocol on 
ARCHER2 and so does not usually need to be explicitly loaded; but if you have UCX loaded, you can switch to
OFI by adding the following lines to your submission script before you run your application:

```
module load craype-network-ofi
module load cray-mpich
```

It can be very useful to track the memory usage of your job as it
runs, for example to see whether there is high usage on all nodes, or a single node, if usage increases gradually or rapidly etc.

Here are [instructions](https://github.com/ARCHER2-HPC/checkmem) on how to do this using a couple of small scripts.

### Slurm `--cpu-freq=X` option is not respected when used with `sbatch` (Added: 2023-01-18)

If you specify the CPU frequency using the `--cpu-freq` option with the `sbatch` command (either using the script `#SBATCH --cpu-freq=X`
method or the `--cpu-freq=X` option directly) then this option will not be respected as the default
setting for ARCHER2 (2.0 GHz) will override the option. You should specify the `--cpu-freq` option to `srun` directly
instead within the job submission script. i.e.:

```
srun --cpu-freq=2250000 ...
```

You can find more information on [setting the CPU frequency in the User Guide](/user-guide/energy/#controlling-cpu-frequency).


### Research Software

There are several outstanding issues for the centrally installed Research Software:

- **PLUMED** is not yet available. Currently, we recommend affected users to install a local version of the software.

Users should also check individual software pages, for known limitations/ caveats, for the use of software on the Cray EX platform and Cray Linux Environment.

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

### AOCC compiler fails to compile with NetCDF (Added: 2021-11-18)

There is currently a problem with the module file which means cray-netcdf-hdf5parallel will not operate correctly in PrgEnv-aocc. An example of the error seen is:  

```
F90-F-0004-Corrupt or Old Module file /opt/cray/pe/netcdf-hdf5parallel/4.7.4.3/crayclang/9.1/include/netcdf.mod (netcdf.F90: 8)
```

The current workaround for this is to load module epcc-netcdf-hdf5parallel instead if PrgEnv-aocc is required.

### Slurm  `--export` option does not work in job submission script

The option `--export=ALL` propagates all the environment variables from the login node to the compute node. If you include the option in the job submission script, it is wrongly ignored by Slurm. The current workaround is to include the option when the job submission script is launched. For instance:

    sbatch --export=ALL myjob.slurm

## Recently Resolved Issues




