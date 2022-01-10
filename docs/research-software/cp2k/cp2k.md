# CP2K

[CP2K](https://www.cp2k.org/) is a quantum chemistry and solid state
physics software package that can perform atomistic simulations of solid
state, liquid, molecular, periodic, material, crystal, and biological
systems. CP2K provides a general framework for different modelling
methods such as DFT using the mixed Gaussian and plane waves approaches
GPW and GAPW. Supported theory levels include DFTB, LDA, GGA, MP2, RPA,
semi-empirical methods (AM1, PM3, PM6, RM1, MNDO), and classical force
fields (AMBER, CHARMM). CP2K can do simulations of molecular dynamics,
metadynamics, Monte Carlo, Ehrenfest dynamics, vibrational analysis,
core level spectroscopy, energy minimisation, and transition state
optimisation using NEB or dimer method.

## Useful links

  - [CP2K Reference Manual](https://manual.cp2k.org/#gsc.tab=0)
  - [CP2K HOWTOs](https://www.cp2k.org/howto)
  - [CP2K FAQs](https://www.cp2k.org/faq)

## Using CP2K on ARCHER2

CP2K is available through the `cp2k`
module. MPI only `cp2k.popt` and MPI/OpenMP
Hybrid `cp2k.psmp` binaries are available.

For ARCHER2, CP2K has been compiled with the following optional
features: FFTW for fast Fourier transforms,
`libint` to enable methods including
Hartree-Fock exchange, `libxsmm` for
efficient small matrix multiplications,
`libxc` to provide a wider choice of
exchange-correlation functionals, ELPA for improved performance of
matrix diagonalisation, PLUMED to allow enhanced sampling methods, and
SIRIUS for plane wave computations.

See
[CP2K compile instructions](https://github.com/cp2k/cp2k/blob/master/INSTALL.md)
for a full list of optional features.

If there is an optional feature not available, and which you would like,
please [contact the Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).
Experts may also wish to compile their own versions of the code (see below for instructions).

## Running parallel CP2K jobs

### MPI only jobs

To run CP2K using MPI only, load the `cp2k` module and use the
`cp2k.popt` executable.

For example, the following script will run a CP2K job using 4 nodes
(128x4 cores):

=== "Full system"
    ```
    #!/bin/bash

    # Request 4 nodes using 128 cores per node for 128 MPI tasks per node.

    #SBATCH --job-name=CP2K_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the relevent CP2K module
    module load cp2k

    export OMP_NUM_THREADS=1

    srun --hint=nomultithread --distribution=block:block cp2k.popt -i MYINPUT.inp
    ```


### MPI/OpenMP hybrid jobs

To run CP2K using MPI and OpenMP, load the `cp2k` module and use the
`cp2k.psmp` executable.

=== "Full system"
    ```
    #!/bin/bash

    # Request 4 nodes with 16 MPI tasks per node each using 8 threads;
    # note this means 128 MPI tasks in total.
    # Remember to replace [budget code] below with your account code,
    # e.g. '--account=t01'.

    #SBATCH --job-name=CP2K_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the relevant CP2K module
    module load cp2k

    # Ensure OMP_NUM_THREADS is consistent with cpus-per-task above
    export OMP_NUM_THREADS=8
    export OMP_PLACES=cores

    srun --hint=nomultithread --distribution=block:block cp2k.psmp -i MYINPUT.inp
    ```



## Compiling CP2K

The latest instructions for building CP2K on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for CP2K on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/CP2K/ARCHER2-CP2K-7.1)
