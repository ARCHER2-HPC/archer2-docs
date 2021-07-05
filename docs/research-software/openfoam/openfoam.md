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

  - OpenFOAM website (.org) <https://openfoam.org>
  - OpenFOAM documentation <https://cfd.direct/openfoam/user-guide/>
  - OpenFOAM website (.com) <https://www.openfoam.com>
  - OpenFOAM documentation <https://www.openfoam.com/documentation/>

## Using OpenFOAM on ARCHER2

OpenFOAM is released under a GPL v3 license and is freely available to
all users on ARCHER2.

``` 
auser@uan01:> module avail openfoam
--------------- /work/y07/shared/archer2-modules/modulefiles-cse ----------
openfoam/com/v2006  openfoam/com/v2106  openfoam/org/v8.20200901
```

Versions from openfoam.org are typically v8.0 etc and there is typically
one release per year (in June; with a patch release in September).
Versions from openfoam.com are e.g., v2006 (to be read as 2020 June) and
there are typically two releases a year (one in June, and one in
December).

To use OpenFOAM on ARCHER2 you should first load the OpenFOAM module,
e.g.,:

    user@uan01:> module -s restore PrgEnv-gnu
    user@uan01:> module load openfoam/com/v2006

The module defines only the base installation directory via the
environment variable `FOAM_INSTALL_DIR`. After loading the module you
need to source the `etc/bashrc` file provided by OpenFOAM, e.g.,:

    user@uan01:> source ${FOAM_INSTALL_DIR}/etc/bashrc

You should then be able to use OpenFOAM. The above commands will also
need to be added to any job/batch submission scripts you want to use to
run OpenFOAM. Note that all the centrally installed versions of OpenFOAM
are compiled under `PrgEnv-gnu`.

Note there are no default module versions specificied. It is recommended to
use a fully qualified module name (with the exact version, as in the
example above).

## Running parallel OpenFOAM jobs

While it is possible to run limited OpenFOAM pre-processing and
post-processing activities on the front end, we request all significant
work is submitted to the queue system. Please remember that the front
end is a shared resource.

A typical SLURM job submission script for OpenFOAM is given here. This
would request 4 nodes to run with 128 MPI tasks per node (a total of 512
MPI tasks). Each MPI task is allocated one core (`--cpus-per-task=1`).

```
#!/bin/bash

#SBATCH --nodes=4
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:10:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

# Load the appropriate modules and source the OpenFOAM bashrc file
# The first line makes PrgEnv-gnu available on the back end nodes.

module -s restore /etc/cray-pe.d/PrgEnv-gnu
module load openfoam/org/v8.20200901

source ${FOAM_INSTALL_DIR}/etc/bashrc

# Run OpenFOAM work

srun --distribution=block:block --hint=nomultithread interFoam -parallel
```

## Compiling OpenFOAM

If you want to compile your own version of OpenFOAM, instructions are
available for ARCHER2 at
<https://github.com/hpc-uk/build-instructions/tree/main/apps/OpenFOAM>

## Module version history

The following centrally installed versions are available.

* Module `openfoam/com/v2106` installed July 2021 (Cray PE 20.10)

    Version v2106 (June 2021).
    See [OpenFOAM.com website](https://www.openfoam.com/news/main-news/openfoam-v2106)

* Module `openfoam/com/v2006` installed October 2020 (Cray PE 20.10)

    Version v2006 (June 2020).
    See [OpenFOAM.com website](https://www.openfoam.com/releases/openfoam-v2006/)

* Module `openfoam/org/v8.20200901` installed October 2020 (Cray PE 20.10)

    Version 8 patch release 1st September 2020.
    See [OpenFOAM.org website](https://openfoam.org/news/v8-patch/)

