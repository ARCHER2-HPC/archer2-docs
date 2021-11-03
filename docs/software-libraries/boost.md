# Boost

Boost provide portable C++ libraries useful in a broad range of
contexts. The libraries are freely available under the terms of
the [Boost Software license][1].

[1]: https://www.boost.org/users/license.html


## Compiling and linking

- `module load boost`

The C++ compiler wrapper `CC` will
introduce the appropriate options to compile an application
against the Boost libraries. The other compiler wrappers
(`cc` and `ftn`) do not introduce these options.

To check exactly what options are introduced type, e.g.,
```
$ CC --cray-print-opts
```

The `boost` module also defines the environment variable `BOOST_DIR`
as the root of the installation for the current programming environment
if this information is needed.

### Version history

=== "Full system"
    
    - Module `boost/1.72` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `boost/1.72.0` installed January 2021


The following libraries are installed: `atomic chrono container context
contract coroutine date_time exception fiber filesystem graph_parallel
graph iostreams locale log math mpi program_options random
regex serialization stacktrace system test thread timer type_erasure
wave`

## Compiling Boost

The ARCHER2 Github repository contains a recipe for compiling Boost for
the different programming environments.
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout cse-develop
$ ./sh/boost.sh --prefix=/path/to/install/location
```
where the `--prefix` determines the install location. The list of
libraries compiled is specified in the `boost.sh` script. See the
[ARCHER2 Github repository][4] for further information.

[4]: https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop

## Resources

- [Boost home page][5].

- [Documentation (HTML) for the current version][6].

- [Boost GitHub repository][7].

[5]: https://www.boost.org
[6]: https://www.boost.org/doc/libs/1_75_0/libs/libraries.htm
[7]: https://github.com/boostorg

