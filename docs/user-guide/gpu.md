# AMD GPU Development Platform

!!! note
    This page is work in progress. More details on the GPU Development Platform
    and how to use it will be added as they become available.

In early 2024 ARCHER2 users will gain access to a small GPU system 
integrated into ARCHER2 which is designed to allow users to test and develop software 
using AMD GPUs.

!!! important
    The GPU component is very small and so is aimed at software development and 
    testing rather than to be used for production research.

## Hardware available

The GPU Development Platform consists of 4 compute nodes each with:

- 1x AMD EPYC 7534P (Milan) processor, 32 core, 2.8 GHz
- 4x AMD Instinct MI210 accelerator
- 512 GiB host memory
- 2&times; 100 Gb/s Slingshot interfaces per node

!!! note
    Further details on the hardware available will be added shortly.

## Accessing the GPU compute nodes

The GPU nodes can be accessed through the Slurm job submission system from the
standard ARCHER2 login nodes. Details of the scheduler limits and configuration
and example job submission scripts are provided below. 

## Compiling software for the GPU compute nodes

### Overview

As a quick summary, the recommended procedure for compiling code that
offloads to the AMD GPUs is as follows:

- Load the desired Programming Environment module: `module load PrgEnv-xxx`
- Load the ROCm module: `module load rocm`
- Load the appropriate GPU target module `module load craype-accel-amd-gfx90a`
- Load the appropriate CPU target module: `module load craype-x86-milan`
- Load any other modules (e.g. libraries)
- Use the usual compiler wrappers `ftn`, `cc`, or `CC`

For details and alternative approaches, see below. 

### Programming Environments

The following programming environments and compilers are available to
compile code for the AMD GPUs on ARCHER2 using the usual compiler
wrappers (`ftn`, `cc`, `CC`), which is the recommended approach:


| Programming Environment   | Description        | Actual compilers called by `ftn`, `cc`, `CC` |
| ------------------------- | ------------------ | ------------------------------------ |
| `PrgEnv-amd`              | AMD LLVM compilers | `amdflang`, `amdclang`, `amdclang++` |
| `PrgEnv-cray`             | Cray compilers     | `crayftn`, `craycc`, `crayCC`        |
| `PrgEnv-gnu`              | GNU compilers      | `gfortran`, `gcc`, `g++`             |
| `PrgEnv-gnu-amd`          | hybrid             | `gfortran`, `amdclang`, `amdclang++` |
| `PrgEnv-cray-amd`         | hybrid             | `crayftn`, `amdclang`, `amdclang++`  |


To decide which compiler(s) to use to compile offload code for the AMD
GPUs, you may find it useful to consult the [Compilation
Strategies](#compilation-strategies) section below.

The hybrid environments `PrgEnv-gnu-amd` and `PrgEnv-cray-amd` are
provided as a convenient way to mitigate less mature OpenMP offload
support in the AMD LLVM Fortran compiler. In these hybrid environments
`ftn` therefore calls `gfortran` or `crayftn` instead of `amdflang`.

Details about the underlying compiler being called by a compiler
wrapper can be checked using the `--version` flag, for example:

```
> module load PrgEnv-amd
> cc --version
AMD clang version 14.0.0 (https://github.com/RadeonOpenCompute/llvm-project roc-5.2.3 22324 d6c88e5a78066d5d7a1e8db6c5e3e9884c6ad10e)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /opt/rocm-5.2.3/llvm/bin

```


### ROCm 

Access to AMD's ROCm software stack is provided through the `rocm`
module:

```
module load rocm
```

With the `rocm` module loaded the AMD LLVM compilers `amdflang`,
`amdclang`, and `amdclang++` become available to use directly or
through AMD's compiler driver utility `hipcc`. Neither approach is
recommended as a first choice for most users, as considerable care
needs to be taken to pass suitable flags to the compiler or to
`hipcc`. With `PrgEnv-amd` loaded the compiler wrappers `ftn`, `cc`,
`CC`, which bypass `hipcc` and call `amdflang`, `amdclang`, or
`amdclang++` directly, take care of passing suitable compilation
flags, which is why using these wrappers is the recommended approach
for most users, at least initially.

**Note**: the `rocm` module should be loaded whenever you are compiling
for the AMD GPUs, even if you are not using the AMD LLVM compilers
(`amdflang`, `amdclang`, `amdclang++`).  

The `rocm` module also provides access to other AMD tools, such as
HIPIFY (`hipify-clang` or `hipify-perl` command), which enables
translation of CUDA to HIP code. See also the [section below on
HIPIFY](#hipify).


### GPU target

Regardless of what approach you use, you will need to tell the
underlying GPU compiler which GPU hardware to target. When using the
compiler wrappers `ftn`, `cc`, or `CC`, as recommended, this can be
done by ensuring the appropriate GPU target module is loaded:

```
module load craype-accel-amd-gfx90a
```

### CPU target

The AMD GPU nodes are equipped with AMD EPYC Milan CPUs instead of the
AMD EPYC Rome CPUs present on the regular CPU-only ARCHER2 compute
nodes. Though the difference between these processors is small, when
using the compiler wrappers `ftn`, `cc`, or `CC`, as recommended, we
should load the appropriate CPU target module:

```
module load craype-x86-milan
```



### Compilation Strategies for GPU Offloading

Compiler support on ARCHER2 for various programming models that enable
offloading to AMD GPUs can be summarised at a glance in the following
table:

| PrgEnv        | Actual compiler | OpenMP Offload | HIP | OpenACC |
| ------------- | --------------- | :------------: | :-: | :-----: |
| `PrgEnv-amd`  | `amdflang`      |     ✅	   | ❌  |   ❌    |
| `PrgEnv-amd`  | `amdclang`      |     ✅	   | ❌  |   ❌    |
| `PrgEnv-amd`  | `amdclang++`    |     ✅	   | ✅  |   ❌    |
| `PrgEnv-cray` | `crayftn`       |     ✅	   | ❌  |   ✅    |
| `PrgEnv-cray` | `craycc`        |     ✅	   | ❌  |   ❌    |  
| `PrgEnv-cray` | `crayCC`        |     ✅	   | ✅  |   ❌    |
| `PrgEnv-gnu`  | `gfortran`      |     ✅	   | ❌  |   ❌    |
| `PrgEnv-gnu`  | `gcc`           |     ✅	   | ❌  |   ❌    |
| `PrgEnv-gnu`  | `g++`           |     ✅         | ❌  |   ❌    |


It is generally recommended to do the following:

```
module load PrgEnv-xxx
module load rocm
module load craype-accel-amd-gfx90a
module load craype-x86-milan
```

And then to use the `ftn`, `cc` and/or `CC` wrapper to compile as
appropriate for the programming model in question. Specific guidance
on how to do this for different programming models is provided in the
subsections below. 

When deviating from this procedure and using underlying compilers
directly, or when debugging a problematic build using the wrappers, it
may be useful to check what flags the compiler wrappers are passing to
the underlying compiler. This can be done by using the
`-craype-verbose` option with a wrapper when compiling a
file. Optionally piping the resulting output to the command `tr " "
"\n"` so that flags are split over lines may be convenient for visual
parsing. For example:

```
> CC -craype-verbose source.cpp | tr " " "\n"
```



#### OpenMP Offload

To use the compiler wrappers to compile code that offloads to GPU with
OpenMP directives, first load the desired PrgEnv module and other
necessary modules:

```
module load PrgEnv-xxx
module load rocm
module load craype-accel-amd-gfx90a
module load craype-x86-milan
```

Then use the appropriate compiler wrapper and pass the `-fopenmp`
option to the wrapper when compiling. For example:

```
ftn -fopenmp source.f90
```

This should work under `PrgEnv-amd`, `PrgEnv-cray`, and
`PrgEnv-gnu`. You may find that offload directives introduced in more
recent versions of the OpenMP standard, e.g. versions later than
OpenMP 4.5, fail to compile with some compilers. Under `PrgEnv-cray`
an explicit description of supported OpenMP features can be viewed
using the command `man intro_openmp`.

    
#### HIP

To compile C or C++ code that uses HIP written specifically to offload
to AMD GPUs, first load the desired PrgEnv module (either `PrgEnv-amd`
or `PrgEnv-cray`) and other necessary modules:

```
module load PrgEnv-xxx
module load rocm
module load craype-accel-amd-gfx90a
module load craype-x86-milan
```

Then compile using the `CC` compiler wrapper as follows:

```
CC -x hip -std=c++11 -D__HIP_ROCclr__ --rocm-path=${ROCM_PATH} source.cpp
```

Alternatively, you may use `hipcc` to drive the AMD LLVM compiler
`amdclang(++)` to compile HIP code. In that case you will need to take
care to explicitly pass all required offload flags to `hipcc`, such
as:

```
-D__HIP_PLATFORM_AMD__ --offload-arch=gfx90a
```

To see what `hipcc` passes to the compiler, you can pass the
`--verbose` option. If you are compiling MPI-parallel HIP code with
`hipcc`, please see additional guidance under [HIPCC and
MPI](#hipcc-and-mpi).

`hipcc` can compile both HIP code for device (GPU) execution and
non-HIP code for host (CPU) execution and will default to using the
AMD LLVM compiler `amdclang(++)` to do so. If your software consists
of separate compilation units - typically separate files - containing
HIP code non-HIP code, it is possible to use a different compiler than
`hipcc` to compile the non-HIP code. To do this:

- Compile the HIP code as above using `hipcc`
- Compile the non-HIP code using the compiler wrapper `CC` and a *different* PrgEnv than `PrgEnv-amd` loaded
- Link the resulting code objects (`.o` files) together using the compiler wrapper 


#### OpenACC

Offloading using OpenACC directives on ARCHER2 is only supported by
the Cray Fortran compiler. You should therefore load the following:

```
module load PrgEnv-cray
module load rocm
module load craype-accel-amd-gfx90a
module load craype-x86-milan
```

OpenACC Fortran code can then be compiled using the `-hacc` flag, as follows:

```
ftn -hacc source.f90 
```

Details on what OpenACC standard and features are supported under
`PrgEnv-cray` can be viewed using the command `man intro_openacc`.


### Advanced Compilation

#### OpenMP Offload + OpenMP CPU threading

Code may use OpenMP for multithreaded execution on the host CPU in
combination with target directives to offload work to GPU. Both uses
of OpenMP can coexist in a single compilation unit, which should be
compiled using the relevant compiler wrapper and the `-fopenmp` flag.


#### HIP + OpenMP Offload

Using both OpenMP and HIP to offload to GPU is possible, but only if
the two programming models are not mixed in the same compilation unit.
Two or more separate compilation units - typically separate source
files - should be compiled as recommended individually for HIP and
OpenMP offload code in the respective sections above. The resulting
code objects (`.o` files) should then be linked together using a
compiler wrapper *with* the `-fopenmp` flag, but *without* the `-x
hip` flag.

#### HIP + OpenMP CPU threading

Code in a single compilation unit, such as a single source file, can
use HIP to offload to GPU as well as OpenMP for multithreaded
execution on the host CPU. Compilation should be done using the
relevant compiler wrapper and the flags `-fopenmp` and `–x hip` - in
that order - as well as the flags for HIP compilation specified above:

```
CC -fopenmp -x hip -std=c++11 -D__HIP_ROCclr__ --rocm-path=${ROCM_PATH} source.cpp
```


#### HIPCC and MPI

When compiling an MPI-parallel code with `hipcc` instead of a compiler
wrapper, the path to the Cray MPI library include directory should be
passed explicitly, or set as part of the `CXXFLAGS` environment
variable, as:

```
-I${CRAY_MPICH_DIR}/include
```

MPI library directories should also be passed to `hipcc`, or set as
part of the `CXXFLAGS` environment variable prior to compiling, as:

```
-L${CRAY_MPICH_DIR}/lib ${PE_MPICH_GTL_DIR_amd_gfx90a}
```

Finally the MPI library should be linked explicitly, or set as part of
the `LIBS` environment variable prior to linking, as:

```
-lmpi ${PE_MPICH_GTL_LIBS_amd_gfx90a}
```



## GPU-aware MPI

Available in three of the programming environments:

- PrgEnv-amd
- PrgEnv-cray
- PrgEnv-gnu

Need to set environment variable to enable GPU support in cray-mpich:

`MPICH_GPU_SUPPORT_ENABLED=1`

No additional MPI models to load use the standard CRAY-MPICH module.

This supports GPU-GPU transfers:

- Inter-node via GPU-NIC RDMA
- Intra-node via GPU Peer2Peer IPC

Be aware that on these nodes there are only two PICe network cards in each node and they may not be in the same memory region to a given GPU.
Therefore NUMA effects are to be expected in multi-node communication.

### Libraries

cray-libsci_acc


### Environment variables 

https://rocm.docs.amd.com/projects/HIP/en/latest/how_to_guides/debugging.html#summary-of-environment-variables-in-hip

#### AMD_LOG_LEVEL

0: Disable log.
1: Enable log on error level.
2: Enable log on warning and below levels.
0x3: Enable log on information and below levels.
0x4: Decode and display AQL packets.

#### AMD_LOG_MASK

Enable HIP log on different Level.

Default: 0x7FFFFFFF

0x1: Log API calls.
0x02: Kernel and Copy Commands and Barriers.
0x4: Synchronization and waiting for commands to finish.
0x8: Enable log on information and below levels.
0x20: Queue commands and queue contents.
0x40:Signal creation, allocation, pool.
0x80: Locks and thread-safety code.
0x100: Copy debug.
0x200: Detailed copy debug.
0x400: Resource allocation, performance-impacting events.
0x800: Initialization and shutdown.
0x1000: Misc debug, not yet classified.
0x2000: Show raw bytes of AQL packet.
0x4000: Show code creation debug.
0x8000: More detailed command info, including barrier commands.
0x10000: Log message location.
0xFFFFFFFF: Log always even mask flag is zero.

#### HIP_VISIBLE_DEVICES:

For system with multiple devices, it’s possible to make only certain device(s) visible to HIP via setting environment variable, HIP_VISIBLE_DEVICES(or CUDA_VISIBLE_DEVICES on Nvidia platform), only devices whose index is present in the sequence are visible to HIP.

Runtime : HIP Runtime. Applies only to applications using HIP on the AMD platform.

`export HIP_VISIBLE_DEVICES=0,1`

 
#### ROCR_VISIBLE_DEVICES

A list of device indices or UUIDs that will be exposed to applications

Runtime : ROCm Platform Runtime. Applies to all applications using the user mode ROCm software stack.

`export ROCR_VISIBLE_DEVICES="0,GPU-DEADBEEFDEADBEEF"`

#### OMP_DEFAULT_DEVICE

Default device used for OpenMP target offloading.

Runtime : OpenMP Runtime. Applies only to applications using OpenMP offloading.

`export OMP_DEFAULT_DEVICE="2"`

sets the default device to the 3rd device on the node.

#### HSA_ENABLE_SDMA

`export HSA_ENABLE_SDMA=0`

Forces host-to-device and device-to-host copies to use compute shader blit kernels rather than the dedicated DMA copy engines.

Impact will be reduced bandwidth but useful when isolating issues with hardware copy engines.


#### MPICH_OFI_NIC_VERBOSE

#### MPICH_OFI_NIC_POLICY

#### MPICH_GPU_SUPPORT_ENABLED

Activates GPU aware MPI in Cray MPICH:

`export MPICH_GPU_SUPPORT_ENABLED=1`

If not set then if MPI calls try to send messages with pointers to GPU memory an error will occur.


## Python Environment




## Supported software

The ARCHER2 GPU service is intended for code development, testing and experimentation and will not have supported centrally installed versions of codes as is supported for the ARCHER2 compute nodes. However some builds are being made available to users by members of CSE to under a best effort approach to support the community.

Codes that have modules targetting GPUs are:

- 

## Running jobs on the GPU nodes

To run a GPU job, you must specify a GPU partition and a
quality of service (QoS) as well as the number of GPUs required. You
specify the number of GPU cards you want per node using the `--gpus=N`
option, where `N` is typically 1, 2 or 4.

!!! Note
	As there are 4 GPUs per node, each GPU is associated with 1/4 of the
	resources of the node, i.e., 8 of 32 physical cores and roughly 128 GiB
	of the total 512 GiB host memory.


Allocations of host resources are made pro-rata. For example, if 2 GPUs
are requested, `sbatch` will allocate 16 cores and around 256 GiB of host
memory (in addition to 2 GPUs). Any attempt to use more than the
allocated resources will result in an error.

This automatic allocation by Slurm for GPU jobs means that the
submission script should not specify options such as `--ntasks` and
`--cpus-per-task`. Such a job submission will be rejected. See below for
some examples of how to use host resources and how to launch MPI
applications.

!!! Warning
	In order to run jobs on the GPU nodes your ARCHHER2 budget must have positive CU
	hours associated with it. However, your budget will not be charged for any GPU
    jobs you run.

### Slurm Partitions

Your job script must specify a partition. The following table has a list
of relevant GPU partition(s) on ARCHER2.

| Partition | Description                                                 | Max nodes available |
| --------- | ----------------------------------------------------------- | ------------------- |
| gpu  | GPU nodes with AMD EPYC 32-core processor, 512 GB memory, 4&times;AMD Instinct MI210 GPU | 4    |

### Slurm Quality of Service (QoS)

Your job script must specify a QoS relevant for the GPU nodes. Available
QoS specifications are as follows.

| QoS        | Max Nodes Per Job | Max Walltime | Jobs Queued | Jobs Running | Partition(s) | Notes |
| ---------- | ----------------- | ------------ | ----------- | ------------ | ------------ | ------|
| gpu-shd    | 1               | 1 hr       | 2          | 1           | gpu    | Nodes potentially shared with other users |
| gpu-exc    | 2               | 1 hr       | 2          | 1           | gpu    | Exclusive node access |

### Example job submission scripts

Here are a series of example jobs for various patterns of running on the ARCHER2 GPU nodes
They cover the following scenarios:

- Single GPU
- Multiple GPU on a single node - shared node access (max. 2 GPU)
- Multiple GPU on a single node - exclusive node access (max. 4 GPU)
- Multiple GPU on multiple nodes

### Single GPU

This example requests a single GPU on a potentially shared node and launch using a single CPU
process with offload to a single GPU.

```
#!/bin/bash

#SBATCH --job-name=single-GPU
#SBATCH --gpus=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-shd

srun --ntasks=1 --cpus-per-task=1 ./my_gpu_program.x
```

### Multiple GPU on a single node - shared node access (max. 2 GPU)

This example requests two GPUs on a potentially shared node and launch using two
MPI processes (one per GPU) with one MPI process per CPU NUMA region.

We use the `--cpus-per-task=8` option to `srun` to set the stride between the two
MPI processes to 8 physical cores. This places the MPI processes on separate NUMA
regions to ensure they are associated with the correct GPU that is closest to them
on the compute node architecture.

```
#!/bin/bash

#SBATCH --job-name=multi-GPU
#SBATCH --gpus=2
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-shd

srun --ntasks=2 --cpus-per-task=8 ./my_gpu_program.x
```

### Multiple GPU on a single node - exclusive node access (max. 4 GPU)

This example requests four GPUs on a single node and launches the program using four
MPI processes (one per GPU) with one MPI process per CPU NUMA region.

We use the `--cpus-per-task=8` option to `srun` to set the stride between the
MPI processes to 8 physical cores. This places the MPI processes on separate NUMA
regions to ensure they are associated with the correct GPU that is closest to them
on the compute node architecture.

```
#!/bin/bash

#SBATCH --job-name=multi-GPU
#SBATCH --gpus=4
#SBATCH --nodes=1
#SBATCH --exclusive
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-exc

srun --ntasks=4 --cpus-per-task=8 ./my_gpu_program.x
```

!!! note
    When you use the `--qos=gpu-exc` QoS you must also add the `--exclusive` flag
    and the specify the number of nodes you want with `--nodes=1`.  

### Multiple GPU on multiple nodes - exclusive node access (max. 8 GPU)

This example requests eight GPUs across two nodes and launches the program using eight
MPI processes (one per GPU) with one MPI process per CPU NUMA region.

We use the `--cpus-per-task=8` option to `srun` to set the stride between the
MPI processes to 8 physical cores. This places the MPI processes on separate NUMA
regions to ensure they are associated with the correct GPU that is closest to them
on the compute node architecture.

```
#!/bin/bash

#SBATCH --job-name=multi-GPU
#SBATCH --gpus=4
#SBATCH --nodes=2
#SBATCH --exclusive
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-exc

srun --ntasks=8 --cpus-per-task=8 ./my_gpu_program.x
```

!!! note
    When you use the `--qos=gpu-exc` QoS you must also add the `--exclusive` flag
    and the specify the number of nodes you want with `--nodes=1`.  

### Interactive jobs

#### Using `salloc`

!!! tip
    This method does not give you an interactive shell on a GPU compute node. If you
    want an interactive shell on the GPU compute nodes, see the `srun` method described
    below.

If you wish to have a terminal to perform interactive testing, you can 
use the `salloc` command to reserve the resources so you can use `srun` commands interactively. 
For example, to request 2 GPU for 20 minutes you would use (remember to replace `t01` with your
budget code):

```
auser@ln04:/work/t01/t01/auser> salloc --gpus=2 --time=00:20:00 --partition=gpu --qos=gpu-shd --account=t01
salloc: Pending job allocation 5335731
salloc: job 5335731 queued and waiting for resources
salloc: job 5335731 has been allocated resources
salloc: Granted job allocation 5335731
salloc: Waiting for resource configuration
salloc: Nodes nid200001 are ready for job

auser@ln04:/work/t01/t01/auser> export OMP_NUM_THREADS=1
auser@ln04:/work/t01/t01/auser> srun rocm-smi


======================= ROCm System Management Interface =======================
================================= Concise Info =================================
GPU  Temp   AvgPwr  SCLK    MCLK     Fan  Perf  PwrCap  VRAM%  GPU%  
0    31.0c  43.0W   800Mhz  1600Mhz  0%   auto  300.0W    0%   0%    
1    34.0c  43.0W   800Mhz  1600Mhz  0%   auto  300.0W    0%   0%    
================================================================================
============================= End of ROCm SMI Log ==============================


srun: error: nid200001: tasks 0: Exited with exit code 2
srun: launch/slurm: _step_signal: Terminating StepId=5335731.0

auser@ln04:/work/t01/t01/auser> module load xthi
auser@ln04:/work/t01/t01/auser> srun --ntasks=2 --cpus-per-task=8 --hint=nomultithread xthi
Node summary for    1 nodes:
Node    0, hostname nid200001, mpi   2, omp   1, executable xthi
MPI summary: 2 ranks 
Node    0, rank    0, thread   0, (affinity =  0-7) 
Node    0, rank    1, thread   0, (affinity = 8-15) 
```

#### Using `srun`

If you want an interactive terminal on a GPU node then you can use the `srun` command to achieve this.
For example, to request 1 GPU for 20 minutes with an interactive terminal on a GPU compute node you
would use (remember to replace `t01` with your budget code):

```
auser@ln04:/work/t01/t01/auser> srun --gpus=1 --time=00:20:00 --partition=gpu --qos=gpu-shd --account=z19 --pty /bin/bash
srun: job 5335771 queued and waiting for resources
srun: job 5335771 has been allocated resources
auser@nid200001:/work/t01/t01/auser> 
```

Note that the command prompt has changed to indicate we are now on a GPU compute node. You can now directly run commands
that interact with the GPU devices, e.g.:

```
auser@nid200001:/work/t01/t01/auser> rocm-smi

======================= ROCm System Management Interface =======================
================================= Concise Info =================================
GPU  Temp   AvgPwr  SCLK    MCLK     Fan  Perf  PwrCap  VRAM%  GPU%  
0    29.0c  43.0W   800Mhz  1600Mhz  0%   auto  300.0W    0%   0%     
================================================================================
============================= End of ROCm SMI Log ==============================
```

!!! warning
    Launching parallel jobs on GPU nodes from an interactive shell on a GPU node is not straightforward so you should either
    use job submission scripts or the `salloc` method of interactive use described above.

## Debugging

rocgdb?

## Profiling

rocprof?

## Performance tuning

### Job placement

One important feature of GPU nodes is the placement of processes to GPU's and the associated host memory region.

#### Node Topology

======================= ROCm System Management Interface =======================
=========================== Weight between two GPUs ============================
       GPU0         GPU1         GPU2         GPU3
GPU0   0            15           15           15
GPU1   15           0            15           15
GPU2   15           15           0            15
GPU3   15           15           15           0

============================ Hops between two GPUs =============================
       GPU0         GPU1         GPU2         GPU3
GPU0   0            1            1            1
GPU1   1            0            1            1
GPU2   1            1            0            1
GPU3   1            1            1            0

========================== Link Type between two GPUs ==========================
       GPU0         GPU1         GPU2         GPU3
GPU0   0            XGMI         XGMI         XGMI
GPU1   XGMI         0            XGMI         XGMI
GPU2   XGMI         XGMI         0            XGMI
GPU3   XGMI         XGMI         XGMI         0

================================== Numa Nodes ==================================
GPU 0          : (Topology) Numa Node: 0
GPU 0          : (Topology) Numa Affinity: 0
GPU 1          : (Topology) Numa Node: 1
GPU 1          : (Topology) Numa Affinity: 1
GPU 2          : (Topology) Numa Node: 2
GPU 2          : (Topology) Numa Affinity: 2
GPU 3          : (Topology) Numa Node: 3
GPU 3          : (Topology) Numa Affinity: 3
============================= End of ROCm SMI Log ==============================


### Performance tuning

AMD provide documentation:

https://rocm.docs.amd.com/en/docs-5.5.1/how_to/tuning_guides/mi200.html



## Tools

### rocm-smi

If you load the rocm module on the system you will have access to the rocm-smi utility. This utility allows users to report information about the GPUs on node and can be very useful in better understanding the set up of the hardware you are working with and monitoring GPU metrics during job execution.

Here are some useful commands to get you started:



More detail can be found at 


### Hipify




## Notes

You should expect the software development environment to be similar to that
available on the Frontier exascale system:

- [Programming environment](https://docs.olcf.ornl.gov/systems/frontier_user_guide.html#programming-environment)
- [Compiling](https://docs.olcf.ornl.gov/systems/frontier_user_guide.html#compiling) 


Examples need:

Fortran:

    OpenACC

    OpenMP

    HIP: N/A

C/C++:

    OpenACC

    OpenMP

    HiP

- GPU isolation: https://rocm.docs.amd.com/en/docs-5.7.1/understand/gpu_isolation.html