# GLM

OpenGL Mathemetics (GLM) is a header-only C++ library which performs
operations typically encountered in graphics applications, but can
also be relevant to scientific applications. GLM is freely avaialble
under an MIT license.


## Compiling with GLM

- `module load glm`

The compiler wrapper `CC` will automatically location the required
include dicrectory when the module is loaded.

The `glm` module also defines the environment variable `GLM_DIR`
which carries the root of the installation, if needed.


### Version history

- Module `glm/0.9.9.6` installed January 2021


## Install your own version

One can follow the instructions used to install the current version
on ARCHER2 via the ARCHER2 github repository:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout cse-develop
$ ./sh/glm.sh --prefix=/path/to/install/location
```
where the `--prefix` option sets the install location. See the ARCHER2
[github repository][1] for further details.

[1]: https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop



## Resources

The GLM [guthub repository](https://github.com/g-truc/glm)
