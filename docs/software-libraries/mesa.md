# Mesa

Mesa is an open-source implementation of OpenGL, Vulkan, and other graphics API to vendor-specific hardware drivers.


## Compiling with Mesa

- `module load mesa`

To compile an application with the mesa header files, load the
`mesa` module and use the compiler wrappers in the usual way.
The relevant header files will be introduced automatically.

The header files are located in `/work/y07/shared/libs/core/mesa/21.0.1/`,
and can be included manually at compilation without loading the module
if required.


### Version history

- Module `mesa/21.0.1` installed June 2023


## Compiling your own version

Build recipe for this module can be found at the [HPC-UK github repo](https://github.com/hpc-uk/build-instructions/blob/main/libs/mesa/ARCHER2_mesa_21.0.1.md)

## Resources

[Mesa home page](https://mesa3d.org/)
