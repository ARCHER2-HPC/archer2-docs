# Containers

This page was originally based on the documentation at the
[University of Sheffield HPC service](http://docs.hpc.shef.ac.uk/en/latest/sharc/software/apps/singularity.html)

Designed around the notion of mobility of compute and reproducible
science, Singularity enables users to have full control of their
operating system environment. This means that a non-privileged user can
"swap out" the Linux operating system and environment on the host for a
Linux OS and environment that they control. So if the host system is
running CentOS Linux but your application runs in Ubuntu Linux with a
particular software stack, you can create an Ubuntu image, install your
software into that image, copy the image to another host (e.g. ARCHER2),
and run your application on that host in its native Ubuntu environment.

Singularity also allows you to leverage the resources of whatever host
you are on. This includes high-speed interconnects (e.g. Slingshot on
ARCHER2), file systems (e.g. /home and /work on ARCHER2) and potentially other
resources.

!!! note
    Singularity only supports Linux containers. You cannot create
    images that use Windows or macOS (this is a restriction of the
    containerisation model rather than Singularity).

## Useful Links

  - [Singularity website](https://www.sylabs.io/)
  - [Singularity documentation](https://www.sylabs.io/docs/)

## About Singularity Containers (Images)

Similar to Docker, a Singularity container (or, more commonly, *image*)
is a self-contained software stack. As Singularity does not require a
root-level daemon to run its images (as is required by Docker) it is
suitable for use on multi-user HPC systems such as ARCHER2. Within the
container/image, you have exactly the same permissions as you do in a
standard login session on the system.

In practice, this means that an image created on your local machine with
all your research software installed for local development will also run
on ARCHER2.

Pre-built images (such as those on [DockerHub](http://hub.docker.com) or
[SingularityHub archive](https://datasets.datalad.org/?dir=/shub/) can simply be downloaded
and used on ARCHER2 (or anywhere else Singularity is installed).

Creating and modifying images requires root permission and so must be
done on a system where you have such access (in practice, this is
usually within a virtual machine on your laptop/workstation).

!!! Note SingularityHub now read-only
    SingularityHub was a publicly available cloud service for Singularity Containers active from 2016 to 2021. It built container recipes from Github repositories on Google Cloud, and containers were available via the command line Singularity or sregistry software. These containers are still available now in the [SingularityHub Archive](https://datasets.datalad.org/?dir=/shub/)


## Using Singularity Images on ARCHER2

Singularity images can be used on ARCHER2 in a number of ways,
including:

  - Interactively on the login nodes
  - Interactively on compute nodes
  - As serial processes within a non-interactive batch script
  - As parallel processes within a non-interactive batch script

We provide information on each of these scenarios below. First,
we describe briefly how to get existing images onto ARCHER2 so
that you can use them.

### Getting existing images onto ARCHER2

Singularity images are files, so, if you already have an image,
you can use `scp` to copy the file to ARCHER2 as you would with
any other file.

If you wish to get a file from one of the container image repositories
then Singularity allows you to do this from ARCHER2 itself.

For example, to retrieve an image from SingularityHub on ARCHER2 we can
simply issue a Singularity command to pull the image.

    auser@ln03:~> singularity pull hello-world.sif shub://vsoch/hello-world

The image located at the `shub` URI is written to a Singularity Image
File (SIF) called `hello-world.sif`.

### Interactive use on the login nodes

Once you have an image file, using it on the login nodes in an
interactive way is extremely simple: you use the `singularity shell`
command. Using the image we built in the example above:

    auser@ln03:~> singularity shell hello-world.sif
    Singularity> 

Within a Singularity image your home directory will be available.

Once you have finished using your image, you can return to the ARCHER2
login node prompt with the `exit` command:

    Singularity> exit
    exit
    auser@ln03:~>

### Interactive use on the compute nodes

The process for using an image interactively on the compute nodes is
very similar to that for the login nodes. The only difference is that
you first have to submit an interactive serial job (from a location on
`/work`) in order to get interactive access to the compute node.

For example, to reserve a full node for you to work on interactively you
would use:

    auser@ln03:/work/t01/t01/auser> srun --nodes=1 --exclusive --time=00:20:00 \
                                          --account=[budget code] \
                                          --partition=standard --qos=standard \
                                          --pty /bin/bash
    
    ...wait until job starts...
    
    auser@nid00001:/work/t01/t01/auser>

Note that the prompt has changed to show you are on a compute node. Now
you can use the image in the same way as on the login node.

    auser@nid00001:/work/t01/t01/auser> singularity shell hello-world.sif
    Singularity> exit
    exit
    auser@nid00001:/work/t01/t01/auser> exit
    auser@ln03:/work/t01/t01/auser>

!!! note
    We used `exit` to leave the interactive image shell and then
    `exit` again to leave the interactive job on the compute node.

### Serial processes within a non-interactive batch script

You can also use Singularity images within a non-interactive batch
script as you would any other command. If your image contains a
*runscript* then you can use `singularity run` to execute the runscript
in the job. You can also use `singularity exec` to execute arbitrary
commands (or scripts) within the image.

An example job submission script to run a serial job that executes the
runscript within the `hello-world.sif` image that we downloaded previously to an
ARCHER2 login node would be as follows.

=== "Full system"
    ```bash
    #!/bin/bash --login
    
    # Slurm job options (name, compute nodes, job time)
    
    #SBATCH --job-name=helloworld
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:10:00
    
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Run the serial executable
    singularity run $SLURM_SUBMIT_DIR/hello-world.sif
    ```

You submit this in the usual way and the standard output and error
should be written to `slurm-...`, where the output filename ends
with the job number.

### Parallel processes within a non-interactive batch script

Running a Singularity container in parallel across a number of compute nodes requires some
preparation. In general though, Singularity can be run within the parallel job launcher (`srun`).

    srun <options> \
        singularity <options> /path/to/image/file \
            app <options>

The code snippet above shows the launch command as having three nested parts, `srun`, the singularity environment
and the containerized application.

The Singularity image must be compatible with the MPI environment on the host; either, the containerized
app has been built against the appropriate MPI libraries or the container itself contains an MPI library
that is compatible with the host MPI. The latter situation is known as the [hybrid model](https://sylabs.io/guides/3.7/user-guide/mpi.html#hybrid-model);
this is the approach taken in the sections that follow.

## Creating Your Own Singularity Images

!!! note
    This information is based on that in the [Introduction to Singularity](https://carpentries-incubator.github.io/singularity-introduction/)
    lesson from the [Carpentries Incubator](https://carpentries.org/community-lessons/#the-carpentries-incubator). Citation
    J. Cohen and A. Turner. "Reproducible computational environments using containers: Introduction to Singularity". Version 2020.08a, 
    August 2020. Carpentries Incubator. https://github.com/carpentries-incubator/singularity-introduction

As we saw above, you can create Singularity images by importing from
DockerHub or Singularity Hub on ARCHER2 itself. If you wish to create
your own custom image using Singularity then you must install Singularity on a system
where you have root (or administrator) privileges - often your own
laptop or workstation.

There are three different options to install Singularity on your local
system: install Docker and use the [Docker Singularity image](https://quay.io/repository/singularity/singularity?tab=tags) to build
Singularity containers, install a virtual machine that you can use to
build Singularity images, or install Singularity on your local system.

For macOS and Windows users we recommend installing Docker Desktop and
using the official Singularity image to build your own images. For Linux
users, we recommend installing Singularity directly on your local system.

We cover the mechanism that uses Docker in more detail below for macOS
and Windows users. If your local system is Linux, you can find
information on installing Singularity on Linux distribution at:

  - [Installing Singularity on
    Linux](https://sylabs.io/guides/3.7/admin-guide/installation.html#installation-on-linux)

### Building Singularity images using Docker (macOS/Windows)

!!! tip
    You should install Docker Desktop to allow you to build Singularity
    images using Docker. Instructions can be found at:
    
     - [Installing Docker Desktop on Windows Home/Professional](https://docs.docker.com/docker-for-windows/install/) 
     - [Installing Docker Desktop on macOS](https://docs.docker.com/docker-for-mac/install/)

Once you have installed Docker, you can build Singularity images with a
command similar to:

```bash
docker run -it --privileged --rm -v ${PWD}:/home/singularity quay.io/singularity/singularity:v3.7.0-slim build /home/singularity/my_image.sif /home/singularity/my_image.def
```

!!! tip
    You can setup an alias to the long `docker run` command above to make it easier
    to use. For example, if you are using bash or zsh you could use a command like
    `alias dsingularity="docker run -it --privileged --rm -v ${PWD}:/home/singularity quay.io/singularity/singularity:v3.7.0-slim"`

For information on how to write Singularity image definition files, see the
[Singularity documentation](https://sylabs.io/guides/3.7/user-guide/definition_files.html).

!!! tip
    You can, of course, also get to a Singularity image via a Docker image
    (as Singularity can use images directly from the DockerHub). In this workflow
    you would create a Docker image, upload it to the DockerHub and then pull it
    using Singularity on ARCHER2.

## Using Singularity with MPI on ARCHER2

!!! note
    This information is based on that in the [Introduction to Singularity](https://carpentries-incubator.github.io/singularity-introduction/)
    lesson from the [Carpentries Incubator](https://carpentries.org/community-lessons/#the-carpentries-incubator). Citation
    J. Cohen and A. Turner. "Reproducible computational environments using containers: Introduction to Singularity". Version 2020.08a, 
    August 2020. Carpentries Incubator. https://github.com/carpentries-incubator/singularity-introduction

MPI on ARCHER2 is provided by the Cray MPICH libraries with the interface
to the high-performance Slingshot interconnect provided via the OFI interface.
Therefore, as per the [Singularity MPI Hybrid model](https://sylabs.io/guides/3.7/user-guide/mpi.html#hybrid-model), we will build our Singularity image such
that it contains a version of the MPICH MPI library compiled with support for OFI.
Below, we provide instructions on creating a container with
a version of MPICH compiled in this way, but, for convenience, we 
also provide a base image file that already has a suitable MPICH, and so, you
can skip this step if you wish. Instructions on how to use this image as the
base for your own images are also provided. Finally, we provide an 
example of how to run a Singularity container with MPI over multiple 
ARCHER2 compute nodes.

### Building an image with MPI from scratch

!!! note
    These instructions are based on those used in the [Reproducible computational environments using containers: Introduction to Singularity](https://carpentries-incubator.github.io/singularity-introduction/).

!!! warning
    Remember, all these steps should be executed on your local system where
    you have administrator privileges, **not on ARCHER2**.

We will illustrate the process of building a Singularity image with MPI from
scratch by building an image that contains MPI provided by MPICH and the OSU
MPI benchmarks. In order to do this, we first need to download the source code
for both MPICH and the OSU benchmarks. At the time of writing, the stable MPICH
release is 3.4.2 and the stable OSU benchmark release is 5.8 - this may have
changed by the time you are following these instructions.

Go ahead and download the source code:

```bash
wget http://www.mpich.org/static/downloads/3.4.2/mpich-3.4.2.tar.gz
wget https://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.8.tgz
```

Now create a Singularity image definition file that describes how to build the
image:

```
Bootstrap: docker
From: ubuntu:20.04

%files
    /home/singularity/osu-micro-benchmarks-5.8.tar.gz /root/
    /home/singularity/mpich-3.4.2.tar.gz /root/

%environment
    export SINGULARITY_MPICH_DIR=/usr

%post
    apt-get -y update && DEBIAN_FRONTEND=noninteractive apt-get -y install build-essential libfabric-dev libibverbs-dev gfortran
    cd /root
    tar zxvf mpich-3.4.2.tar.gz && cd mpich-3.4.2
    echo "Configuring and building MPICH..."
    ./configure --prefix=/usr --with-device=ch3:nemesis:ofi && make -j2 && make install
    cd /root
    tar zxvf osu-micro-benchmarks-5.8.tar.gz
    cd osu-micro-benchmarks-5.8/
    echo "Configuring and building OSU Micro-Benchmarks..."
    ./configure --prefix=/usr/local/osu CC=/usr/bin/mpicc CXX=/usr/bin/mpicxx
    make -j2 && make install

%runscript
    exec /usr/local/osu/libexec/osu-micro-benchmarks/mpi/$*
```

A quick overview of what the above definition file is doing:

 - The image is being bootstrapped from the `ubuntu:20.04` Docker image.
 - In the `%files` section: The OSU Micro-Benchmarks and MPICH tar files are copied from the current directory into the `/root` directory in the image.
 - In the `%environment` section: Set an environment variable that will be available within all containers run from the generated image.
 - In the `%post` section:
    - Ubuntu's `apt-get` package manager is used to update the package directory and then install the compilers and other libraries required for the MPICH build.
    - The MPICH `.tar.gz` file is extracted and the configure, build and install steps are run. Note the use of the `--with-device` option to configure MPICH to use the correct driver to support improved communication performance on a high performance cluster.
    - The OSU Micro-Benchmarks `tar.gz` file is extracted and the configure, build and install steps are run to build the benchmark code from source.
 - In the `%runscript` section: A runscript is set up that will echo the rank number of the current process and then run the command provided as a command line argument.

_Note that the base path of the the executable to run is hardcoded in the run script_, so the command line parameter to provide when running a container based on this image is relative to this base path, for example, `startup/osu_hello`, `collective/osu_allgather`, `pt2pt/osu_latency`, `one-sided/osu_put_latency`.

!!! info
    You can find more information on Singularity definition file syntax in the
    [Singularity documentation](https://sylabs.io/guides/3.7/user-guide/definition_files.html).

Now go ahead and build the Singularity image using Singularity via Docker:

```bash
docker run -it --privileged --rm -v ${PWD}:/home/singularity quay.io/singularity/singularity:v3.7.0-slim build /home/singularity/osu_benchmarks.sif /home/singularity/osu_benchmarks.def
```

Once you have successfully created your Singularity image file, `osu_benchmarks.sif`, use `scp` to 
copy it to ARCHER2 and you can test as described in the section below.

!!! tip
    You can setup an alias to the long `docker run` command above to make it easier
    to use. For example, if you are using bash or zsh you could use a command like
    `alias dsingularity="docker run -it --privileged --rm -v ${PWD}:/home/singularity quay.io/singularity/singularity:v3.7.0-slim"`

!!! tip
    You can find a copy of the `osu_benchmarks.sif` image on ARCHER2 in the directory
    `$EPCC_SINGULARITY_DIR` if you do not want to build it yourself but still want to
    test.

### Creating an image based on the base ARCHER2 MPI Singularity image

We have built an image with MPICH 3.4.1 built against OFI that you can use as a base
image to install further software and create your own images. The image can be found
on ARCHER2 at `$EPCC_SINGULARITY_DIR/mpich_base.sif`.

To use this image in your own image builds you should download it from ARCHER2 to the
system where you are building your images. You can then add the following lines to 
your image definition file to start from this base image:

```
Bootstrap: localimage
From: /path/to/container/mpich_base.sif
```

(Remember to put the actual path to the `mpich_base.sif` image file that you have
downloaded to your system.)

### Running parallel MPI jobs using Singularity containers

!!! tip
    These instructions assume you have a Singularity image uploaded to 
    ARCHER2 that includes MPI provided by MPICH with the OFI interface.
    See the sections above for how to create such images.

Once you have uploaded your Singularity image that includes MPICH built with
OFI for ARCHER2, you can use it to run parallel jobs in a similar way to
non-Singularity jobs. The example job submission script below uses the image
we built above with MPICH and the OSU benchmarks to run the Allreduce benchmark
on two nodes where all 128 cores on each node are used for MPI processes (so, 256
MPI processes in total).

=== "Full system"
    ```bash
    #!/bin/bash

    # Slurm job options (name, compute nodes, job time)
    #SBATCH --job-name=singularity_parallel
    #SBATCH --time=0:10:0
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    #SBATCH --account=[budget code]

    # Set the number of threads to 1.
    # This prevents any threaded system libraries from automatically using threading.
    export OMP_NUM_THREADS=1

    # Set the LD_LIBRARY_PATH environment variable within the Singularity container
    # to ensure that it used the correct MPI libraries.
    export SINGULARITYENV_LD_LIBRARY_PATH= \
        /opt/cray/pe/mpich/8.1.9/ofi/gnu/9.1/lib-abi-mpich: \
        /opt/cray/pe/pmi/6.0.13/lib: \
        /opt/cray/libfabric/1.11.0.4.71/lib64: \
        /usr/lib64/host: \
        /usr/lib/x86_64-linux-gnu/libibverbs: \
        /.singularity.d/libs

    # Set the options for the Singularity executable.
    # This makes sure Cray Slingshot interconnect libraries are available
    # from inside the container.
    BIND_OPTS="-B /opt/cray,/usr/lib64:/usr/lib64/host,/usr/lib64/tcl"
    BIND_OPTS="${BIND_OPTS},/var/spool/slurmd/mpi_cray_shasta"

    # Launch the parallel job.
    srun --hint=nomultithread --distribution=block:block \
        singularity run ${BIND_OPTS} osu_benchmarks.sif \
            collective/osu_allreduce
    ```

The only changes from a standard submission script are:

- We set the environment variable `SINGULARITY_LD_LIBRARY_PATH` to ensure the correct libraries are available within the container to be able to use Cray MPICH.
- `srun` calls the `singularity` software with the image file we created rather than the parallel program directly.

!!! important
    Remember that the image file must be located on `/work` to run jobs on the
    compute nodes.

If the job runs correctly, you should see output similar to the following in your `slurm-*.out` 
file:

```
# OSU MPI Allreduce Latency Test v5.7
# Size       Avg Latency(us)
4                       6.81
8                       6.86
16                      6.90
32                      7.09
64                     10.79
128                    10.97
256                    13.07
512                    15.50
1024                   17.35
2048                   22.49
4096                   41.13
8192                   54.00
16384                  75.09
32768                 120.40
65536                 243.26
131072                614.60
262144                618.11
524288                877.92
1048576              2981.61
```