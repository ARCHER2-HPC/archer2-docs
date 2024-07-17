# Using Spack on ARCHER2

[Spack](https://github.com/spack/spack) is a package manager, a tool to assist with building and installing software as well as determining what dependencies are required and installing those. It was originally designed for use on HPC clusters, where several variations of a given package may be installed alongside one another for different use cases -- for example different versions, built with different compilers, using MPI or hybrid MPI+OpenMP. Spack is principally written in Python but has a component written in Answer Set Programming (ASP) which is used to determine the required dependencies for a given package installation.

Users are welcome to install Spack themselves in their own directories, but we are making an experimental installation tailored for ARCHER2 available centrally. This page provides documentation on how to activate and install packages using the central installation on ARCHER2. For more in-depth information on using Spack itself please see the [developers' documentation](https://spack.readthedocs.io/en/latest/).

!!! important
    As our Spack installation is still in an experimental stage we cannot guarantee that it will work with full functionality and may not be able to provide support.

## Activating Spack

As it is still in an experimental stage, the Spack module is not made available to users by default. The development modules must firstly be enabled by running:
    
    auser@ln01:~> module use /work/y07/shared/archer2-lmod/apps/dev

Several modules with `spack` in their name will become visible to you. You should simply load the `spack` module:

    auser@ln01:~> module load spack

This configures Spack to place its cache on and install software to a directory called `.spack` in your base work directory, e.g. at `/work/t01/t01/auser/.spack`.

!!! tip
    Spack is also configured to allow it to re-use as dependencies any packages that the ARCHER2 CSE team has `spack install`ed centrally, potentially helping to save you build time and storage quota.

At this point Spack is available to you via the `spack` command. You can get started with `spack help`, reading the [Spack documentation](https://spack.readthedocs.io/en/latest/), or by testing a package's installation.

## Contributing

If you develop a package for use on ARCHER2 please feel free to open a pull request to the [GitHub repository](https://github.com/EPCCed/spack-archer2).