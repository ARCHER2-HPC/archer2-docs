# ARCHER2 Test and Development System (TDS) user notes

The ARCHER2 Test and Development System (TDS) is a small system used for testing
changes before they are rolled out onto the full ARCHER2 system. This page 
contains useful information for people using the TDS on its configuration and
what they can expect from the system.

!!! important
    The TDS is used for testing on a day to day basis. This means that nodes and
    the entire system may be made unavailable or rebooted with little or no warning.

## TDS system details

 - Login node: 2x 64-core AMD EPYC 7742, 512 GB RAM
 - Compute nodes: 8 compute nodes in total
    + 4 standard memory compute nodes: 2x 64-core AMD EPYC 7742, 256 GB RAM
    + 4 high memory compute nodes: 2x 64-core AMD EPYC 7742, 512 GB RAM

 - Slingshot interconnect

 - Storage:
    + /home file system: shared with ARCHER2 main system
    + /work file system: 224 TiB Lustre (2x MDT, 2x OST) - not shared with main system

## Connecting to the TDS

You can only log into the TDS from an ARCHER2 login node. You should create an 
SSH key pair on an ARCHER2 login node and add the public part to your ARCHER2 account
in SAFE in the usual way.

Once your new key pair is setup, you can then login to the TDS (from an ARCHER2 login
node) with

```
ssh login-tds.archer2.ac.uk
```

You will require your SSH key passphrase (for the new key pair you generated) and your
usual ARCHER2 account password to login to the TDS.

## Slurm scheduler configuration

 - Paritions available:
    + `standard`: includes all compute nodes
    + `highmem`: includes high memory compute nodes
 - QoS available:
    + `standard`: same limits as on ARCHER2 main system
    + `highmem`: same limits as on ARCHER2 main system

## Known issues/notes

 - Software modules
    + HPE Cray Programming Environment 22.12
    + ARCHER2 CSE service modules available but not all software installed on TDS `/work` file system - i.e. you may be able to load a module but the software it points to may not be available. Check if the software is actually installed before trying to use it.

 - GCC 12.2.0 (gcc/g++/gfortran) compiler has been shown to give incorrect numerical results for a number of software packages (VASP, CASTEP. CP2K). If you want to use this compiler version we recommend checking output carefully. We may remove this version from the PE software stack installed on the full system as part of the software upgrade.

 - Singularity + MPI does not currently work - MPI executable in the Singularity container segfaults.

 - Energy use data is not available from TDS compute nodes.

 - Change of behaviour of the `--cpus-per-task` Slurm option. If you set `--cpus-per-task` greater than `1` in your job submission script (e.g. using `#SBATCH` directives) then this option is not inhereted by `srun` commands in the job script. You need to eithe set something like `export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK` or repeat the option explicitly in the `srun` command (e.g. `srun --cpus-per-task=$SLURM_CPUS_PER_TASK --hint=nomultithread --distribution=block:block`).

  - Change in definition of a Slurm NUMA region. On the TDS, a Slurm NUMA region is 4 cores (corresponding to an Core CompleX CCX in the AMD EPYC Zen2 architecture). This means cyclic process placements on NUMA regions (e.g. `--distribution=block:cyclic`) will cycle over 4-core CCX. (On the main system, a Slurm NUMA region is 16 cores).

