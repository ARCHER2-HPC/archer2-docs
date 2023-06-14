# Matio

Matio is a library which allows reading and writing matrices in
MATLAB MAT format. It is an open source development released under
a BSD license.


## Compiling and linking against Matio

- `module load matio`

Load the `matio` module and use the standard compiler wrappers
`cc`, `CC`, or `ftn` in the usual way. The appropriate header
files and libraries will be included automatically via the
compiler wrappers.

The `matio` module set the `PATH` variable so that the stand-alone
utility `matdump` can be used. The module also defines `MATIO_PATH`
which gives the root of the installation if this is needed.


### Version history

=== "Upgrade 2023"

    - Module `matio/1.5.23` installed May 2023 (PE 22.12)
    - Module `matio/1.5.18` is removed.

=== "Full system"

    - Module `matio/1.5.18` installed October 2021 (PE 21.04)

=== "4-cabinet system"

    - Module `matio/1.5.18` installed January 2021


## Compiling your own version

A version of Matio as currently installed on Archer2 can be
compiled using the script avaailable from the Archer2
[github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop):

```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2022-12
$ ./sh/tpsl/matio.sh --prefix=/path/to/install/location
```
where `--prefix` defines the location of the installation.


## Resources

Matio [github repository](https://github.com/tbeu/matio)
