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

!!! important
    As our Spack installation is still in an experimental stage please be aware
    that we cannot guarantee that it will work with full functionality and may
    not be able to provide support.

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
dependencies of a package before you install it, you can use `spack spec` to see
the full concretised set of packages:

    auser@ln01:~> spack spec gromacs@2024.2%gcc+mpi

!!! tip
    Spack needs to bootstrap the installation of some extra software in order to
    function, principally `clingo`  which is used to solve the dependencies
    required for an installation. The first time you ask Spack to concretise a
    spec into a precise set of requirements, it will take extra time as it
    downloads this software and extracts it into a local directory for Spack's
    use.

!!! tip
    Spack on ARCHER2 has been configured to use as much of the HPE Cray
    Programming Environment as possible. For example, this means that Cray
    LibSci will be used to provide the BLAS, LAPACK and ScaLAPACK dependencies
    and Cray MPICH will provide MPI. It is also configured to allow it to re-use
    as dependencies any packages that the ARCHER2 CSE team has `spack install`ed
    centrally, potentially helping to save you build time and storage quota.

### Using Spack packages

Spack provides a module-like way of making software that you have installed
available to use. If you have a GROMACS installation, you can make it
available to use with `spack load`:

    auser@ln01:~> spack load gromacs

At this point you should be able to use the software as normal. You can then
remove it once again from the environment with `spack unload`:

    auser@ln01:~> spack unload gromacs

If you have multiple variants of the same package installed, you can use the
spec to distinguish between them. You can always check what packages have been
installed using the `spack find` command. If no other arguments are given it
will simply list all installed packages, or you can give a package name to
narrow it down:

    auser@ln01:~> spack find gromacs

You can see your packages' install locations using `spack find --paths` or
`spack find -p`.

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

and of course, to be absolutely certain that you are uninstalling the correct
package, you can provide the hash:

    auser@ln01:~> spack uninstall /wjy5dus

Uninstalling a package will leave behind any implicitly installed packages that
were installed to support it. Spack may have also installed build-time
dependencies that aren't actually needed any more -- these are often packages
like `autoconf`, `cmake` and `m4`. You can run the garbage collection command to
uninstall any build dependencies and implicit dependencies that are no longer
required:

    auser@ln01:~> spack gc

If you commonly use a set of Spack packages together you may want to consider
using a Spack environment to assist you in their installation and management.
Please see the [Spack
documentation](https://spack.readthedocs.io/en/latest/environments.html) for
more information.

## Custom configuration

Spack is configured using YAML files. The central installation on ARCHER2 made
available to users is configured to use the HPE Cray Programming Environment and
to allow you to start installing software to your `/work` directories right
away, but if you wish to make any changes you can provide your own overriding
userspace configuration.

Your own configuration should fit in the user level scope. On ARCHER2 Spack is
configured to, by default, place and look for your configuration files in your
work directory at e.g. `/work/t01/t01/auser/.spack`. You can however override
this to have Spack use any directory you choose by setting the
`SPACK_USER_CONFIG_PATH` environment variable, for example:

    auser@ln01:~> export SPACK_USER_CONFIG_PATH=/work/t01/t01/auser/spack-config

Of course this will need to be a directory where you have write permissions,
such in your home or work directories, or in one of your project's `shared`
directories.

You can edit the configuration files directly in a text editor or by
running, for example:

    auser@ln01:~> spack config edit repos

which would open your `repos.yaml` in `vim`. You can change which editor is used
by setting the `SPACK_EDITOR` environment variable.

The final configuration used by Spack is a compound of several scopes, from the
Spack defaults which are overridden by the ARCHER2 system configuration files,
which can then be overridden in turn by your own configurations. You can see
what options are in use at any point by running, for example:

    auser@ln01:~> spack config get config

which goes through any and all `config.yaml` files known to Spack and sets
the options according to those files' level of precedence. You can also get more
information on which files are responsible for which lines in the final active
configuration by running, for example to check `packages.yaml`:

    auser@ln01:~> spack config blame packages

Unless you have already written a `packages.yaml` of your own, this will show a
mix of options originating from the Spack defaults and also from an
`archer2-user` directory which is where we have told Spack how to use packages
from the HPE Cray Programming Environment.

If there is some behaviour in Spack that you want to change, looking at the
output of `spack config get` and `spack config blame` may help to show what you
would need to do. You can then write your own user scope configuration file to
set the behaviour you want, which will override the option as set by the
lower-level scopes.

Please see the [Spack
documentation](https://spack.readthedocs.io/en/latest/configuration.html) to
find out more about writing configuration files.

## Writing new packages

A Spack package is at its core a Python `package.py` file which provides
instructions to Spack on how to obtain source code and compile it. A very simple
package will allow it to build just one version with one compiler and one set of
options. A more fully-featured package will list more versions and include logic
to build them with different compilers and different options, and to also pick
its dependencies correctly according to what is chosen.

Spack provides several thousand packages in its `builtin` repository. You may be
able to use these with no issues on ARCHER2 by simply running `spack install` as
described above, but if you do run into problems in the interaction between
Spack and the CPE compilers and libraries then you may wish to write your own.
Where the ARCHER2 CSE service has encountered problems with packages we have
attempted to provide our own in a repository located at
`$SPACK_ROOT/var/spack/repos/archer2`.

### Creating your own package repository

A package repository is a directory containing a `repo.yaml` configuration file
and another directory called `packages`. Directories within the latter are named
for the package they provide, for example `cp2k`, and contain in turn a
`package.py`. You can create a repository from scratch with the command

    auser@ln01:~> spack repo create reponame

where `reponame` is the name of the repository. This command will create the
`reponame` directory in your current working directory, but you can provide a
path to its location elsewhere if necessary. You can then make it available to
Spack to use by running:

    auser@ln01:~> spack repo add reponame

This adds the path to `reponame` to the `repos.yaml` file in your user scope
config directory [as described above](#custom-configuration). If this file
doesn't yet exist, it will be created.

### Creating a package



## Contributing

If you develop a package for use on ARCHER2 please feel free to open a pull
request to the [GitHub repository](https://github.com/EPCCed/spack-archer2).
