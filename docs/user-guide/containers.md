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

Similar to Docker, a Singularity container is a self-contained software stack.
As Singularity does not require a root-level daemon to run its containers
(as is required by Docker) it is suitable for use on multi-user HPC systems
such as ARCHER2. Within the container, you have exactly the same permissions
as you do in a standard login session on the system.

In practice, this means that a container image created on your local machine with
all your research software installed for local development will also run
on ARCHER2.

Pre-built container images (such as those on [DockerHub](http://hub.docker.com) or
[SingularityHub archive](https://datasets.datalad.org/?dir=/shub/) can simply be downloaded
and used on ARCHER2 (or anywhere else Singularity is installed).

Creating and modifying container images requires root permission and so must be
done on a system where you have such access (in practice, this is
usually within a virtual machine on your laptop/workstation).

!!! Note SingularityHub now read-only
    SingularityHub was a publicly available cloud service for Singularity container images active from 2016 to 2021. It built container recipes from Github repositories on Google Cloud, and container images were available via the command line Singularity or sregistry software. These container images are still available now in the [SingularityHub Archive](https://datasets.datalad.org/?dir=/shub/)


## Using Singularity Images on ARCHER2

Singularity containers can be used on ARCHER2 in a number of ways,
including:

  - Interactively on the login nodes
  - Interactively on compute nodes
  - As serial processes within a non-interactive batch script
  - As parallel processes within a non-interactive batch script

We provide information on each of these scenarios below. First,
we describe briefly how to get existing container images onto ARCHER2 so
that you can launch containers based on them.

### Getting existing container images onto ARCHER2

Singularity container images are files, so, if you already have a container image,
you can use `scp` to copy the file to ARCHER2 as you would with
any other file.

If you wish to get a file from one of the container image repositories,
then Singularity allows you to do this from ARCHER2 itself.

For example, to retrieve a container image from SingularityHub on ARCHER2 we can
simply issue a Singularity command to pull the image.

    auser@ln03:~> singularity pull hello-world.sif shub://vsoch/hello-world

The container image located at the `shub` URI is written to a Singularity Image
File (SIF) called `hello-world.sif`.

### Interactive use on the login nodes

Once you have a container image file, launching a container based on the container image on the login nodes in an
interactive way is extremely simple: you use the `singularity shell`
command. Using the container image we built in the example above:

    auser@ln03:~> singularity shell hello-world.sif
    Singularity> 

Within a Singularity container your home directory will be available.

Once you have finished using your container, you can return to the ARCHER2
login node prompt with the `exit` command:

    Singularity> exit
    exit
    auser@ln03:~>

### Interactive use on the compute nodes

The process for using a container interactively on the compute nodes is
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
you can launch a container in the same way as on the login node.

    auser@nid00001:/work/t01/t01/auser> singularity shell hello-world.sif
    Singularity> exit
    exit
    auser@nid00001:/work/t01/t01/auser> exit
    auser@ln03:/work/t01/t01/auser>

!!! note
    We used `exit` to leave the interactive container shell and then
    `exit` again to leave the interactive job on the compute node.

### Serial processes within a non-interactive batch script

You can also use Singularity containers within a non-interactive batch
script as you would any other command. If your container image contains a
*runscript* then you can use `singularity run` to execute the runscript
in the job. You can also use `singularity exec` to execute arbitrary
commands (or scripts) within the container.

An example job submission script to run a serial job that executes the
runscript within a container based on the container image in the `hello-world.sif`
file that we downloaded previously to an ARCHER2 login node would be as follows.

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
and the containerised application.

The Singularity container image must be compatible with the MPI environment on the host; either, the containerised
app has been built against the appropriate MPI libraries or the container itself contains an MPI library
that is compatible with the host MPI. The latter situation is known as the [hybrid model](https://sylabs.io/guides/3.7/user-guide/mpi.html#hybrid-model);
this is the approach taken in the sections that follow.

## Creating Your Own Singularity Container Images

As we saw above, you can create Singularity container images by importing from
DockerHub or Singularity Hub on ARCHER2 itself. If you wish to create
your own custom container image to use with Singularity then you must use a system
where you have root (or administrator) privileges - often your own
laptop or workstation.

There are a number of different options to create container images on your local
system to use with Singularity on ARCHER2. We are going to use Docker on our 
local system to create the container image, push the new container image to
Docker Hub and then use Singularity on ARCHER2 to convert the Docker container
image to a Singularity container image SIF file.

For macOS and Windows users we recommend installing Docker Desktop. For Linux
users, we recommend installing Docker directly on your local system. See the
Docker documentation for full details on how to install Docker Desktop/Docker.

### Building container images using Docker

!!! note
    We assume that you are familiar with using Docker in these instructions. You 
    can find an introduction to Docker at
    [Reproducible Computational Environments Using Containers: Introduction to Docker](https://carpentries-incubator.github.io/docker-introduction/)

As usual, you can build container images with a command similar to:

```bash
docker build --platform linux/amd64 -t <username>/<image name>:<version> .
```

Where:
- `<username>` is your Docker Hub username
- `<image name>` is the name of the container image you wish to create
- `<version>` - specifies the version of the image you are creating (e.g. "latest", "v1")
- `.` is the *build context* - in this example it is the location of the Dockerfile

Note, you should use the `--platform linux/amd64` option to ensure that the container
image is compatible with the processor architecture on ARCHER2.

## Using Singularity with MPI on ARCHER2

MPI on ARCHER2 is provided by the Cray MPICH libraries with the interface
to the high-performance Slingshot interconnect provided via the OFI interface.
Therefore, as per the [Singularity MPI Hybrid model](https://sylabs.io/guides/3.7/user-guide/mpi.html#hybrid-model), we will build our container image such that it contains a version of the MPICH MPI library compiled with support for OFI.
Below, we provide instructions on creating a container image with
a version of MPICH compiled in this way. We then provide an 
example of how to run a Singularity container with MPI over multiple 
ARCHER2 compute nodes.

### Building an image with MPI from scratch

!!! warning
    Remember, all these steps should be executed on your local system where
    you have administrator privileges and Docker installed, **not on ARCHER2**.

We will illustrate the process of building a Singularity image with MPI from
scratch by building an image that contains MPI provided by MPICH and the OSU
MPI benchmarks. As part of the container image creation we need to download the source code
for both MPICH and the OSU benchmarks. At the time of writing, the stable MPICH
release is 3.4.2 and the stable OSU benchmark release is 5.8 - this may have
changed by the time you are following these instructions.

First, create a Dockerfile that describes how to build the image:

```docker
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# Install the necessary packages (from repo)
RUN apt-get update && apt-get install -y --no-install-recommends \
 apt-utils \
 build-essential \
 curl \
 libcurl4-openssl-dev \
 libzmq3-dev \
 pkg-config \
 software-properties-common
RUN apt-get clean
RUN apt-get install -y dkms
RUN apt-get install -y autoconf automake build-essential numactl libnuma-dev autoconf automake gcc g++ git libtool

# Download and build an ABI compatible MPICH
RUN curl -sSLO http://www.mpich.org/static/downloads/3.4.2/mpich-3.4.2.tar.gz \
   && tar -xzf mpich-3.4.2.tar.gz -C /root \
   && cd /root/mpich-3.4.2 \
   && ./configure --prefix=/usr --with-device=ch4:ofi --disable-fortran \
   && make -j8 install \
   && rm -rf /root/mpich-3.4.2 \
   && rm /mpich-3.4.2.tar.gz

# OSU benchmarks
RUN curl -sSLO http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.4.1.tar.gz \
   && tar -xzf osu-micro-benchmarks-5.4.1.tar.gz -C /root \
   && cd /root/osu-micro-benchmarks-5.4.1 \
   && ./configure --prefix=/usr/local CC=/usr/bin/mpicc CXX=/usr/bin/mpicxx \
   && cd mpi \
   && make -j8 install \
   && rm -rf /root/osu-micro-benchmarks-5.4.1 \
   && rm /osu-micro-benchmarks-5.4.1.tar.gz

# Add the OSU benchmark executables to the PATH
ENV PATH=/usr/local/libexec/osu-micro-benchmarks/mpi/pt2pt:$PATH
ENV PATH=/usr/local/libexec/osu-micro-benchmarks/mpi/collective:$PATH

# path to mlx libraries in Ubuntu
ENV LD_LIBRARY_PATH=/usr/lib/libibverbs:$LD_LIBRARY_PATH
```

A quick overview of what the above Dockerfile is doing:

 - The image is being bootstrapped from the `ubuntu:20.04` Docker image.
 - The first set of `RUN` sections with `apt-get` commands: install the base packages required from the Ubunntu package repos
 - MPICH install: downloads and compiles the MPICH 3.4.2 in a way that is compatible with Cray MPICH on ARCHER2
 - OSU MPI benchmarks install: downloads and compiles the OSU micro benchmarks
 - `ENV` sections: add the OSU benchmark executables to the PATH so they can be executed in the container without specifying the full path; set the correct paths to the network libraries within the container.


Now we can go ahead and build the container image using Docker (this assumes that
you issue the command in the same directory as the Dockerfile you created based on
the specification above):

```bash
docker build --platform linux/amd64 -t auser/osu-benchmarks:5.4.1 .
```

(Remember to change `auser` to your Dockerhub username.)

Once you have successfully built your container image, you should push it to
Dockerhub:

```bash
docker push auser/osu-benchmarks:5.4.1
```

Finally, you need to use Singularity on ARCHER2 to convert the Docker container image
to a Singularity container image file. Log into ARCHER2, move to the work file system
and then use a command like:

```bash
auser@ln01:/work/t01/t01/auser> singularity build osu-benchmarks_5.4.1.sif docker://auser/osu-benchmarks:5.4.1
```

!!! tip
    You can find a copy of the `osu-benchmarks_5.4.1.sif` image on ARCHER2 in the directory
    `$EPCC_SINGULARITY_DIR` if you do not want to build it yourself but still want to
    test.

### Running parallel MPI jobs using Singularity containers

!!! tip
    These instructions assume you have built a Singularity container image file on 
    ARCHER2 that includes MPI provided by MPICH with the OFI interface.
    See the sections above for how to build such container images.

Once you have built your Singularity container image file that includes MPICH built with
OFI for ARCHER2, you can use it to run parallel jobs in a similar way to
non-Singularity jobs. The example job submission script below uses the container image file
we built above with MPICH and the OSU benchmarks to run the Allreduce benchmark
on two nodes where all 128 cores on each node are used for MPI processes (so, 256
MPI processes in total).

```bash
#!/bin/bash

# Slurm job options (name, compute nodes, job time)
#SBATCH --job-name=singularity_parallel
#SBATCH --time=0:10:0
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1

# Replace [budget code] below with your budget code (e.g. t01)
#SBATCH --partition=standard
#SBATCH --qos=standard
#SBATCH --account=[budget code]

# Load the module to make the Cray MPICH ABI available
module load cray-mpich-abi

export OMP_NUM_THREADS=1
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# Set the LD_LIBRARY_PATH environment variable within the Singularity container
# to ensure that it used the correct MPI libraries.
export SINGULARITYENV_LD_LIBRARY_PATH="/opt/cray/pe/mpich/8.1.23/ofi/gnu/9.1/lib-abi-mpich:/opt/cray/pe/mpich/8.1.23/gtl/lib:/opt/cray/libfabric/1.12.1.2.2.0.0/lib64:/opt/cray/pe/gcc-libs:/opt/cray/pe/gcc-libs:/opt/cray/pe/lib64:/opt/cray/pe/lib64:/opt/cray/xpmem/default/lib64:/usr/lib64/libibverbs:/usr/lib64:/usr/lib64"

# This makes sure HPE Cray Slingshot interconnect libraries are available
# from inside the container.
export SINGULARITY_BIND="/opt/cray,/var/spool,/opt/cray/pe/mpich/8.1.23/ofi/gnu/9.1/lib-abi-mpich:/opt/cray/pe/mpich/8.1.23/gtl/lib,/etc/host.conf,/etc/libibverbs.d/mlx5.driver,/etc/libnl/classid,/etc/resolv.conf,/opt/cray/libfabric/1.12.1.2.2.0.0/lib64/libfabric.so.1,/opt/cray/pe/gcc-libs/libatomic.so.1,/opt/cray/pe/gcc-libs/libgcc_s.so.1,/opt/cray/pe/gcc-libs/libgfortran.so.5,/opt/cray/pe/gcc-libs/libquadmath.so.0,/opt/cray/pe/lib64/libpals.so.0,/opt/cray/pe/lib64/libpmi2.so.0,/opt/cray/pe/lib64/libpmi.so.0,/opt/cray/xpmem/default/lib64/libxpmem.so.0,/run/munge/munge.socket.2,/usr/lib64/libibverbs/libmlx5-rdmav34.so,/usr/lib64/libibverbs.so.1,/usr/lib64/libkeyutils.so.1,/usr/lib64/liblnetconfig.so.4,/usr/lib64/liblustreapi.so,/usr/lib64/libmunge.so.2,/usr/lib64/libnl-3.so.200,/usr/lib64/libnl-genl-3.so.200,/usr/lib64/libnl-route-3.so.200,/usr/lib64/librdmacm.so.1,/usr/lib64/libyaml-0.so.2"

# Launch the parallel job.
srun --hint=nomultithread --distribution=block:block \
    singularity run osu-benchmarks_5.4.1.sif \
        osu_allreduce
```

The only changes from a standard submission script are:

- We set the environment variable `SINGULARITY_LD_LIBRARY_PATH` to ensure that the excutable can find the correct libraries are available within the container to be able to use HPE Cray Slingshot interconnect.
- We set the environment variable `SINGULARITY_BIND` to ensure that the correct libraries are available within the container to be able to use HPE Cray Slingshot interconnect.
- `srun` calls the `singularity` software with the container image file we created rather than the parallel program directly.

!!! important
    Remember that the image file must be located on `/work` to run jobs on the
    compute nodes.

If the job runs correctly, you should see output similar to the following in your `slurm-*.out` 
file:

```
Lmod is automatically replacing "cray-mpich/8.1.23" with
"cray-mpich-abi/8.1.23".


# OSU MPI Allreduce Latency Test v5.4.1
# Size       Avg Latency(us)
4                       7.93
8                       7.93
16                      8.13
32                      8.69
64                      9.54
128                    13.75
256                    17.04
512                    25.94
1024                   29.43
2048                   43.53
4096                   46.53
8192                   46.20
16384                  55.85
32768                  83.11
65536                 136.90
131072                257.13
262144                486.50
524288               1025.87
1048576              2173.25
```
