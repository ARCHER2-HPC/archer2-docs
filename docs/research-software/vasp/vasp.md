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

   - [VASP Manual](https://www.vasp.at/wiki/index.php/The_VASP_Manual)
   - [VASP wiki](https://www.vasp.at/wiki/index.php/The_VASP_Manual)
   - [VASP FAQs](https://www.vasp.at/faqs/)

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

#### VASP Transition State Tools (VTST)

As well as the standard VASP 5 modules, we provide versions of VASP 5 with the
[VASP Transition State Tools (VTST)](http://theory.cm.utexas.edu/vtsttools/) from
the University of Texas added. The VTST version adds various functionality to VASP
and provides additional scripts to use with VASP. Additional functionality includes:

- Climbing Image NEB: method for finding reaction pathways between two stable states.
- Dimer: method for finding reaction pathways when only one state is known.
- Lanczos: provides an alternative way to find the lowest mode and find saddle points.
- Optimisers: provides an alternative way to find the lowest mode and find saddle points.
- Dynamical Matrix: uses finite difference to find normal modes and reaction prefactors.

Full details of these methods and the provided scripts can be found on
[the VTST website](http://theory.cm.utexas.edu/vtsttools/).

On ARCHER2, the VTST version of VASP 5 can be accessed by loading the modules with
`VTST` in the module name, for example:

=== "Full system"
    ```
    module load vasp/5/5.4.4.pl2-vtst
    ```

#### Example VASP 5 job submission script

The following script will run a VASP job using 2 nodes (128x2, 256 total
cores).

=== "Full system"
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

    # Load the VASP module
    module load vasp/5

    # Avoid any unintentional OpenMP threading by setting OMP_NUM_THREADS
    export OMP_NUM_THREADS=1

    # Launch the code.
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

=== "Full system"

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

    # Load the VASP module
    module load vasp/6

    # Avoid any unintentional OpenMP threading by setting OMP_NUM_THREADS
    export OMP_NUM_THREADS=1

    # Launch the code.
    srun --distribution=block:block --hint=nomultithread vasp_std
    ```
## Compiling VASP on ARCHER2

If you wish to compile your own version of VASP on ARCHER2 (either VASP
5 or VASP 6) you can find information on how we compiled the central
versions in the build instructions GitHub repository. See:

   - [Build instructions for VASP on GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/VASP)

## Tips for using VASP on ARCHER2

The performance of VASP depends on the version of VASP used, the performance
of MPI collective operations, the choice of VASP parallelisation parameters
(`NCORE`/`NPAR` and `KPAR`) and how many MPI processes per node are used.

**VASP version** For the benchmarks studied, VASP 6.3.0 usually gives
better performance than the older VASP 5.4.4.pl2. The exception is for when
using higher node counts (above 8 nodes) for the CdTe benchmark (which uses
`vasp_ncl`). Users should generally use VASP 6.3.0 but it may be worth evaluating
the performance fo VASP 5.4.4.pl2 for your case if you are running larger
calculations, particularly when using the non-collinear version of VASP.

**MPI collective performance:** To ensure that the MPI collective operations give
the best performance, you should ensure that consecutive MPI rank IDs are pinned to
consecutive cores on a node to maximise shared memory optimisations in NUMA regions.
In practice, the recommended options to the `srun` command: `--hint=nomultithread`
and `--distribution=block:block` should always be specified when running VASP on
ARCHER2. You should also make sure that you use the UCX transport layer for MPI
rather than the default OpenFabrics transport layer. The VASP modules are setup
to enable this but if you are using your own compiled version of VASP you should
add the following lines to your job submission script before you run VASP 
(assuming that you compiled VASP using GCC):

```
module load PrgEnv-gnu
module load craype-network-ucx
module load cray-mpich-ucx
export UCX_IB_REG_METHODS=direct
```

**KPAR:** You should always use the maximum value of `KPAR` that is possible for
your calculation within the memory limits of what is possible.

**NCORE/NPAR:** We have found that the optimal values of `NCORE` (and hence `NPAR`)
depend on both the type of calculation you are performing (e.g. pure DFT, hybrid functional,
&Gamma;-point, non-collinear) and the number of nodes/cores you are using for your 
calculation. In practice, this means that you should experiment with different values
to find the best choice for your calculation. There is information below on the best 
choices for the benchmarks we have run on ARCHER2 that may serve as a useful starting
point. The performance difference from choosing different values can vary by up to
100% so it is worth spending time investigating this.

**MPI processes per node** We found that it is sometimes beneficial to performance to
use less MPI processes per node than the total number of cores per node in some cases
for the benchmarks used. We found that for the large TiO2 &Gamma;-point calculation it
was best to use just 64 MPI processes per node (leaving half of the cores idle). For
the CdTe non-collinear, multiple k-point benchmark, best performance was achieved when
all 128 cores on the node had an MPI process (128 MPI processes per node).

**OpenMP threads** The use of OpenMP threads did not improve performance or scaling
for either of the benchmarks used. This was true even for the TiO2 benchmark case where 
we used only 64 MPI processes per node, the performance was better with 64 idle cores
on a node rather than using the spare core for OpenMP threads. This seems to be because
when OpenMP threading is used, `NCORE` is fixed at a value of 1, which gives poor
performance.

## VASP performance data on ARCHER2

VASP performance data on ARCHER2 is currently available for two
different benchmark systems:

- TiO_2 Supercell, pure DFT functional, &Gamma;-point, 1080 atoms
- CdTe Supercell, hybrid DFT functional. 8 k-points, 65 atoms

### TiO_2 Supercell, pure DFT functional, Gamma-point, 1080 atoms

Basic information:

- Uses `vasp_gam`
- `NELM = 10`
- [Full TiO2 performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_TiO2_perf_analysis.ipynb)

Performance summary for best choices of MPI processes per node and NCORE at
different node counts. Performance reported as timing for `LOOP+` in seconds.

Performance summary:

- Best performance from VASP 6.3.0
- Best performance from 64 MPI processes per node - leaves 64 cores idle on each node
- Best performance with `NCORE = 64`
- Scales well to 16 nodes
- Using OpenMP threads results in worse performance

#### Full system, VASP 6.3.0

 - `vasp/6/6.3.0` module
 - GCC 11.2.0
 - AOCL 3.1 for BLAS/LAPACK/ScaLAPACK and FFTW
 - UCX for MPI transport layer

| Nodes | MPI processes per node | Total MPI processes | NCORE | 6.3.0 (full system) |
|------:|-----------------------:|--------------------:|------:|--------------------:|
|     1 |                     64 |                 128 |    64 |                3295 |
|     2 |                     64 |                 256 |    64 |                1548 |
|     4 |                     64 |                 512 |    64 |                 814 |
|     8 |                     64 |                 512 |    64 |                 416 |
|    16 |                     64 |                1024 |    64 |                 221 |
|    32 |                     64 |                2048 |    64 |                 131 |
|    64 |                     64 |                4096 |    64 |                  82 |

#### Full system, 5.4.4.pl2

 - `vasp/5/5.4.4.pl2` module
 - GCC 11.2.0
 - HPE Cray LibSci 21.09 for BLAS/LAPACK/ScaLAPACK and FFTW 3.3.8.11
 - UCX for MPI transport layer

| Nodes | MPI processes per node | Total MPI processes | NCORE | 5.4.4.pl2 (full system) |
|------:|-----------------------:|--------------------:|------:|------------------------:|
|     1 |                     64 |                  64 |    64 |                    3428 |
|     2 |                     64 |                 128 |    64 |                    1615 |
|     4 |                     64 |                 256 |    64 |                     823 |
|     8 |                     64 |                 512 |    64 |                     429 |
|    16 |                     64 |                1024 |    64 |                     231 |
|    32 |                     64 |                2048 |    64 |                     135 |
|    64 |                     64 |                4096 |    64 |                      79 |


### CdTe Supercell, hybrid DFT functional. 8 k-points, 65 atoms

Basic information:

- Uses `vasp_ncl`
- `NELM = 6`
- [CdTe performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_CdTe_perf_analysis.ipynb)

Performance summary:

- VASP version:
  + Up to 8 nodes: best performance from VASP 6.3.0
  + 16 nodes or more: best performance from VASP 5.4.4.pl2
- Cores per node:
  + Best performance usually from 128 MPI processes per node - all cores occupied
  + At 64 nodes, best performance from 64 MPI processes per node - 64 core idle
- `NCORE`:
  + Up to 8 nodes: best performance with `NCORE = 4` (VASP 6.3.0)
  + 16 nodes or more: best performance with `NCORE = 16` (VASP 5.4.4.pl2)
- `KPAR = 2` is maximum that can be used on standard memory nodes 
- Scales well to 64 nodes
- Using OpenMP threads results in worse performance

#### Full system, VASP 6.3.0

 - `vasp/6/6.3.0` module
 - GCC 11.2.0
 - AOCL 3.1 for BLAS/LAPACK/ScaLAPACK and FFTW
 - UCX for MPI transport layer

| Nodes | MPI processes per node | Total MPI processes | NCORE | KPAR | 5.4.4.pl2 (4-cab system) |
|------:|-----------------------:|--------------------:|------:|-----:|-------------------------:|
|     1 |                    128 |                 128 |     4 |    2 |                    19000 |
|     2 |                    128 |                 256 |     4 |    2 |                    10021 |
|     4 |                    128 |                 512 |     4 |    2 |                     5560 |
|     8 |                    128 |                1024 |     4 |    2 |                     3176 |
|    16 |                    128 |                2048 |     8 |    2 |                     2413 |
|    32 |                     64 |                2048 |    16 |    2 |                     1340 |
|    64 |                     64 |                4096 |    16 |    2 |                      908 |

#### Full system, 5.4.4.pl2

 - `vasp/5/5.4.4.pl2` module
 - GCC 11.2.0
 - HPE Cray LibSci 21.09 for BLAS/LAPACK/ScaLAPACK and FFTW 3.3.8
 - UCX for MPI transport layer

| Nodes | MPI processes per node | Total MPI processes | NCORE | KPAR | 5.4.4.pl2 (4-cab system) |
|------:|-----------------------:|--------------------:|------:|-----:|-------------------------:|
|     1 |                    128 |                 128 |     4 |    2 |                    23417 |
|     2 |                    128 |                 256 |     4 |    2 |                    12338 |
|     4 |                    128 |                 512 |     4 |    2 |                     6751 |
|     8 |                    128 |                1024 |     4 |    2 |                     3676 |
|    16 |                     64 |                1024 |    16 |    2 |                     2136 |
|    32 |                     64 |                2048 |    16 |    2 |                     1266 |
|    64 |                     64 |                4096 |    16 |    2 |                      806 |

