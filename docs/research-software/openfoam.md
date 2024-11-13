# OpenFOAM

OpenFOAM is an open-source toolbox for computational fluid dynamics.
OpenFOAM consists of generic tools to simulate complex physics for a
variety of fields of interest, from fluid flows involving chemical
reactions, turbulence and heat transfer, to solid dynamics,
electromagnetism and the pricing of financial options.

The core technology of OpenFOAM is a flexible set of modules written in
C++. These are used to build solvers and utilities to perform
pre-processing and post-processing tasks ranging from simple data
manipulation to visualisation and mesh processing.

There are a number of different flavours of the OpenFOAM package with
slightly different histories, and slightly different features. The two
most common are distributed by openfoam.org and openfoam.com.

## Useful Links

  - [OpenFOAM website (.org)](https://openfoam.org)
  - [OpenFOAM documentation (.org)](https://cfd.direct/openfoam/user-guide/)
  - [OpenFOAM website (.com)](https://www.openfoam.com)
  - [OpenFOAM documentation (.com)](https://www.openfoam.com/documentation/)

## Using OpenFOAM on ARCHER2

OpenFOAM is released under a GPL v3 license and is freely available to
all users on ARCHER2.

=== "Upgrade 2023"
    ```
    auser@ln01> module avail openfoam
    --------------- /work/y07/shared/archer2-lmod/apps/core -----------------
    openfoam/com/v2106        openfoam/org/v9.20210903
    openfoam/com/v2212 (D)    openfoam/org/v10.20230119 (D)

    ```

    Note: the older versions were recompiled under PE22.12 in April 2023.

=== "Full system"
    ```
    auser@ln01> module avail openfoam
    --------------- /work/y07/shared/archer2-lmod/apps/core -----------------
    openfoam/com/v2106          openfoam/org/v9.20210903 (D)
    openfoam/org/v8.20200901
    ```

Versions from openfoam.org are typically v8.0 etc and there is typically
one release per year (in June; with a patch release in September).
Versions from openfoam.com are e.g., v2106 (to be read as 2021 June) and
there are typically two releases a year (one in June, and one in
December).

To use OpenFOAM on ARCHER2 you should first load an OpenFOAM module,
e.g.

```
user@ln01:> module load PrgEnv-gnu
user@ln01:> module load openfoam/com/v2106
```

(Note that the `openfoam` module will automatically load `PrgEnv-gnu`
if it is not already active.)
The module defines only the base installation directory via the
environment variable `FOAM_INSTALL_DIR`. After loading the module you
need to source the `etc/bashrc` file provided by OpenFOAM, e.g.

```bash
source ${FOAM_INSTALL_DIR}/etc/bashrc
```

You should then be able to use OpenFOAM. The above commands will also
need to be added to any job/batch submission scripts you want to use to
run OpenFOAM. Note that all the centrally installed versions of OpenFOAM
are compiled under `PrgEnv-gnu`.

Note there are no default module versions specified. It is recommended to
use a fully qualified module name (with the exact version, as in the
example above).

### Extensions to OpenFOAM

Many packages extend the central OpenFOAM functionality in some way. However,
there is no completely standardised way in which this works. Some packages
assume they have write access to the main OpenFOAM installation. If this is
the case, you must install your own version before continuing. This
can be done on an individual basis, or a per-project basis using the
[project shared directories](https://docs.archer2.ac.uk/user-guide/data/#sharing-data-with-archer2-users-in-your-project).

Some packages are installed in the OpenFOAM user directory, by default this is
set to `$HOME/OpenFOAM/$USER-[openfoam-version]`. This can be changed (e.g. to
the work filesystem) by adding `WM_PROJECT_USER_DIR=/work/a01/a01/auser/OpenFOAM/auser-[openfoam-version]`
as an argument to `source ${FOAM_INSTALL_DIR}/etc/bashrc`. For example:

```bash
source ${FOAM_INSTALL_DIR}/etc/bashrc WM_PROJECT_USER_DIR=/work/a01/a01/auser/OpenFOAM/auser-v2106
```

### Compiling OpenFOAM

If you want to compile your own version of OpenFOAM, instructions are
available for ARCHER2 at:

 - [Build instructions for OpenFOAM on GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/OpenFOAM)

## Running parallel OpenFOAM jobs

While it is possible to run limited OpenFOAM pre-processing and
post-processing activities on the front end, we request all significant
work is submitted to the queue system. Please remember that the front
end is a shared resource.

A typical SLURM job submission script for OpenFOAM is given here. This
would request 4 nodes to run with 128 MPI tasks per node (a total of 512
MPI tasks). Each MPI task is allocated one core (`--cpus-per-task=1`).

=== "Upgrade 2023"
    ```
    #!/bin/bash

    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --distribution=block:block
    #SBATCH --hint=nomultithread
    #SBATCH --time=00:10:00

    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Ensure the cpus-per-task option is propagated to srun commands
    export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

    # Load the appropriate module and source the OpenFOAM bashrc file

    module load openfoam/org/v10.20230119

    source ${FOAM_INSTALL_DIR}/etc/bashrc

    # Run OpenFOAM work, e.g.,

    srun interFoam -parallel

    ```


=== "Full system"
    ```
    #!/bin/bash

    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --distribution=block:block
    #SBATCH --hint=nomultithread
    #SBATCH --time=00:10:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the appropriate module and source the OpenFOAM bashrc file

    module load openfoam/org/v8.20210901

    source ${FOAM_INSTALL_DIR}/etc/bashrc

    # Run OpenFOAM work, e.g.,

    srun interFoam -parallel

    ```

## Module version history

The following centrally installed versions are available.

### Upgrade 2023

- Module `openfoam/com/v2212` installed as default April 2023 (PE 22.12).
  This is version v2212 (December 2022).
  See the [OpenFOAM.com v2212 release announcement](https://www.openfoam.com/news/main-news/openfoam-v2212)

- Module `openfoam/com/v2106` was recompiled April 2023 (PE 22.12).
  This is version v2106 (June 2021).
  See the [OpenFOAM.com v2106 release announcement](https://www.openfoam.com/news/main-news/openfoam-v2106)

- Module `openfoam/org/v10.20230119` installed as default April 2023 (PE 22.12)
  This is version 10 patch release 19th January 2023.
  See [version 10 patch news](https://openfoam.org/news/v10-patch/)

- Module `openfoam/org/v9.20210903` was recompiled April 2023 (PE 22.12).
  This is version 9 patch release 3rd September 2021.
  See [version 9 patch release news](https://openfoam.org/news/v9-patch/).

### Full system

- Module `openfoam/com/v2106` installed October 2021 (Cray PE 21.04).
  Version v2106 (June 2021).
  See [OpenFOAM.com website](https://www.openfoam.com/news/main-news/openfoam-v2106)

- Module `openfoam/org/v9.20200903` installed October 2021 (Cray PE 21.09).
  Version 9 patch release 3rd September 2021.
  See [OpenFOAM.org website](https://openfoam.org/news/v9-patch/)

- Module `openfoam/org/v8.20200901` installed October 2021 (Cray PE 21.09).
  Version 8 patch release 1st September 2020.
  See [OpenFOAM.org website](https://openfoam.org/news/v8-patch/)
