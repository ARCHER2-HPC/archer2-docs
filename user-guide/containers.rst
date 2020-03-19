Containers
==========

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This page was originally based on the documentation at the `University of Sheffield HPC service:
<http://docs.hpc.shef.ac.uk/en/latest/sharc/software/apps/singularity.html>`_.

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
is required by Docker) it is suitable for use on a multi-user HPC system such as ARCHER2.
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
get exisitng images onto ARCHER2 so you can use them.

Getting existing images onto ARCHER2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Singularity images are simply files so, if you already have an image file, you can use
``scp`` to copy the file to ARCHER2 as you would with any other file.

If you wish to get a file from one of the container image repositories then Singularity
allows you to do this from ARCHER2 itself.

This functionality requires tools that are not part of the standard OS on ARCHER2 so we have
provided a Singularity image that allows you to build images from remote repositories (i.e.
you use a Singularity image to build Singularity images!).

For example, to retrieve an image from DockerHub on ARCHER2 we fist need to enter an
interactive session in the image we provide for building Singularity images:

::

   [user@archer2-login0 ~]$ module load singularity
   [user@archer2-login0 ~]$ singularity exec $CIRRUS_SIMG/archer2-sbuild.simg /bin/bash --login
   Singularity>

This invokes a login bash shell within the ``$CIRRUS_SIMG/archer2-sbuild.simg`` image as
indicated by our prompt change. (We need a login shell to allow ``module`` commands to work
within the image.)

Now we are in the image we can load the singularity module (to get access to the Singularity
commands) and pull an image from DockerHub:

::

   Singularity> module load singularity
   Singularity> singularity build lolcow.simg docker://godlovedc/lolcow
   Docker image path: index.docker.io/godlovedc/lolcow:latest
   Cache folder set to /lustre/home/t01/user/.singularity/docker
   Importing: base Singularity environment
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:9fb6c798fa41e509b58bccc5c29654c3ff4648b608f5daa67c1aab6a7d02c118.tar.gz
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:3b61febd4aefe982e0cb9c696d415137384d1a01052b50a85aae46439e15e49a.tar.gz
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:9d99b9777eb02b8943c0e72d7a7baec5c782f8fd976825c9d3fb48b3101aacc2.tar.gz
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:d010c8cf75d7eb5d2504d5ffa0d19696e8d745a457dd8d28ec6dd41d3763617e.tar.gz
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:7fac07fb303e0589b9c23e6f49d5dc1ff9d6f3c8c88cabe768b430bdb47f03a9.tar.gz
   Importing: /lustre/home/t01/user/.singularity/docker/sha256:8e860504ff1ee5dc7953672d128ce1e4aa4d8e3716eb39fe710b849c64b20945.tar.gz
   Importing: /lustre/home/t01/user/.singularity/metadata/sha256:ab22e7ef68858b31e1716fa2eb0d3edec81ae69c6b235508d116a09fc7908cff.tar.gz
   WARNING: Building container as an unprivileged user. If you run this container as root
   WARNING: it may be missing some functionality.
   Building Singularity image...
   Singularity container built: lolcow.simg
   Cleaning up...

The first argument to ``singularity build`` (lolcow.simg) specifies a path and name for your container.
The second argument (docker://godlovedc/lolcow) gives the DockerHub URI from which to download the image.

Now we can exit the image and run our new image we have just built on the ARCHER2 login node:

::

   [user@archer2-login0 ~]$ singularity run lolcow.simg

This image contains a *runscript* that tells Singularity what to do if we run the image. We demonstrate
different ways to use images below.

Similar syntax can be used for Singularity Hub. For more information see the Singularity documentation:

* `Build a Container <https://www.sylabs.io/guides/2.6/user-guide/build_a_container.html>`_


Interactive use on the login nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have an image file, using it on the login nodes in an interactive way is extremely simple:
you use the ``singularity shell`` command. Using the image we built in the example above:

::

   [user@archer2-login0 ~]$ module load singularity
   [user@archer2-login0 ~]$ singularity shell lolcow.simg
   Singularity: Invoking an interactive shell within container...

   Singularity lolcow.simg:~>

Within a Singularity image your home directory will be available. The directory with
centrally-installed software (``/lustre/sw``) is also available in images by default. Note that
the ``module`` command will not work in images unless you have installed he required software and
configured the environment correctly; we describe how to do this below.

Once you have finished using your image, you return to the ARCHER2 login node command line with the
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

Note the prompt has changed to show you are on a compute node. Now you can use the image
in the same way as on the login node

::

   [user@r1i2n13 ~]$ module load singularity
   [user@r1i2n13 ~]$ singularity shell lolcow.simg
   Singularity: Invoking an interactive shell within container...

   Singularity lolcow.simg:~> exit
   exit
   [user@r1i2n13 ~]$ exit
   [user@archer2-login0 ~]$

Note we used ``exit`` to leave the interactive image shell and then ``exit`` again to leave the
interactive job on the compute node.

Serial processes within a non-interactive batch script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can also use Singularity images within a non-interactive batch script as you would any
other command. If your image contains a *runscript* then you can use ``singularity run`` to
execute the runscript in the job. You can also use ``singularity exec`` to execute arbitrary
commands (or scripts) within the image.

An exmaple job submission script to run a serial job that executes the runscript within the
``lolcow.simg`` we built above on ARCHER2 would be:

::

    #!/bin/bash --login

    # PBS job options (name, compute nodes, job time)
    #PBS -N simg_test
    #PBS -l select=1:ncpus=1
    #PBS -l walltime=0:20:0

    # Replace [budget code] below with your project code (e.g. t01)
    #PBS -A [budget code]

    # Change to the directory that the job was submitted from
    cd $PBS_O_WORKDIR

    # Load any required modules
    module load singularity

    # Run the serial executable
    singularity run $HOME/lolcow.simg

You submit this in the usual way and the output would be in the STDOUT/STDERR files in the
usual way.


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

If yout are using Windows or macOS, the simplest solution is to use
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

   [user@archer2-login0 ~]$ module load singularity
   [user@archer2-login0 ~]$ singularity exec archer2-mods.simg /bin/bash --login
   Singularity> module avail intel-compilers

   ------------------------- /lustre/sw/modulefiles ---------------------
   intel-compilers-16/16.0.2.181
   intel-compilers-16/16.0.3.210(default)
   intel-compilers-17/17.0.2.174(default)
