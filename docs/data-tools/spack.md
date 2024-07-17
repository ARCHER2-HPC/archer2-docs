# Spack

[Spack](https://github.com/spack/spack) is a package manager, a tool to assist
with building and installing software as well as determining what dependencies
are required and installing those. It was originally designed for use on HPC
clusters, where several variations of a given package may be installed alongside
one another for different use cases -- for example different versions, built
with different compilers, using MPI or hybrid MPI+OpenMP. Spack is principally
written in Python but has a component written in Answer Set Programming (ASP)
which is used to determine the required dependencies for a given package
installation.

Users are welcome to install Spack themselves in their own directories, but we
are making an experimental installation tailored for ARCHER2 available
centrally. This page provides documentation on how to activate and install
packages using the central installation on ARCHER2. For more in-depth
information on using Spack itself please see the [developers'
documentation](https://spack.readthedocs.io/en/latest/).

!!! important As our Spack installation is still in an experimental stage we
    cannot guarantee that it will work with full functionality and may not be
    able to provide support.

## Activating Spack

As it is still in an experimental stage, the Spack module is not made available
to users by default. The development modules must firstly be enabled by running:
    
    auser@ln01:~> module use /work/y07/shared/archer2-lmod/apps/dev

Several modules with `spack` in their name will become visible to you. You
should simply load the `spack` module:

    auser@ln01:~> module load spack

This configures Spack to place its cache on and install software to a directory
called `.spack` in your base work directory, e.g. at
`/work/t01/t01/auser/.spack`.

At this point Spack is available to you via the `spack` command. You can get
started with `spack help`, reading the [Spack
documentation](https://spack.readthedocs.io/en/latest/), or by testing a
package's installation.

## Using Spack on ARCHER2

### Installing software

At its simplest, Spack installs software with the `spack install` command:

    auser@ln01:~> spack install gromacs

This very simple `gromacs` installation specification, or spec, would install
GROMACS using the default options given by the Spack `gromacs` package. The spec
can be expanded to include which options you like. For example, the command

    auser@ln01:~> spack install gromacs@2024.2%gcc+mpi

would use the GCC compiler to install an MPI-enabled version of GROMACS version
2024.2. You can find information about any Spack package and the options
available to use with the `spack info` command:

    auser@ln01:~> spack info gromacs

When installing a package, Spack will determine what dependencies are required
to support it. If they are not already available to Spack, either as packages
that it has installed beforehand or as external dependencies, then Spack will
also install those, marking them as implicity installed, as opposed to the
explicit installation of the package you requested. If you want to see the
dependencies of a package before you install it, you can use `spack spec`:

    auser@ln01:~> spack spec gromacs@2024.2%gcc+mpi

!!! tip Spack on ARCHER2 has been configured to use as much of the HPE Cray
    Programming Environment as possible. For example, this means that Cray
    LibSci can be used to provide the BLAS, LAPACK and ScaLAPACK dependencies
    and Cray MPICH can provide MPI. It is also configured to allow it to re-use
    as dependencies any packages that the ARCHER2 CSE team has `spack install`ed
    centrally, potentially helping to save you build time and storage quota.

### Using Spack packages

Spack provides a module-like way of making software that you have installed
available to use. If you have a `gromacs` installation, you can make it
available to use with `spack load`

    auser@ln01:~> spack load gromacs

At this point you should be able to use the software as normal. You can then
remove it once again from the environment with `spack unload`

    auser@ln01:~> spack unload gromacs

If you have multiple variants of the same package installed, you can use the
spec to distinguish between them. You can always check what packages have been
installed using the `spack find` command. If no other arguments are given it
will simply list all installed packages, or you can give a package name to
narrow it down:

    auser@ln01:~> spack find gromacs

As Spack builds binaries to use RPATHs, you may be able to use your software
directly, bypassing Spack entirely. You can see your packages' install locations
using `spack find --paths`.

### Maintaining your Spack installations

In any Spack command that requires as an argument a reference to an installed
package, you can provide a hash reference to it rather than its spec. You can
see the first part of the hash by running `spack find -l`, or the full hash with
`spack find -L`. Then use the hash in a command by prefixing it with a forward
slash, e.g. `wjy5dus` becomes `/wjy5dus`.

If you have two packages installed which appear identical in `spack find` apart
from their hash, you can differentiate them with `spack diff`:

    auser@ln01:~> spack diff /wjy5dus /bleelvs

You can uninstall your packages with `spack uninstall`:

    auser@ln01:~> spack uninstall gromacs@2024.2

Uninstalling a package will leave behind any implicitly installed packages that
were installed to support it. Spack may have also installed build-time
dependencies that aren't actually needed any more -- these are commonly packages
like `autoconf`, `cmake` and `m4`. You can run the garbage collection command to
uninstall any build dependencies and no longer required implicit dependencies:

    auser@ln01:~> spack gc

If you commonly use a set of Spack packages together you may want to consider
using a Spack environment. Please see the Spack documentation for more
information.

## Contributing

If you develop a package for use on ARCHER2 please feel free to open a pull
request to the [GitHub repository](https://github.com/EPCCed/spack-archer2).

## More information