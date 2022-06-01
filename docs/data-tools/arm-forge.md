## Arm Forge


[Arm Forge](https://developer.arm.com/Tools%20and%20Software/Arm%20Forge)
is a debugging and profiling tool for scalar, multi-threaded and large-scale
parallel applications. The debugger and profiler are called DDT and MAP
respectively.

!!! note
    Arm Forge is a commercial package for which CSE has secured funding for a
    licence, through until April 2023. CSE will monitor the use of Arm Forge and,
    if the software proves to be sufficiently useful, will aim to secure further
    funding – though availability of Forge beyond April 2023 is not guaranteed.

In order to use any of the functionalities outlined in this documentation,
you will first need to load the Arm Forge module, using the following command:

```
module load arm/forge
```

## Useful links

  - [Arm Forge User Guide](https://developer.arm.com/documentation/101136/latest/)

## Running Forge

Arm Forge is primarily controlled using a GUI, which requires allowing it to
control your local display remotely. In order to allow this, use the ```-X```
flag when connecting to ARCHER2. More information on this can be found
[here](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in).

You will also need to setup access to the Arm Licence Server Status page. The
following command sets up port 4241 to do so.

```
ssh archer2 -L 4241:dvn04:4241
```

The licence limits usage to 64 nodes (8192 cores).

One the module has been loaded, Forge can be launched using the following
command:
```
forge my_program
```

## MAP

### Generating a MAP file

First, run the following command setup your local config:
```
work/y07/shared/utils/core/arm/forge/21.0.2/config-init
```
Link your code against the MAP libraries in
```/work/y07/shared/utils/core/arm/forge/21.0.2/map/libs"```

Add ```source "/work/y07/shared/utils/core/arm/forge/21.0.2/remote-init"```
to your submission script and prefix your srun command with
```"map --profile"```

The run should generate a ```.map``` file.
