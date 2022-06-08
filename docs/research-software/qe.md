# Quantum Espresso

Quantum Espresso (QE) is an integrated suite of open-source computer
codes for electronic-structure calculations and materials modeling at
the nanoscale. It is based on density-functional theory, plane waves,
and pseudopotentials.

## Useful Links

  - [Quantum Espresso home page](http://www.quantum-espresso.org/)
  - [Quantum Espresso User Guides](http://www.quantum-espresso.org/resources/users-manual)
  - [Quantum Espresso Tutorials](http://www.quantum-espresso.org/resources/tutorials)

## Using QE on ARCHER2

QE is released under a GPL v2 license and is freely available to all
ARCHER2 users.

## Running parallel QE jobs

For example, the following script will run a QE `pw.x` job using 4 nodes
(128x4 cores).

=== "Full system"
    ```
    #!/bin/bash

    # Request 4 nodes to run a 512 MPI task job with 128 MPI tasks per node.
    # The maximum walltime limit is set to be 20 minutes.

    #SBATCH --job-name=qe_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the relevant Quantum Espresso module
    module load quantum_espresso

    srun pw.x < test_calc.in
    ```

## Hints and tips

The QE module is set to load up the default QE-provided
pseudo-potentials. If you wish to use non-default pseudo-potentials, you
will need to change the `ESPRESSO_PSEUDO` variable to point to the
directory you wish. This can be done by adding the following line
**after** the module is loaded

    export ESPRESSO_PSEUDO /path/to/pseudo_potentials

## Compiling QE

The latest instructions for building QE on ARCHER2 can be found in the
GitHub repository of build instructions:

   - [Build instructions for Quantum Espresso](https://github.com/hpc-uk/build-instructions/tree/main/apps/QuantumEspresso)
