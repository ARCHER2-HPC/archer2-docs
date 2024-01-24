# AMD GPU development system

!!! note
    This page is work in progress. More details on the GPU development system
    and how to use it will be added as they become available.

In early 2024 ARCHER2 users will gain access to a small GPU system 
integrated into ARCHER2 which is designed to allow users to test and develop software 
using AMD GPU.

!!! important
    The GPU component is very small and so is aimed at software development and 
    testing rather than to be used for production research.

## Hardware available

The GPU development system will consist of 4 compute nodes each with:

- 1x AMD EPYC 7534P (Milan) processor, 32 core, 2.8 GHz
- 4x AMD Instinct MI210 accelerator
- 512 GiB host memory
- 2&times; 100 Gb/s Slingshot interfaces per node

!!! note
    Further details on the hardware available will be added shortly.

## Accessing the GPU compute nodes

The GPU nodes will be accessed through the Slurm job submisson system from the
standard ARCHER2 login nodes. Details of the scheduler limits and configuration
and example job submission scripts are provided below.

## Compiling software for the GPU compute nodes

!!! note
    Details on how to compile software for use on compute nodes will be added
    as soon as they are available.

### Supported programming models

- hip
- openACC
- openMP offload

May work but at user peril (CSE will be looking into additional programming models over time and ill update documentation accordingly): 

- Sycl
- Kokkos
- Raja?


### Programming environments

#### PrgEnv-amd

AMD LLVM Compilers (GPU support)

module load PrgEnv-amd

Real Compilers: amdflang, amdclang, amdclang++

#### PrgEnv-aocc

AMD Optimizing Compilers (AOCC, CPU only support)

module load PrgEnv-aocc

Real Compilers: flang, clang, clang++

#### PrgEnv-cray

Cray Compilation Environment

module load PrgEnv-cray

Real Compilers: crayftn, craycc, crayCC

#### PrgEnv-cray-amd

AMD Clang C/C++ compiler and the Cray Compiling Environment (CCE) Fortran compiler

module load PrgEnv-cray-amd

Real Compilers: crayftn, amdclang, amdclang++

#### PrgEnv-gnu

GNU Compiler Collection

module load PrgEnv-gnu

Real Compilers: gfortran, gcc, g++

#### PrgEnv-gnu-amd

AMD Clang C/C++ compiler and the GNU compiler suite Fortran compiler

module load PrgEnv-gnu-amd

Real Compilers: gfortran, amdclang, amdclang++

### Other modules

#### Hip compilation

Need to load the Rocm module:

module load rocm

This contains the hipcc compiler

#### GPU target

Additionally you need to tell the GPU compiler which GPU hardware your planning to target.
To do this on archer2 you can load the following module:

module load craype-accel-amd-gfx90a

#### CPU target

Additionally unlike the majority of the cPU compute nodes on ARCHER2 the GPU nodes do not use AMD EPYC Rome CPUs and instead use AMD EPYC MILAN's instead.
This means we need to load a different CPU target module:

module load craype-x86-milan

### Compilation 

#### Compilation strategies for GPUs

1) OpenACC: The compiler uses directives in the code target 

    The compiler 

2) OpenMP offload:

    The compiler

3) Device specific code (AMD HiP):

    Architecture specific code kernels are written for sections of you application that have to be compiled by a device specific compiler.

    Separately the CPU sections of the code still need to compiled by a chosen base or host compiler (cray, amd, aocc, gnu,...).

    The compilation pipeline looks something like:

    **DIAGAM**




For each programming environment we can then look at different ways to target GPU hardware.

#### Cray

module load PrgEnv-cray
module load craype-accel-amd-gfx90a
module load rocm

##### HiP

Fortran:

Not supported?

C/C++:

CC -x hip -I. -Ihip -std=c++11 -DHIP main.cpp hip/HIPStream.cpp –o hip.x

##### OpenACC

Fortran:

Enabled by default

ftn -hacc saxpy_acc_mpi.f90 -o saxpy_acc_mpi.x

C/C++:

No openACC support.

##### OpenMP

Fortran:

ftn -fopenmp gemv-omp-target-many-matrices.f90 -o gemv-omp-target-many-matrices.x

C/C++:

CC -fopenmp -DOMP main.cpp omp/OMPStream.cpp -I. -Iomp -DOMP_TARGET_GPU –o omp.x


#### AMD

module load PrgEnv-amd
module load craype-accel-amd-gfx90a
module load rocm

##### HiP

Fortran: ?

C/C++:

CC -x hip -I. -Ihip -std=c++11 -DHIP main.cpp hip/HIPStream.cpp –o hip.x


##### OpenACC

Fortran: ?

C/C++: ?

##### OpenMP

Fortran: 

ftn -fopenmp gemv-omp-target-many-matrices.f90 -o gemv-omp-target-many-matrices.x

C/C++:

CC -fopenmp -DOMP main.cpp omp/OMPStream.cpp -I. -Iomp -DOMP_TARGET_GPU –o omp.x

#### GNU

???


### Advanced Compilation

#### Mixing openMP and HIP C/C++

#### Compilation with hipcc

#### Hpicc and MPI


## GPU-aware MPI

Avalible in three of the programming environments:

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
For example, to request 2 GPU for 20 minutes with an interactive terminal on a GPU compute node you
would use (remember to replace `t01` with your budget code):

```
auser@ln04:/work/t01/t01/auser> srun --gpus=2 --time=00:20:00 --partition=gpu --qos=gpu-shd --account=z19 --pty /bin/bash
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
1    31.0c  43.0W   800Mhz  1600Mhz  0%   auto  300.0W    0%   0%    
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