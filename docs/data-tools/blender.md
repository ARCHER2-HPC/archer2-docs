# Blender

[Blender](https://www.blender.org) is a 3D rendering and package tool primarily used for 3D animation and VFX but increasingly also for scientific visualisation. By being an artist tool first as opposed to a scientific visualisation package, it allows for a great versatility and a complete control over every aspect of the final image.


## Useful links

  - [Blender homepage](https://www.blender.org)
  - [Introduction to Scientific Visualization with Blender, course by surf](https://surf-visualization.github.io/blender-course/)
  - [EPCC Webinar on the use of Blender for Scientific Visualisation](https://www.archer2.ac.uk/training/courses/240214-visualisation-vt)


## Using Blender on ARCHER2

Blender is available through the `blender` module.

```
module load blender
```

Once the module has been loaded, the Blender executable will be available. 

### Running blender jobs


Even though blender is single node only, each frame being independent makes the render of animations an embarrassingly parallel problem. Here is an example job for running blender to export frames 1 to 100 from the blend file `scene.blend`. Submitting an other job with a different frame range will use a 2nd node etc.

```bash
#!/bin/bash

# Slurm job options (job-name, compute nodes, job time)
#SBATCH --job-name=example_blender_job
#SBATCH --time=0:20:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1

# Replace [budget code] below with your budget code (e.g. t01)
#SBATCH --account=[budget code]             
#SBATCH --partition=standard
#SBATCH --qos=standard

module load blender

export BLENDER_USER_RESOURCES=${HOME/home/work}/.blender

blender -b scene.blend --render-output //render_ -noaudio -f 1-100 -- --cycles-device CPU

```

The full list of command line arguments can be found in [Blender's documentation](https://docs.blender.org/manual/en/4.2/advanced/command_line/arguments.html). Note that with blender, the order of the arguments is important. 

To automatise this process addons like the one available in [blender4science](https://github.com/EPCCed/blender4science/) are helpful as they allow to submit multiple identical jobs and handles the parallelisation to render each frame only once.

!!! note Blender doesn't work on ARCHER2 GPU nodes at the moment due to incompatibilities with the rocm version available




