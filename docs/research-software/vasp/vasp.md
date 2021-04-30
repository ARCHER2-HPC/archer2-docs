# VASP

The [Vienna Ab initio Simulation Package (VASP)](http://www.vasp.at) is
a computer program for atomic scale materials modelling, e.g. electronic
structure calculations and quantum-mechanical molecular dynamics, from
first principles.

VASP computes an approximate solution to the many-body Schrödinger
equation, either within density functional theory (DFT), solving the
Kohn-Sham equations, or within the Hartree-Fock (HF) approximation,
solving the Roothaan equations. Hybrid functionals that mix the
Hartree-Fock approach with density functional theory are implemented as
well. Furthermore, Green's functions methods (GW quasiparticles, and
ACFDT-RPA) and many-body perturbation theory (2nd-order Møller-Plesset)
are available in VASP.

In VASP, central quantities, like the one-electron orbitals, the
electronic charge density, and the local potential are expressed in
plane wave basis sets. The interactions between the electrons and ions
are described using norm-conserving or ultrasoft pseudopotentials, or
the projector-augmented-wave method.

To determine the electronic ground state, VASP makes use of efficient
iterative matrix diagonalisation techniques, like the residual
minimisation method with direct inversion of the iterative subspace
(RMM-DIIS) or blocked Davidson algorithms. These are coupled to highly
efficient Broyden and Pulay density mixing schemes to speed up the
self-consistency cycle.

## Useful Links

   - [VASP Manual](http://cms.mpi.univie.ac.at/vasp/vasp/vasp.html)
   - [VASP wiki](https://www.vasp.at/wiki/index.php/The_VASP_Manual)
   - [VASP Licensing](http://www.vasp.at/index.php/faqs/71-how-can-i-purchase-a-vasp-license)

## Using VASP on ARCHER2

**VASP is only available to users who have a valid VASP licence.**

If you have a VASP 5 or 6 licence and wish to have access to VASP on
ARCHER2, please make a request via the SAFE, see:

   - [How to request access to package groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

!!! note
    Both VASP 5 and VASP 6 are available on ARCHER2. You generally need a
    different licence for each of these versions.

## Running parallel VASP jobs

To access VASP you should load the appropriate `vasp` module in your job
submission scripts.

### VASP 5

To load the default version of VASP 5, you would use:

    module load vasp/5

Once loaded, the executables are called:

  - `vasp_std` - Multiple k-point version
  - `vasp_gam` - GAMMA-point only version
  - `vasp_ncl` - Non-collinear version

Once the module has been loaded, you can access the LDA and PBE
pseudopotentials for VASP on ARCHER2 at:

    $VASP_PSPOT_DIR

The following script will run a VASP job using 2 nodes (128x2, 256 total
cores).

```
#!/bin/bash

# Request 16 nodes (2048 MPI tasks at 128 tasks per node) for 20 minutes.   

#SBATCH --job-name=VASP_test
#SBATCH --nodes=16
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

# Load the VASP module, avoid any unintentional OpenMP threading by
# setting OMP_NUM_THREADS, and launch the code.
export OMP_NUM_THREADS=1
module load vasp/5
srun --distribution=block:block --hint=nomultithread vasp_std
```

### VASP 6

To load the default version of VASP 6, you would use:

    module load vasp/6

Once loaded, the executables are called:

  - `vasp_std` - Multiple k-point version
  - `vasp_gam` - GAMMA-point only version
  - `vasp_ncl` - Non-collinear version

Once the module has been loaded, you can access the LDA and PBE
pseudopotentials for VASP on ARCHER2 at:

    $VASP_PSPOT_DIR

The following script will run a VASP job using 2 nodes (128x2, 256 total
cores) using only MPI ranks and no OpenMP threading.

!!! tip
    VASP 6 can make use of OpenMP threads in addition to running with pure
    MPI. We will add notes on performance and use of threading in VASP as
    information becomes available.

```
#!/bin/bash

# Request 16 nodes (2048 MPI tasks at 128 tasks per node) for 20 minutes.   

#SBATCH --job-name=VASP_test
#SBATCH --nodes=16
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

# Load the VASP module, avoid any unintentional OpenMP threading by
# setting OMP_NUM_THREADS, and launch the code.
export OMP_NUM_THREADS=1
module load vasp/6
srun --distribution=block:block --hint=nomultithread vasp_std
```

## Compiling VASP on ARCHER2

If you wish to compile your own version of VASP on ARCHER2 (either VASP
5 or VASP 6) you can find information on how we compiled the central
versions in the build instructions GitHub repository. See:

   - [Build instructions for VASP on GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/VASP)

## Tips for using VASP on ARCHER2

### Hybrid functional calculations: underpopulate MPI ranks per node at higher core counts 

When running hybrid functional calculations the VASP software makes
extensive use of MPI collective functions. At higher node counts, the
fact that ARCHER2 compute nodes have a large number of cores per node
means that collective operations can become extremely expensive and 
cause calculations to stop scaling well. This effect can be mitigated 
by running VASP with less than 128 MPI ranks per node (e.g. using
64 MPI ranks per node instead). 

For example, a 65 atom system with 8 k-points requires a reduction
of MPI ranks per node from 128 to 64 once you get to 16 nodes.

An example job submission script to run a VASP 5 calculation on
16 nodes with 64 MPI ranks per node (1024 MPI ranks in total) would
be:

```
#!/bin/bash

# Request 16 nodes (1024 MPI tasks at 64 tasks per node) for 3 hours
# Note setting --cpus-per-task=2 to distribute the MPI tasks evenly
# across the NUMA regions on the node   

#SBATCH --job-name=VASP_test
#SBATCH --nodes=16
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=2
#SBATCH --time=3:0:0

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

# Load the VASP module, avoid any unintentional OpenMP threading by
# setting OMP_NUM_THREADS, and launch the code.
export OMP_NUM_THREADS=1
module load vasp/5
srun --distribution=block:block --hint=nomultithread vasp_std
```
## VASP performance data on ARCHER2

VASP performance data on ARCHER2 is currently available for two
different benchmark systems:

  - TiO_2 Supercell, pure DFT functional, Gamma-point, 1080 atoms
    - Uses `vasp_gam`
    - [TiO2 performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_TiO2_perf_analysis.ipynb)
  - CdTe Supercell, hybrid DFT functional. 8 k-points, 65 atoms
    - Uses `vasp_ncl`
    - [CdTe performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_CdTe_perf_analysis.ipynb)

