# NWChem

NWChem aims to provide its users with computational chemistry tools that
are scalable both in their ability to treat large scientific
computational chemistry problems efficiently, and in their use of
available parallel computing resources from high-performance parallel
supercomputers to conventional workstation clusters. The NWChem software
can handle: biomolecules, nanostructures, and solid-state system; from
quantum to classical, and all combinations; Gaussian basis functions or
plane-waves; scaling from one to thousands of processors; properties and
relativity.

## Useful Links

  - [NWChem home page](https://nwchemgit.github.io/)
  - [NWChem documentation](https://nwchemgit.github.io/Home.html)
  - [NWChem forum](https://nwchemgit.github.io/Forum.html)

## Using NWChem on ARCHER2

NWChem is released under an Educational Community License (ECL 2.0) and
is freely available to all users on ARCHER2.

### Where can I get help?

If you have problems accessing or running NWChem on ARCHER2, please
contact the Service Desk. General questions on the use of NWChem might
also be directed to the [NWChem forum][1]. More experienced
users with detailed technical issues on NWChem should consider
submitting them to the
[NWChem GitHub issue tracker](https://github.com/nwchemgit/nwchem/issues).

## Running NWChem jobs

The following script will run a NWChem job using 2 nodes (256 cores) in
the standard partition. It assumes that the input file is called
`test\_calc.nw`.

=== "Full system"
    ```
    #!/bin/bash

    # Request 2 nodes with 128 MPI tasks per node for 20 minutes

    #SBATCH --job-name=NWChem_test
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the NWChem module, avoid any unintentional OpenMP threading by
    # setting OMP_NUM_THREADS, and launch the code.
    module load nwchem
    export OMP_NUM_THREADS=1
    srun --distribution=block:block --hint=nomultithread nwchem test_calc
    ```

## Compiling NWChem

The latest instructions for building NWChem on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for NWChem on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/NWChem)

