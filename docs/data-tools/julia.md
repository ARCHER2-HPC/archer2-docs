# Julia

[Julia](https://julialang.org) is a general purpose software used widely
in datascience and for data visualisation.

!!! important
    This documentation is provided by an external party (i.e. not by the
    ARCHER2 service itself). Julia is not part of the officially supported
    software on ARCHER2. While the ARCHER2 service desk is able to provide
    support for basic use of this software (e.g. access to software, writing
    job submission scripts) it does not generally provide detailed technical
    support for the software and you may be directed to seek support from
    other places if the service desk cannot answer the questions.

## First time installation

!!! note
    There is no centrally installed version of Julia, so you will have to
    manually install it and any packages you may need. The following
    guide was tested on julia-1.6.6.

You will first need to download Julia into your work directory and untar the
folder. You should then add the folder to your system path so you can use the
`julia` executable. Finally, you need to tell Julia to install any packages in
the work directory as opposed to the default home directory,  which can only be
accessed from the login nodes. This can be done with the following code

```bash
export WORK=/work/t01/t01/auser
cd $WORK

wget https://julialang-s3.julialang.org/bin/linux/x64/1.6/julia-1.6.6-linux-x86_64.tar.gz
tar zxvf julia-1.6.6-linux-x86_64.tar.gz
rm ./julia-1.6.6-linux-x86_64.tar.gz

export PATH="$PATH:$WORK/julia-1.6.6/bin"

mkdir ./.julia
export JULIA_DEPOT_PATH="$WORK/.julia"
export PATH="$PATH:$WORK/$JULIA_DEPOT_PATH/bin"
```

At this point you should have a working installation of Julia! The environment
variables will however be cleared when you log out of the terminal. You can
set them in the `.bashrc` file so that they're automatically defined every time
you log in by adding the following lines to the end of the file `~/.bashrc`

```bash
export WORK="/work/t01/t01/auser"
export JULIA_DEPOT_PATH="$WORK/.julia"
export PATH="$PATH:$WORK/julia-1.6.6/bin"
export PATH="$PATH:$JULIA_DEPOT_PATH/bin"
```

## Installing packages and using environments
Julia has a built in package manager which can be used to install registered
packages quickly and easily. Like with many other high level programming
languages we can make use of environments to control dependencies etc.

To make an environment, first navigate to where you want your environment to be
(ideally a subfolder of your `/work/` directory) and create an empty folder to
store the environment in. Then launch Julia with the --project flag.

```bash
cd $WORK
mkdir ./MyTestEnv
julia --project=$WORK/MyTestEnv
```

This launches Julia in the `MyTestEnv` environment. You can then install
packages as usual using the normal commands in the Julia terminal. E.g.

```julia
using Pkg
Pkg.add("Oceananigans")
```

## Configuring MPI.jl
The `MPI.jl` package doesn't use the system MPICH implementation by default.
You can set it up to do this by following the steps below. First you will need
to load the `cray-mpich` module and define some environment variables (see [here](https://juliaparallel.org/MPI.jl/stable/configuration/)
for further details). Then you can launch Julia in an environment of your
choice, ready to build.

```bash
module load cray-mpich/8.1.4
export JULIA_MPI_BINARY="system"
export JULIA_MPI_PATH=""
export JULIA_MPI_LIBRARY="/opt/cray/pe/mpich/8.1.4/ofi/cray/9.1/lib/libmpi.so"
export JULIA_MPIEXEC="srun"

julia --project=<<path to environment>>
```

Once in the Julia terminal you can build the `MPI.jl` package using the
following code. The final line installs the `mpiexecjl` command which should
be used instead of `srun` to launch mpi processes.

```julia
using Pkg
Pkg.build("MPI"; verbose=true)
MPI.install_mpiexecjl(command = "mpiexecjl", force = false, verbose = true)
```
The `mpiexecjl` command will be installed in the directory that `JULIA_DEPOT_PATH`
points too.

!!! note
    You only need to do this once per environment.


## Running Julia on the compute nodes
Below is an example script for running Julia with mpi on the compute nodes

```slurm
#!/bin/bash
# Slurm job options (job-name, compute nodes, job time)
#SBATCH --job-name=<<job-name>>
#SBATCH --time=00:19:00

#SBATCH --nodes=1
#SBATCH --ntasks-per-node=24
#SBATCH --cpus-per-task=1

#SBATCH --qos=short
#SBATCH --reservation=shortqos

#SBATCH --account=<<your account>>
#SBATCH --partition=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load PrgEnv-cray
module load cray-mpich/8.1.4

# Set the number of threads to 1
#   This prevents any threaded system libraries from automatically
#   using threading.
export OMP_NUM_THREADS=1
export JULIA_NUM_THREADS=1

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# Define some paths
export WORK=/work/t01/t01/auser

export JULIA="$WORK/julia-1.6.6/bin/julia"  # The julia executable
export PATH="$PATH:$WORK/julia-1.6.6/bin"  # The folder of the julia executable
export JULIA_DEPOT_PATH="$WORK/.julia"
export MPIEXECJL="$JULIA_DEPOT_PATH/bin/mpiexecjl"  # The path to the mpiexexjl executable

$MPIEXECJL --project=$WORK/MyTestEnv -n 24 $JULIA ./MyMpiJuliaScript.jl
```

The above script uses MPI but you can also use multithreading instead by setitng the `JULIA_NUM_THREADS`
environment variable.
