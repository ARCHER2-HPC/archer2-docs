# AMD GPU development system

!!! note
    This page is work in progress. More details on the GPU development system
    and how to use it will be added as they become available.

In early 2024 (January 2024) ARCHER2 users will gain access to a small GPU system 
integrated into ARCHER2 which is designed to allow users to test and develop software 
using AMD GPU.

!!! important
    The GPU component is very small and so is aimed at software development and 
    testing rather than to be used for production research.

## Hardware available

The GPU development system will consist of 4 compute nodes each with:

- 1x AMD EPYC 7534P (Milan) processor, 2.8 GHz
- 4x AMD Instinct MI210 accelerator

!!! note
    Further details on the hardware available will be added as soon as they
    are available.

## Accessing the GPU compute nodes

The GPU nodes will be accessed through the Slurm job submisson system from the
standard ARCHER2 login nodes.

## Compiling software for the GPU compute nodes

!!! note
    Details on how to compile software for use on compute nodes will be added
    as soon as they are available.

You should expect the software development environment to be similar to that
available on the Frontier exascale system:

- [Programming environment](https://docs.olcf.ornl.gov/systems/frontier_user_guide.html#programming-environment)
- [Compiling](https://docs.olcf.ornl.gov/systems/frontier_user_guide.html#compiling) 
