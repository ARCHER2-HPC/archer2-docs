Containers
==========

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This page was originally based on the documentation at the `University of Sheffield HPC service
<http://docs.hpc.shef.ac.uk/en/latest/sharc/software/apps/singularity.html>`__.

Designed around the notion of mobility of compute and reproducible science,
Singularity enables users to have full control of their operating system environment.
This means that a non-privileged user can "swap out" the Linux operating system and
environment on the host for a Linux OS and environment that they control.
So if the host system is running CentOS Linux but your application runs in Ubuntu Linux
with a particular software stack; you can create an Ubuntu image, install your software
into that image, copy the image to another host (e.g. ARCHER2), and run your application
on that host in it’s native Ubuntu environment.

Singularity also allows you to leverage the resources of whatever host you are on.
This includes high-speed interconnects (i.e. Infinband on ARCHER2),
file systems (i.e. /lustre on ARCHER2) and potentially other resources (e.g. the
licensed Intel compilers on ARCHER2).

**Note:** Singularity only supports Linux containers. You cannot create images
that use Windows or macOS (this is a restriction of the containerisation model
rather than Singularity).

Useful Links
------------

* `Singularity website <https://www.sylabs.io/>`_
* `Singularity documentation <https://www.sylabs.io/docs/>`_

About Singularity Containers (Images)
-------------------------------------

Similar to Docker,
a Singularity container (or, more commonly, *image*) is a self-contained software stack.
As Singularity does not require a root-level daemon to run its images (as
is required by Docker) it is suitable for use on multi-user HPC systems such as ARCHER2.
Within the container/image, you have exactly the same permissions as you do in a
standard login session on the system.

In practice, this means that an image created on your local machine
with all your research software installed for local development
will also run on ARCHER2.

Pre-built images (such as those on `DockerHub <http://hub.docker.com>`_ or
`SingularityHub <https://singularity-hub.org/>`_) can simply be downloaded
and used on ARCHER2 (or anywhere else Singularity is installed); see
:ref:`use_image_singularity`).

Creating and modifying images requires root permission and so
must be done on a system where you have such access (in practice, this is
usually within a virtual machine on your laptop/workstation); see
:ref:`create_image_singularity`.

.. _use_image_singularity:

Using Singularity Images on ARCHER2
-----------------------------------

Singularity images can be used on ARCHER2 in a number of ways, including:

* Interactively on the login nodes
* Interactively on compute nodes
* As serial processes within a non-interactive batch script
* As parallel processes within a non-interactive batch script (not yet documented)

We provide information on each of these scenarios (apart from the parallel use where
we are still preparing the documentation) below. First, we describe briefly how to
get existing images onto ARCHER2 so you can use them.

Getting existing images onto ARCHER2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Singularity images are simply files, so, if you already have an image file, you can use
``scp`` to copy the file to ARCHER2 as you would with any other file.

If you wish to get a file from one of the container image repositories then Singularity
allows you to do this from ARCHER2 itself.

For example, to retrieve an image from SingularityHub on Cirrus we can simply issue a Singularity
command to pull the image.

::

   [user@cirrus-login1 ~]$ singularity pull hello-world.sif shub://vsoch/hello-world

The image located at the ``shub`` URI is written to a Singularity Image File (SIF) called ``hello-world.sif``.

Interactive use on the login nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have an image file, using it on the login nodes in an interactive way is extremely simple:
you use the ``singularity shell`` command. Using the image we built in the example above:

::

   [user@archer2-login0 ~]$ singularity shell lolcow.simg
   Singularity: Invoking an interactive shell within container...

   Singularity lolcow.simg:~>

Within a Singularity image your home directory will be available. The directory with
centrally-installed software (``/lustre/sw``) is also available in images by default. Note that
the ``module`` command will not work in images unless you have installed the required software and
configured the environment correctly; we describe how to do this below.

Once you have finished using your image, you can return to the ARCHER2 login node command line with the
``exit`` command:

::

   Singularity lolcow.simg:~> exit
   exit
   [user@archer2-login0 ~]$

Interactive use on the compute nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The process for using an image interactively on the compute nodes is very similar to that for
using them on the login nodes. The only difference is that you have to submit an interactive
serial job to get interactive access to the compute node first.

For example, to reserve a full node for you to work on interactively you would use:

::

   [user@archer2-login0 ~]$ qsub -IVl select=1:ncpus=36,walltime=0:20:0,place=scatter:excl -A t01
   qsub: waiting for job 234192.indy2-login0 to start

   ...wait until job starts...

   qsub: job 234192.indy2-login0 ready

   [user@r1i2n13 ~]$

Note that the prompt has changed to show you are on a compute node. Now you can use the image
in the same way as on the login node.

::

   [user@r1i2n13 ~]$ singularity shell lolcow.simg
   Singularity: Invoking an interactive shell within container...

   Singularity lolcow.simg:~> exit
   exit
   [user@r1i2n13 ~]$ exit
   [user@archer2-login0 ~]$

Note how we used ``exit`` to leave the interactive image shell and then ``exit`` again to leave the
interactive job on the compute node.

Serial processes within a non-interactive batch script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can also use Singularity images within a non-interactive batch script as you would any
other command. If your image contains a *runscript* then you can use ``singularity run`` to
execute the runscript in the job. You can also use ``singularity exec`` to execute arbitrary
commands (or scripts) within the image.

An example job submission script to run a serial job that executes the runscript within the
``lolcow.simg`` image that we built previously on an ARCHER2 login node would be as follows.

::

   #!/bin/bash --login

   # Slurm job options (name, compute nodes, job time)
   
   #SBATCH -J simgtest
   #SBATCH -o simgtest.o%j
   #SBATCH -e simgtest.o%j
   #SBATCH --nodes=1
   #SBATCH --ntasks=1
   #SBATCH --time=00:10:00

   #SBATCH --account= [budget code]
   #SBATCH --partition= standard
   #SBATCH --qos=standard

   # Setup the batch environment
   module load epcc-job-env

   # Run the serial executable
   singularity run $HOME/lolcow.simg

You submit this in the usual way and the standard output and error should be written to ``simgtest.o...``,
where the output filename ends with the job number.

.. _create_image_singularity:

Creating Your Own Singularity Images
------------------------------------

As we saw above, you can create Singularity images by importing from
DockerHub or Singularity Hub on ARCHER2 itself. If you wish to create your
own custom image then you must install Singularity on a system where you
have root (or administrator) privileges - often your own laptop or
workstation.

We provide links below to instructions on how to install Singularity
locally and then cover what options you need to include in a
Singularity recipe file to create images that can run on ARCHER2 and
access the software development modules. (This can be useful if you
want to create a custom environment but still want to compile and
link against libraries that you only have access to on ARCHER2 such
as the Intel compilers, HPE MPI libraries, etc.)

Installing Singularity on Your Local Machine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You will need Singularity installed on your machine in order to locally run,
create and modify images. How you install Singularity on your laptop/workstation
depends on the operating system you are using.

If you are using Windows or macOS, the simplest solution is to use
`Vagrant <http://www.vagrantup.com>`_ to give you an easy to use virtual
environment with Linux and Singularity installed. The Singularity website
has instructions on how to use this method to install Singularity:

* `Installing Singularity on macOS with Vagrant <https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-mac>`_
* `Installing Singularity on Windows with Vagrant <https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-windows>`_

If you are using Linux then you can usually install Singularity directly, see:

* `Installing Singularity on Linux <https://www.sylabs.io/guides/2.6/user-guide/installation.html#install-on-linux>`_

Singularity Recipes to Access modules on ARCHER2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may want your custom image to be able to access the modules environment
on ARCHER2 so you can make use of custom software that you cannot access
elsewhere. We demonstrate how to do this for a CentOS 7 image but the steps
are easily translated for other flavours of Linux.

For the ARCHER2 modules to be available in your Singularity container you need to
ensure that the ``environment-modules`` package is installed in your image.

In addition, when you use the container you must invoke access as a login
shell to have access to the module commands.

Here is an example recipe file to build a CentOS 7 image with access to
TCL modules alread installed on ARCHER2:

::

   BootStrap: docker
   From: centos:centos7

   %post
       yum update -y
       yum install environment-modules -y

If we save this recipe to a file called ``archer2-mods.def`` then we can use the
following command to build this image (remember this command must be run on a
system where you have root access, not ARCHER2):

::

   me@my-system:~> sudo singularity build archer2-mods.simg archer2-mods.def

The resulting image file (``archer2-mods.simg``) can then be compied to ARCHER2
using scp.

When you use the image interactively on ARCHER2 you must start with a login
shell, i.e.:

::

   [user@archer2-login0 ~]$ singularity exec archer2-mods.simg /bin/bash --login
   Singularity> module avail intel-compilers

   ------------------------- /lustre/sw/modulefiles ---------------------
   intel-compilers-16/16.0.2.181
   intel-compilers-16/16.0.3.210(default)
   intel-compilers-17/17.0.2.174(default)
