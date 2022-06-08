# ONETEP

ONETEP (Order-N Electronic Total Energy Package) is a linear-scaling
code for quantum-mechanical calculations based on density-functional
theory.

## Useful Links

  - [ONETEP home page](https://www.onetep.org)
  - [ONETEP tutorials](https://www.onetep.org/Main/Tutorials)
  - [ONETEP documentation](https://www.onetep.org/Main/Documentation)

## Using ONETEP on ARCHER2

**ONETEP is only available to users who have a valid ONETEP licence.**

If you have a ONETEP licence and wish to have access to ONETEP on
ARCHER2, please make a request via the SAFE, see:

   - [How to request access to package
     groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

## Running parallel ONETEP jobs

The following script, supplied by the ONETEP developers, will run a ONETEP job using 2 nodes (256 cores) with
16 MPI processes per node and 8 OpenMP threads per MPI process. It
assumes that there is a single calculation options file with the `.dat`
extension in the working directory.

```slurm
#!/bin/bash

# --------------------------------------------------------------------------
# A SLURM submission script for ONETEP on ARCHER2 (full 23-cabinet system).
# Central install, Cray compiler version.
# Supports hybrid (MPI/OMP) parallelism.
#
# 2022.06 Jacek Dziedzic, J.Dziedzic@soton.ac.uk
#                         University of Southampton
#         Lennart Gundelach, L.Gundelach@soton.ac.uk
#                            University of Southampton
#         Tom Demeyere, T.Demeyere@soton.ac.uk
#                       University of Southampton
# --------------------------------------------------------------------------

# v1.00 (2022.06.04) jd: Adapted from the user-compiled Cray compiler version.

# ==========================================================================================================
# Edit the following lines to your liking.
#
#SBATCH --job-name=mine               # Name of the job.
#SBATCH --nodes=2                     # Number of nodes in job.
#SBATCH --ntasks-per-node=16          # Number of MPI processes per node.
#SBATCH --cpus-per-task=8             # Number of OMP threads spawned from each MPI process.
#SBATCH --time=5:00:00                # Max time for your job (hh:mm:ss).
#SBATCH --partition=standard          # Partition: standard memory CPU nodes with AMD EPYC 7742 64-core processor
#SBATCH --account=t01                 # Replace 't01' with your budget code.
#SBATCH --qos=standard                # Requested Quality of Service (QoS), See ARCHER2 documentation

export OMP_NUM_THREADS=8              # Repeat the value from 'cpus-per-task' here.

# Set up the job environment, loading the ONETEP module.
# The module automatically sets OMP_PLACES, OMP_PROC_BIND and FI_MR_CACHE_MAX_COUNT.
# To use a different binary, replace this line with either (drop the leading '#')
# module load onetep/6.1.9.0-GCC-LibSci
# to use the GCC-libsci binary, or with
# module load onetep/6.1.9.0-GCC-MKL
# to use the GCC-MKL binary.

module load onetep/6.1.9.0-CCE-LibSci

# ==========================================================================================================
# !!! You should not need to modify anything below this line.
# ==========================================================================================================

workdir=`pwd`
echo "--- This is the submission script, the time is `date`."

# Figure out ONETEP executable
onetep_exe=`which onetep.archer2`
echo "--- ONETEP executable is $onetep_exe."

onetep_launcher=`echo $onetep_exe | sed -r "s/onetep.archer2/onetep_launcher/"`

echo "--- workdir is '$workdir'."
echo "--- onetep_launcher is '$onetep_launcher'."

# Ensure exactly 1 .dat file in there.
ndats=`ls -l *dat | wc -l`

if [ "$ndats" == "0" ]; then
  echo "!!! There is no .dat file in the current directory. Aborting." >&2
  touch "%NO_DAT_FILE"
  exit 2
fi

if [ "$ndats" == "1" ]; then
  true
else
  echo "!!! More than one .dat file in the current directory, that's too many. Aborting." >&2
  touch "%MORE_THAN_ONE_DAT_FILE"
  exit 3
fi

rootname=`echo *.dat | sed -r "s/\.dat\$//"`
rootname_dat=$rootname".dat"
rootname_out=$rootname".out"
rootname_err=$rootname".err"

echo "--- The input file is $rootname_dat, the output goes to $rootname_out and errors go to $rootname_err."

# Ensure ONETEP executable is there and is indeed executable.
if [ ! -x "$onetep_exe" ]; then
  echo "!!! $onetep_exe does not exist or is not executable. Aborting!" >&2
  touch "%ONETEP_EXE_MISSING"
  exit 4
fi

# Ensure onetep_launcher is there and is indeed executable.
if [ ! -x "$onetep_launcher" ]; then
  echo "!!! $onetep_launcher does not exist or is not executable. Aborting!" >&2
  touch "%ONETEP_LAUNCHER_MISSING"
  exit 5
fi

# Dump the module list to a file.
module list >\$modules_loaded 2>&1

ldd $onetep_exe >\$ldd

# Report details
echo "--- Number of nodes as reported by SLURM: $SLURM_JOB_NUM_NODES."
echo "--- Number of tasks as reported by SLURM: $SLURM_NTASKS."
echo "--- Using this srun executable: "`which srun`
echo "--- Executing ONETEP via $onetep_launcher."


# Actually run ONETEP
# Additional srun options to pin one thread per physical core
########################################################################################################################################################
srun --hint=nomultithread --distribution=block:block -N $SLURM_JOB_NUM_NODES -n $SLURM_NTASKS $onetep_launcher -e $onetep_exe -t $OMP_NUM_THREADS $rootname_dat >$rootname_out 2>$rootname_err
########################################################################################################################################################

echo "--- srun finished at `date`."

# Check for error conditions
result=$?
if [ $result -ne 0 ]; then
  echo "!!! srun reported a non-zero exit code $result. Aborting!" >&2
  touch "%SRUN_ERROR"
  exit 6
fi

if [ -r $rootname.error_message ]; then
  echo "!!! ONETEP left an error message file. Aborting!" >&2
  touch "%ONETEP_ERROR_DETECTED"
  exit 7
fi

tail $rootname.out | grep completed >/dev/null 2>/dev/null
result=$?
if [ $result -ne 0 ]; then
  echo "!!! ONETEP calculation likely did not complete. Aborting!" >&2
  touch "%ONETEP_DID_NOT_COMPLETE"
  exit 8
fi

echo "--- Looks like everything went fine. Praise be."
touch "%DONE"

echo "--- Finished successfully at `date`."
```

## Hints and Tips

See the information in the [ONETEP documentation](https://www.onetep.org/Main/RunningONETEP).

## Compiling ONETEP

The latest instructions for building ONETEP on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for ONETEP on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/ONETEP)
