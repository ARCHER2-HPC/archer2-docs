# MITgcm

The Massachusetts Institute of Technology General Circulation Model
(MITgcm) is a numerical model designed for study of the atmosphere,
ocean, and climate. MITgcm's flexible non-hydrostatic formulation
enables it to simulate fluid phenomena over a wide range of scales; its
adjoint capabilities enable it to be applied to sensitivity questions
and to parameter and state estimation problems. By employing fluid
equation isomorphisms, a single dynamical kernel can be used to simulate
flow of both the atmosphere and ocean.

## Useful Links

  - [MITgcm home page](http://mitgcm.org)
  - [MITgcm documentation](https://mitgcm.readthedocs.io/en/latest/)

## Building MITgcm on ARCHER2

MITgcm is not available via a module on ARCHER2 as users will build
their own executables specific to the problem they are working on.
However, we do provide an optfile which will allow `genmake2` to create
Makefiles which will work on ARCHER2.

!!! note
    The processes to build MITgcm on the ARCHER2 4-cabinet system and full
    system are slightly different. Please make sure you use the commands
    for the correct system below.

You can obtain the MITgcm source code from the developers by cloning
from the GitHub repository with the command

    git clone https://github.com/MITgcm/MITgcm.git

You should then copy the ARCHER2 optfile into the MITgcm directories. You may use the files at the locations below for the 4-cabinet and full systems.

=== "Full system"
    ```bash
    cp /work/y07/shared/apps/core/mitgcm/optfiles/linux_amd64_gnu_archer2 MITgcm/tools/build_options/
    ```

=== "4-cabinet system"
    ```bash
    cp /work/n02/shared/MITgcm/optfiles/dev_linux_amd64_gnu_archer2 MITgcm/tools/build_options/
    ```

Note that this build options file is still being tested for optimisation purposes. For working with large executables (e.g. adjoint configurations), edit the build options file to include the lines:

    FFLAGS="$FFLAGS -mcmodel=large"
    CFLAGS="$CFLAGS -mcmodel=large"

You can also use `-mcmodel=medium` for a lower-memory options. When you are building your code with this optfile, use the GNU environment with

=== "Full system"
    ```
    module load PrgEnv-gnu
    ```

=== "4-cabinet system"
    ```
    module restore PrgEnv-gnu
    ```

You should also set the following environment variables.
`MITGCM_ROOTDIR` is used to locate the source code and should point to
the top MITgcm directory. Optionally, adding the MITgcm tools directory
to your `PATH` environment variable makes it easier to use tools such as
`genmake2`, and the `MITGCM_OPT` environment variable makes it easier to
refer to pass the optfile to `genmake2`.

=== "Full system"
    ```
    export MITGCM_ROOTDIR=/path/to/MITgcm
    export PATH=$MITGCM_ROOTDIR/tools:$PATH
    export MITGCM_OPT=$MITGCM_ROOTDIR/tools/build_options/linux_amd64_gnu_archer2
    ```

=== "4-cabinet system"
    ```
    export MITGCM_ROOTDIR=/path/to/MITgcm
    export PATH=$MITGCM_ROOTDIR/tools:$PATH
    export MITGCM_OPT=$MITGCM_ROOTDIR/tools/build_options/dev_linux_amd64_gnu_archer2
    ```

When using `genmake2` to create the Makefile, you will need to specify the
optfile to use. Other commonly used options might be to use extra source
code with the `-mods` option, and to enable MPI with `-mpi`. You might
then run a command that resembles the following:

    genmake2 -mods /path/to/additional/source -mpi -optfile $MITGCM_OPT

You can read about the full set of options available to `genmake2` by
running

    genmake2 -help

Finally, you may then build your executable with the `make depend`,
`make` commands.

## Running MITgcm on ARCHER2

Once you have built your executable you can write a script like the
following which will allow it to run on the ARCHER2 compute nodes. This
example would run a pure MPI MITgcm simulation over 2 nodes of 128 cores
each for up to one hour.

=== "Full system"
    ```
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=MITgcm-simulation
    #SBATCH --time=1:0:0
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 256 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options
    srun --distribution=block:block --hint=nomultithread ./mitgcmuv
    ```

=== "4-cabinet system"
    ```
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=MITgcm-simulation
    #SBATCH --time=1:0:0
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the job environment (this module needs to be loaded before any other modules)
    module load epcc-job-env

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 256 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options
    srun --distribution=block:block --hint=nomultithread ./mitgcmuv
    ```
    
## Reproducing the ECCO version 4 (release 4) state estimate on ARCHER2

!!! note
    These instructions apply only to the 4-cabinet and have not yet
    been tested on the full ARCHER2 system.

The ECCO version 4 state estimate (ECCOv4-r4) is an observationally-constrained numerical solution produced by the ECCO group at JPL. If you would like to reproduce the state estimate on ARCHER2 in order to create customised runs and experiments, follow the instructions below. They have been slightly modified from the JPL instructions for ARCHER2. 

For more information, see the ECCOv4-r4 website <https://ecco-group.org/products-ECCO-V4r4.htm>

### Get the ECCOv4-r4 source code

First, navigate to your directory on the ``/work`` filesystem in order to get access to the compute nodes. Next, create a working directory, perhaps MYECCO, and navigate into this working directory:

    mkdir MYECCO
    cd MYECCO
    
In order to reproduce ECCOv4-r4, we need a specific checkpoint of the MITgcm source code. 

    git clone https://github.com/MITgcm/MITgcm.git -b checkpoint66g
    
Next, get the ECCOv4-r4 specific code from GitHub:

    cd MITgcm
    mkdir -p ECCOV4/release4
    cd ECCOV4/release4
    git clone https://github.com/ECCO-GROUP/ECCO-v4-Configurations.git
    mv ECCO-v4-Configurations/ECCOv4\ Release\ 4/code .
    rm -rf ECCO-v4-Configurations
    
### Get the ECCOv4-r4 forcing files

The surface forcing and other input files that are too large to be stored on GitHub are available via NASA data servers. In total, these files are about 200 GB in size. You must register for an Earthdata account and connect to a WebDAV server in order to access these files. For more detailed instructions, read the help page <https://ecco.jpl.nasa.gov/drive/help>.

First, apply for an Earthdata account: <https://urs.earthdata.nasa.gov/users/new>

Next, acquire your WebDAV credentials: <https://ecco.jpl.nasa.gov/drive> (second box from the top)

Now, you can use wget to download the required forcing and input files:

    wget -r --no-parent --user YOURUSERNAME --ask-password https://ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_forcing
    wget -r --no-parent --user YOURUSERNAME --ask-password https://ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_init 
    wget -r --no-parent --user YOURUSERNAME --ask-password https://ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_ecco
      
After using `wget`, you will notice that the `input*` directories are, by default, several levels deep in the directory structure. Use the `mv` command to move the `input*` directories to the directory where you executed the `wget` command. Specifically,

```
mv ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_forcing/ .
mv ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_init/ .
mv ecco.jpl.nasa.gov/drive/files/Version4/Release4/input_ecco/ .
rm -rf ecco.jpl.nasa.gov
```

### Compiling and running ECCOv4-r4

The steps for building the ECCOv4-r4 instance of MITgcm are very similar to those for other build cases. First, wou will need to create a build directory:

    cd MITgcm/ECCOV4/release4
    mkdir build
    cd build

If you haven't already, copy the ARCHER2 optfile into the MITgcm directories:

    cp /work/n02/shared/MITgcm/optfiles/dev_linux_amd64_gnu_archer2 MITgcm/tools/build_options/

For working with large executables like ECCOv4-r4, edit the build options file to include the lines:

    FFLAGS="$FFLAGS -mcmodel=large"
    CFLAGS="$CFLAGS -mcmodel=large"

You can also use `-mcmodel=medium` for a lower-memory options. When you are building your code with this optfile, use the GNU
environment with

    module restore PrgEnv-gnu

If you haven't already, set your environment variables:

    export MITGCM_ROOTDIR=/path/to/MITgcm
    export PATH=$MITGCM_ROOTDIR/tools:$PATH
    export MITGCM_OPT=$MITGCM_ROOTDIR/tools/build_options/linux_amd64_gfortran_archer2
    
Next, compile the executable:

    genmake2 -mods /path/to/additional/source -mpi -optfile $MITGCM_OPT
    make depend
    make
    
Once you have compiled the model, you will have the mitgcmuv executable for ECCOv4-r4. 

#### Issue with DOS formatting and end-of-namelist characters

As of 16 December 2020, some of the ECCOv4-r4 files downloaded from the ECCO GitHub repository appear to be DOS formatted and/or have inconsistent end-of-namelist characters. This causes end-of-file runtime errors. You may need to manually replace the end-of-namelist "&" characters in some of the namelist files with "/". The namelists are found here:

    cd MITgcm/ECCOV4/release4/input_init/NAMELIST/
  
The files with "&" end-of-namelist characters are:

    data.autodiff
    data.salt_plume
    data.layers
    data.optim
    data.ptracers
    data.grdchk
    
So far, this has only been tested with the gnu compiler (gcc 10.1.0). 

#### Create run directory and link files

In order to run the model, you need to create a run directory and link/copy the appropriate files. First, navigate to your directory on the ``work`` filesystem. From the ``MITgcm/ECCOV4/release4`` directory:

    mkdir run
    cd run
    
    # link the data files
    ln -s ../input_init/NAMELIST/* .
    ln -s ../input_init/error_weight/ctrl_weight/* .
    ln -s ../input_init/error_weight/data_error/* .
    ln -s ../input_init/* .
    ln -s ../input_init/tools/* .
    ln -s ../input_ecco/*/* .
    ln -s ../input_forcing/eccov4r4* .

    python mkdir_subdir_diags.py
    
    # manually copy the mitgcmuv executable
    cp -p ../build/mitgcmuv .

For a short test run, edit the ``nTimeSteps`` variable in the file ``data``. Comment out the default value and uncomment the line reading ``nTimeSteps=8``. This is a useful test to make sure that the model can at least start up. 

To run on ARCHER2, submit a batch script to the Slurm scheduler. Here is an example submission script:

    #!/bin/bash
    
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=ECCOv4r4-test
    #SBATCH --time=1:0:0
    #SBATCH --nodes=8
    #SBATCH --tasks-per-node=12
    #SBATCH --cpus-per-task=1
    
    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically
    #   using threading.
    export OMP_NUM_THREADS=1
    
    # Launch the parallel job
    #   Using 256 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options
    srun --distribution=block:block --hint=nomultithread ./mitgcmuv

This configuration uses 96 MPI processes at 12 MPI processes per node. Once the run has finished, in order to check that the run has successfully completed, check the end of one of the standard output files. 

    tail STDOUT.0000
    
It should read 

    PROGRAM MAIN: Execution ended Normally
    
The files named `STDOUT.*` contain diagnostic information that you can use to check your results. As a first pass, check the printed statistics for any clear signs of trouble (e.g. NaN values, extremely large values). 

#### ECCOv4-r4 in adjoint mode

If you have access to the commercial TAF software produced by <http://FastOpt.de>, then you can compile and run the ECCOv4-r4 instance of MITgcm in adjoint mode. This mode is useful for comprehensive sensitivity studies and for constructing state estimates. From the ``MITgcm/ECCOV4/release4`` directory, create a new code directory and a new build directory:

    mkdir code_ad
    cd code_ad
    ln -s ../code/* .
    cd ..
    mkdir build_ad
    cd build_ad
    
In this instance, the ``code_ad`` and ``code`` directories are identical, although this does not have to be the case. Make sure that you have the ``staf`` script in your path or in the ``build_ad`` directory itself. To make sure that you have the most up-to-date script, run:

    ./staf -get staf
    
To test your connection to the FastOpt servers, try:

    ./staf -test
    
You should receive the following message:

    Your access to the TAF server is enabled.
    
The compilation commands are similar to those used to build the forward case.

    # load relevant modules
    module load cray-netcdf-hdf5parallel
    module load cray-hdf5-parallel

    # compile adjoint model
    ../../../MITgcm/tools/genmake2 -ieee -mpi -mods=../code_ad -of=(PATH_TO_OPTFILE)
    make depend
    make adtaf
    make adall
    
The source code will be packaged and forwarded to the FastOpt servers, where it will undergo source-to-source translation via the TAF algorithmic differentiation software. If the compilation is successful, you will have an executable named ``mitgcmuv_ad``. This will run the ECCOv4-r4 configuration of MITgcm in adjoint mode. As before, create a run directory and copy in the relevant files. The procedure is the same as for the forward model, with the following modifications:

    cd ..
    mkdir run_ad
    cd run_ad
    # manually copy the mitgcmuv executable
    cp -p ../build_ad/mitgcmuv_ad .
    
To run the model, change the name of the executable in the Slurm submission script; everything else should be the same as in the forward case. As above, at the end of the run you should have a set of `STDOUT.*` files that you can examine for any obvious problems. 
