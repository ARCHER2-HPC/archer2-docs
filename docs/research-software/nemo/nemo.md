# NEMO

!!! warning
    The ARCHER2 Service is not yet available. This documentation is in
    development.

NEMO (Nucleus for European Modelling of the Ocean) is a state-of-the-art
framework for research activities and forecasting services in ocean and
climate sciences, developed in a sustainable way by a European
consortium.

## Useful Links

  - The NEMO home page <https://www.nemo-ocean.eu>
  - NEMO documentation
    <https://forge.ipsl.jussieu.fr/nemo/chrome/site/doc/NEMO/guide/html/NEMO_guide.html>
  - NEMO users' area <http://forge.ipsl.jussieu.fr/nemo/wiki/Users> which includes information on
    obtaining and downloading the latest source code releases. 

NEMO is released under a CeCILL license and if freely available to all
users on ARCHER2.

## Using NEMO on ARCHER2

A central install of NEMO is not appropriate for most users of ARCHER2 since
many configurations will want to add bespoke code changes. There is, however, a
case for providing some central material specific to ARCHER2. This includes:
arch files for compiling on ARCHER2; a pre-compiled version of XIOS and guides
for running NEMO on ARCHER2. The guides provided here address the running of
NEMO as a stand-alone ocean model (albeit with optional sea-ice and external
XIOS i/o servers ) and provide links to some of the material held in the shared
workspace of the n01 (oceans and shelf seas) consortium. The running of full
Earth System Models with NEMO as just one component of a fully interacting,
OASIS-coupled, suite is best dealt with by more comprehensive workflow
management systems such as the Rose and Cylc set-up used by NCAS.

## Setting up the correct environment 

The first point of note is that NEMO will not operate successfully in the
default environment on ARCHER2. To be precise, this statement is true for any
attempts to run NEMO as part of a MPMD task with external XIOS servers. In
attached mode, where no external servers are used and every ocean process acts
as an io server, then the default environment can be used to launch NEMO as a
SPMD task. Since attached mode is not performant at high core counts it is
advisable to standardise on on environment which is suitable for all NEMO
applications. This is currently:

 - Either (starting from the default environment):

```
module unload cray-mpich
module load craype-network-ucx
module load cray-mpich-ucx
module load libfabric
module load cray-hdf5-parallel
module load cray-netcdf-hdf5parallel
module load gcc
```
- Or, equivalently:
```
module -s restore /work/n01/shared/acc/n01_modules/ucx_env
```

If your NEMO tasks are failing at start-up (or possibly hanging) then the
chances are you are attempting to use the wrong mpich library. Note
all investigations to date have focussed on using the Cray compilers.
 
## Enabling FCM to compile in parallel with Cray compilers

FCM is bundled with both NEMO and XIOS and is used by makenemo and make_xios
scripts These scripts accept –j N/-jobs N arguments for parallel builds but the
Cray compilers trip up attempting to load modules which have only just been
built. It seems to be a timing issue because if the -J option is added to inform
the compiler where to look for modules the problem disappears. The -J option
should not be necessary because the -I setting is already given and, according to
the manual, directories given by -J are searched first followed by those given by
-I. The slight difference in priority seems to matter though and parallel builds
will fail with the Cray compilers unless the following change is made to:

```
NEMO/r4.0.X/ext/FCM/lib/Fcm/Config.pm 	(NEMO4 source tree) 
```

and (if compiling xios, not everyone needs to do this, see next section)

```
xios-2.5/tools/FCM/lib/Fcm/Config.pm   (may need to run make_xios once to unpack this)
```

In both cases change: 

```
FC_MODSEARCH => '',             # FC flag, specify "module" path
to
FC_MODSEARCH => '-J',           # FC flag, specify "module" path
```

## Compiling XIOS and NEMO

It is not necessary for everyone to compile XIOS. A compiled version is available in:
```
/work/n01/shared/acc/xios-2.5
```
This was compiled with:
```
./make_xios --prod --arch X86_ARCHER2-Cray --netcdf_lib netcdf4_par --job 16 -full
```
using the:
```
/work/n01/shared/acc/xios-2.5/arch/arch-X86_ARCHER2-Cray.[env,fcm,path] settings
```

The NEMO arch file suggested at the next step will link nemo with the xios libraries
contained therein and copies of the xios_server.exe executable can be taken from
the xios-2.5/bin directory. It is recommended to take copies of this executable
to guard against any possible issues with future updates.

NEMO can be compiled with (for example):

```
./makenemo -n ORCA2_ICE_PISCES_ST -r ORCA2_ICE_PISCES  -m X86_ARCHER2-Cray -j 16
```
once the:
```
/work/n01/shared/acc/arch-X86_ARCHER2-Cray.fcm
```
file has been dropped into the NEMO arch directory. Note this arch file 
currently sets compiler flags of:
```
-em -s integer32 -s real64 -O1 -hflex_mp=intolerant
```

Small gains at higher optimisation levels are offset by much longer compile
times and an inability to attain restartability and reproducibility passes in
some of the standard SETTE tests. Future releases of NEMO (post r4.0.4) will
contain this arch file for ARCHER2. Users of versions 4.0.4 and earlier will
have to manually add this file.

## Building a run script

Most NEMO applications will want to run in detached mode with separate XIOS
servers.  Remember this is only currently possible using the cray-mpich-ucx
module.  MPMD jobs are more complex to set-up than on ARCHER;  but also more
versatile since executables can be mixed on a node.  Generally, xios_servers
will need to be more lightly packed than NEMO cores because:

 - xios servers have less consistent memory requirements than the ocean cores
 - They generally require more memory and their needs can spike depending on 
   output interval or experimental needs
 - There are far fewer of them than ocean cores so a pragmatic solution might be to
   assign each xios_server an entire NUMA region of its own
 - It also makes sense to avoid concentrating too many xios_servers on any particular node
 - Very large models may require xios_servers to occupy more than one NUMA (TBD)

All this can be achieved using the:
```
 –cpu-bind=map_cpu:<cpu map> 
```
option to srun but it is tedious to construct cpu maps by hand. 
The:
```
/work/n01/shared/acc/mkslurm 
```
script will construct a basic run script using supplied packing arguments.

## The mkslurm script

```
usage: mkslurm [-S num_servers] [-s server_spacing] [-m max_servers_per_node] 
               [-C num_clients] [-c client_spacing] 
               [-t time_limit] [-a account] [-j job_name]
```
It is recommended to take your own copy and set defaults for most of these arguments

For example, to run with 4 xios servers (a maximum of 2 per node), each with
sole occupancy of a 16-core NUMA region and 96 ocean cores, spaced with an idle
core in between each, use:
```
./mkslurm -S 4 -s 16 -m 2 -C 96 -c 2 > myscript.slurm
``` 
This will report (to stderr) that 2 nodes are needed with 100 active cores
spread over 256 cores. It will also echo the equivalent full command (to stderr)
to show the defaults used for those arguments not given, i.e.:
```
Running: mkslurm -S 4 -s 16 -m  4 -C  96 -c  2 -t 00:10:00 -a n01 -j nemo_test
nodes needed= 2 (256)
cores to be used= 100 (256)
```
The slurm script produced is shown in the next section

## Running parallel NEMO jobs

The script produced is:
```
#!/bin/bash
#SBATCH --job-name=nemo_test
#SBATCH --time=00:10:00
#SBATCH --nodes=2
#SBATCH --ntasks=100
#SBATCH --account=n01
#SBATCH --partition=standard
#SBATCH --qos=standard
export OMP_NUM_THREADS=1
module restore /work/n01/shared/acc/n01_modules/ucx_env
#
cat > myscript_wrapper2.sh << EOFB
#!/bin/ksh
#
set -A map ./xios_server.exe ./nemo
exec_map=( 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 )
#
exec \${map[\${exec_map[\$SLURM_PROCID]}]}
##
EOFB
chmod u+x ./myscript_wrapper2.sh
#
srun --mem-bind=local --cpu-bind=v,map_cpu:00,0x10,0x20,0x22,0x24,0x26,0x28,0x2a,0x2c,0x2e,0x30,0x32,0x34,0x36,0x38,0x3a,0x3c,0x3e,0x40,0x42,0x44,0x46,0x48,0x4a,0x4c,0x4e,0x50,0x52,0x54,0x56,0x58,0x5a,0x5c,0x5e,0x60,0x62,0x64,0x66,0x68,0x6a,0x6c,0x6e,0x70,0x72,0x74,0x76,0x78,0x7a,0x7c,0x7e,00,0x10,0x20,0x22,0x24,0x26,0x28,0x2a,0x2c,0x2e,0x30,0x32,0x34,0x36,0x38,0x3a,0x3c,0x3e,0x40,0x42,0x44,0x46,0x48,0x4a,0x4c,0x4e,0x50,0x52,0x54,0x56,0x58,0x5a,0x5c,0x5e,0x60,0x62,0x64,0x66,0x68,0x6a,0x6c,0x6e,0x70,0x72,0x74,0x76,0x78,0x7a,0x7c,0x7e, ./myscript_wrapper2.sh
```

which will run the desired MPMD job providing the xios_server.exe and nemo
executables are in the directory that this script is submitted from. The
exec_map array shows the position of each executable in the rank list
(0=xios_server.exe, 1=nemo). The cpu map gives the hexadecimal number of the
core that will run that executable. For larger core counts the cpu_map can be
limited to a single node map which will be cycled through as many times as
necessary until all the tasks are mapped.

## Some preliminary tests

This table shows the results of a repeated 60 day simulation of the
ORCA2_ICE_PISCES, SETTE configuration using various core counts and packing
strategies:

![Screenshot](./orca2_tests.png)

!!! note
    This information is based on experience during early user testing and
    is subject to change
