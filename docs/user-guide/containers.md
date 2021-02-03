# Containers

This page was originally based on the documentation at the
[University of Sheffield HPC service](http://docs.hpc.shef.ac.uk/en/latest/sharc/software/apps/singularity.html)

Designed around the notion of mobility of compute and reproducible
science, Singularity enables users to have full control of their
operating system environment. This means that a non-privileged user can
"swap out" the Linux operating system and environment on the host for a
Linux OS and environment that they control. So if the host system is
running CentOS Linux but your application runs in Ubuntu Linux with a
particular software stack; you can create an Ubuntu image, install your
software into that image, copy the image to another host (e.g. ARCHER2),
and run your application on that host in it’s native Ubuntu environment.

Singularity also allows you to leverage the resources of whatever host
you are on. This includes high-speed interconnects (i.e. Slingshot on
ARCHER2), file systems (i.e. /lustre on ARCHER2) and potentially other
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
[SingularityHub](https://singularity-hub.org/)) can simply be downloaded
and used on ARCHER2 (or anywhere else Singularity is installed).

Creating and modifying images requires root permission and so must be
done on a system where you have such access (in practice, this is
usually within a virtual machine on your laptop/workstation).

## Using Singularity Images on ARCHER2

Singularity images can be used on ARCHER2 in a number of ways,
including:

  - Interactively on the login nodes
  - Interactively on compute nodes
  - As serial processes within a non-interactive batch script
  - As parallel processes within a non-interactive batch script

We provide information on each of these scenarios (apart from the
parallel use where we are still preparing the documentation) below.
First, we describe briefly how to get existing images onto ARCHER2 so
you can use them.

### Getting existing images onto ARCHER2

Singularity images are files, so, if you already have an image
file, you can use `scp` to copy the file to ARCHER2 as you would with
any other file.

If you wish to get a file from one of the container image repositories
then Singularity allows you to do this from ARCHER2 itself.

For example, to retrieve an image from SingularityHub on Cirrus we can
simply issue a Singularity command to pull the image.

    [user@archer2-login0 ~]$ singularity pull hello-world.sif shub://vsoch/hello-world

The image located at the `shub` URI is written to a Singularity Image
File (SIF) called `hello-world.sif`.

### Interactive use on the login nodes

Once you have an image file, using it on the login nodes in an
interactive way is extremely simple: you use the `singularity shell`
command. Using the image we built in the example above:

    [auser@uan01 ~]$ singularity shell hello-world.sif
    Singularity> 

Within a Singularity image your home directory will be available.

Once you have finished using your image, you can return to the ARCHER2
login node command line with the `exit` command:

    Singularity> exit
    exit
    [auser@uan01 ~]$

### Interactive use on the compute nodes

The process for using an image interactively on the compute nodes is
very similar to that for using them on the login nodes. The only
difference is that you have to submit an interactive serial job to get
interactive access to the compute node first.

For example, to reserve a full node for you to work on interactively you
would use:

    auser@uan01:/work/t01/t01/auser> srun --nodes=1 --exclusive --time=00:20:00 --account=[budget code] \
                                          --partition=standard --qos=standard --pty /bin/bash
    
    ...wait until job starts...
    
    auser@nid00001:/work/t01/t01/auser>

Note that the prompt has changed to show you are on a compute node. Now
you can use the image in the same way as on the login node.

    auser@nid00001:/work/t01/t01/auser> singularity shell hello-world.sif
    Singularity> exit
    exit
    auser@nid00001:/work/t01/t01/auser> exit
    auser@uan01:/work/t01/t01/auser>

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
    
    # Setup the batch environment
    module load epcc-job-env
    
    # Run the serial executable
    singularity run $SLURM_SUBMIT_DIR/hello-world.sif

You submit this in the usual way and the standard output and error
should be written to `slurm-...`, where the output filename ends
with the job number.

## Creating Your Own Singularity Images

As we saw above, you can create Singularity images by importing from
DockerHub or Singularity Hub on ARCHER2 itself. If you wish to create
your own custom image then you must install Singularity on a system
where you have root (or administrator) privileges - often your own
laptop or workstation.

We provide links below to instructions on how to install Singularity
locally and then cover what options you need to include in a Singularity
recipe file to create images that can run on ARCHER2 and access the
software development modules. (This can be useful if you want to create
a custom environment but still want to compile and link against
libraries that you only have access to on ARCHER2 such as the Intel
compilers, HPE MPI libraries, etc.)

### Installing Singularity on Your Local Machine

You will need Singularity installed on your machine in order to locally
run, create and modify images. How you install Singularity on your
laptop/workstation depends on the operating system you are using.

If you are using Windows or macOS, the simplest solution is to use
[Vagrant](http://www.vagrantup.com) to give you an easy to use virtual
environment with Linux and Singularity installed. The Singularity
website has instructions on how to use this method to install
Singularity:

  - [Installing Singularity on macOS with
    Vagrant](https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-mac)
  - [Installing Singularity on Windows with
    Vagrant](https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-windows)

If you are using Linux then you can usually install Singularity
directly, see:

  - [Installing Singularity on
    Linux](https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-linux)
