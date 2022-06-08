# Nektar++

Nektar++ is a tensor product based finite element package designed to
allow one to construct efficient classical low polynomial order
<span class="title-ref">h</span>-type solvers (where
<span class="title-ref">h</span> is the size of the finite element) as
well as higher <span class="title-ref">p</span>-order piecewise
polynomial order solvers.

The Nektar++ framework comes with a number of solvers and also allows
one to construct a variety of new solvers. Users can therefore use
Nektar++ just to run simulations, or to extend and/or develop new
functionality.

## Useful Links

  - [Nektar++ home page](https://www.nektar.info)
  - [Nektar++ tutorials](https://www.nektar.info/community/tutorials/)
  - [Nektar GitLab repository](https://gitlab.nektar.info/nektar)

## Using Nektar++ on ARCHER2

Nektar++ is released under an MIT license and is available to all users
on the ARCHER2 full system.

### Where can I get help?

Specific issues with Nektar++ itself might be submitted to the issue
tracker at the Nektar++ gitlab repository (see link above). More general
questions might also be directed to the [Nektar-users mailing list](https://mailman.ic.ac.uk/mailman/listinfo/nektar-users). Issues
specific to the use or behaviour of Nektar++ on ARCHER2 should be sent to the
[Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## Running parallel Nektar++ jobs

Below is the submission script for running the Taylor-Green Vortex, one of the Nektar++ tutorials,
see [https://doc.nektar.info/tutorials/latest/incns/taylor-green-vortex/incns-taylor-green-vortex.html#incns-taylor-green-vortexch4.html](https://doc.nektar.info/tutorials/latest/incns/taylor-green-vortex/incns-taylor-green-vortex.html#incns-taylor-green-vortexch4.html) .

You first need to download the archive linked on the tutorial page.

```bash
cd /path/to/work/dir
wget https://doc.nektar.info/tutorials/latest/incns/taylor-green-vortex/incns-taylor-green-vortex.tar.gz
tar -xvzf incns-taylor-green-vortex.tar.gz
```

=== "Full system"
    ```bash
    #!/bin/bash
    #SBATCH --job-name=nektar
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=32
    #SBATCH --cpus-per-task=1
    #SBATCH --time=02:00:00
    
    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    module load nektar

    export OMP_NUM_THREADS=1

    NEK_INPUT_PATH=/path/to/work/dir/incns-taylor-green-vortex/completed/solver64

    srun --distribution=block:cyclic --hint=nomultithread \
        ${NEK_DIR}/bin/IncNavierStokesSolver \
            ${NEK_INPUT_PATH}/TGV64_mesh.xml \
            ${NEK_INPUT_PATH}/TGV64_conditions.xml
    ```

## Compiling Nektar++

The latest instructions for building Nektar++ on ARCHER2 may be found in
the GitHub repository of build instructions.

[ARCHER2 Full System](https://github.com/hpc-uk/build-instructions/blob/main/apps/nektarplusplus/build_nektarplusplus_5.0.3_archer2_gcc11_cmpich8.md)