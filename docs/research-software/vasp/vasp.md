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

The performance of VASP depends on the performance of MPI collective operations 
and the choice of VASP parallelisation parameters (`NCORE`/`NPAR` and `KPAR`).

**MPI collective performance:** To ensure that the MPI collective operations give the best performance, you should
ensure that consecutive MPI rank IDs are pinned to consecutive cores on a node
to maximise shared memory optimisations in NUMA regions. In practice, the recommended
options to the `srun` command: `--hint=nomultithread` and `--distribution=block:block`
should always be specified when running VASP on ARCHER2.

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

## VASP performance data on ARCHER2

VASP performance data on ARCHER2 is currently available for two
different benchmark systems:

- TiO_2 Supercell, pure DFT functional, &Gamma;-point, 1080 atoms
- CdTe Supercell, hybrid DFT functional. 8 k-points, 65 atoms

### TiO_2 Supercell, pure DFT functional, Gamma-point, 1080 atoms

Basic information:
- Uses `vasp_gam`
- [Full TiO2 performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_TiO2_perf_analysis.ipynb)

Performance summary (best choices of NCORE at different node counts). All tests with
`vasp/5/5.4.4.pl2-gcc10-cpe2103` module on the ARCHER2 4-cabinet system.

| Nodes | MPI processes per node | Total MPI processes | NCORE | Maximum LOOP time (s) |
|------:|-----------------------:|--------------------:|------:|----------------------:|
|     1 |                    128 |                 128 |    32 |                   387 |
|     2 |                    128 |                 256 |   128 |                   170 |
|     4 |                    128 |                 512 |   128 |                    88 |
|     8 |                    128 |                1024 |   128 |                    47 |
|    16 |                    128 |                2046 |   128 |                    28 |
|    32 |                    128 |                4096 |   128 |                    16 |

### CdTe Supercell, hybrid DFT functional. 8 k-points, 65 atoms

Basic information:
  - Uses `vasp_ncl`
  - [CdTe performance data](https://github.com/hpc-uk/archer-benchmarks/blob/main/others/VASP/analysis/VASP_CdTe_perf_analysis.ipynb)

  Performance summary (best choices of NCORE and KPAR at different node counts). All tests with
`vasp/5/5.4.4.pl2-gcc10-cpe2103` module on ARCHER2 4-cabinet system.

| Nodes | MPI processes per node | Total MPI processes | NCORE | KPAR | Maximum LOOP time (s) |
|------:|-----------------------:|--------------------:|------:|-----:|----------------------:|
|     1 |                    128 |                 128 |     2 |    2 |                 13956 |
|     2 |                    128 |                 256 |     4 |    2 |                  6494 |
|     4 |                    128 |                 512 |     4 |    2 |                  3061 |
|     8 |                    128 |                1024 |     4 |    2 |                  1695 |
|    16 |                    128 |                2046 |    16 |    2 |                  1132 |
|    32 |                    128 |                4096 |    16 |    2 |                   593 |
|    64 |                    128 |                8192 |    16 |    2 |                   459 |
