## Arm Forge

[Arm Forge](https://developer.arm.com/Tools%20and%20Software/Arm%20Forge)
provides debugging and profiling tools for MPI parallel applications, and
OpenMP or pthreads mutli-threaded applications (and also hydrid MPI/OpenMP).
The debugger and profiler are called DDT and MAP, respectively.

ARCHER2 has a license for up to 64 nodes (8192 cores) shared between
all users at any one time. (Note cores are counted by the license, not
MPI processes, threads, or any other software entity.)

There are two ways of running the Arm user interface. If you have a good
internet connection to ARCHER2, the GUI can be run on the front-end (with
an X-connection).
Alternatively, one can download a copy of the Forge remote client to your
laptop or desktop, and run it locally. The remote client should be used if
at all possible.

To download the remote client, see the
[Arm developer download pages](https://developer.arm.com/downloads/-/arm-forge). Version 21.0.3 is known to work at the time of writing. Connecting with
the remote client is discussed below.


!!! note
    Arm Forge is a commercial package for which CSE has a licence until
    April 2023. Availability of Forge beyond April 2023 will depend on
    continued funding for the license. Therefore, please let us know if
    Arm Forge is useful in your work.


### One time set-up for using Forge

A preliminary step is required to set up the necessary
Forge configuration files that allow DDT and MAP to initialise its
environment correctly so that it can, for example, interact with
the SLURM queue system. These steps should be performed in the `/work`
file system on ARCHER2.

It is recommended that these commands are performed in the top-level work
file system directory for the user account: `/work/project/project/${USER}`
(where `project` is the relevant project). This directory will
be referred to throughout as `${WORK}`.
```bash
module load arm/forge
cd ${WORK}
source ${FORGE_ROOT}/config-init
```
This will create a directory `${WORK}/.allinea` with the following files:
```bash
ls .allinea
```
```output
system.config  user.config
```
The directory will also store other relevant files when Forge is run.

### Using DDT

DDT ("distributed debugging tool") provides an easy-to-use graphical
interface for source-level debugging of compiled C/C++ or Fortran codes.
It can also be used for non-interactive debugging, and there
is also some limited support for python debugging.

#### Preparation

To prepare your program for debugging, compile and link in the normal way
but remember to include the `-g` compiler option to retain symbolic
information in the executable. For some programs, it may be necessary
to reduce the optimisation to `-O0` to obtain full and consistent
information. (However, this in itself can change
the behaviour of bugs, so some experimentation may be necessary.)

#### Post-mortem debugging

A non-interactive method of debugging is available which allows information
to be obtained on the state of the execution at the point of failure in a
batch job.

Such a job can be submitted to the batch system in the usual way. The
relevant command to start the executable is, e.g.,:
```slurm
# ... SLURM batch commands as usual ...

module load arm/forge

export OMP_NUM_THREADS=16
export OMP_PLACES=cores

ddt --verbose --offline --mpi=slurm --np 8 \
    --mem-debug=fast --check-bounds=before \
    ./my_executable
```
The parallel launch is delegated to `ddt` and the `--mpi=slurm` option
indicates to `ddt` that the relevant queue system is SLURM
(there is no explicit `srun`). It will also be
necessary to state explicitly to `ddt` the number of processes
required (here `--np 8`). For other options see, e.g., `ddt --help`.

Note that higher levels of memory debugging can result in extremely
slow execution. The example given above uses the default
`--mem-debug=fast` which should be a reasonable first choice.

Execution will produce a `.html` format report which can be used
to examine what the state of the execution was at the point of
failure.


#### Interactive debugging: using the client to submit a batch job

Start the client interactively (for details of remote launch, see below),
e.g.,
```bash
module load arm/forge
ddt
```

This should start a window as shown below. Click on the ddt panel on
the left, and then on the "Run and debug a program" option. This
will bring up the "Run" dialogue as shown.

Note:

* One can start either DDT or MAP by clicking the appropriate panel on
the left-hand side;

* If the license has connected successfully, a serial number will be
shown in small text at the lower left.


![Arm Forge window](./forge-ddt.png)

In the "Application" sub panel of the "Run" dialogue, details of the
executable, command line arguments or data files, the working directory
and so on should be arranged.

Click the "MPI" checkbox and specifiy the MPI implemenation which should
be "SLURM (generic)". This is done by clicking the "Details" button and
then the "Change.." button. Choose the "SLURM (generic)" implementation
from the MPI Implementation pull-down menu section and click "OK". You
can then specify the required number of nodes/processes and so on.

Click the OpenMP checkbox and select the relevant number of threads
(if there is no OpenMP in the application itself, select 1 thread).

Click the "Submit to Queue" checkbox and then the associated "Configure"
button. A new dialogue of options will pop-up. In the top dialgoue box
"Submission template file" enter `${FORGE_ROOT}/templates/archer2.qtf`
and click ok.

The file specified provides a template with many of the options required
for a standard batch job. You will then need to click on the
"Queue Parameters" button in the same section and specify
the relevant project budget to use the queue system in the "Account"
entry.

The default queue template file configuration uses the short QoS with the
standard time limit of 20 minutes. If something different is required,
one can edit the settings. Alternatively, one can make a copy of the
`archer2.qtf` file (e.g., in `${WORK}/.allinea`) and make the relevant
changes. This new file can then be specifed in the dialogue.

There may be a short delay while the sbatch job starts. Debugging should
then proceed as described in the Allinea documentation.


### Using MAP

Load the `arm/forge` module:

```bash
module load arm/forge
```

#### Compilation and linking

Compilation should take place as usual. However, an additional set of
libraries is required at link time.

The path to the additional libraries required will depend on the programming
environment you are using. Here are the paths for each of the compiler
environments:

- `PrgEnv-cray`: `${FORGE_ROOT}/map/lib/crayclang/10.0`
- `PrgEnv-gnu`: `${FORGE_ROOT}/map/lib/gnu/8.0`
- `PrgEnv-aocc`: `${FORGE_ROOT}/map/lib/aocc/3.0`

For example, for `PrgEnv-gnu` the additional options required at link time
are
```
-L{FORGE_ROOT}/map/lib/gnu/8.0 \
-lmap-sampler-pmpi -lmap-sampler \
-Wl,--eh-frame-hdr -Wl,-rpath=${FORGE_ROOT}/map/lib/gnu/8.0
```

#### Generating a profile

Submit a batch job in the usual way, and include the lines:
```slurm
# ... SLURM batch commands as usual ...

module load arm/forge

map -n <number of MPI processes> --mpiargs="--hint=nomultithread --distribution=block:block" --profile ./my_executable
```
Successful execution will generate a file with a ```.map``` extension.

This `.map` file may be viewed via the GUI (start with either `map` or
`forge`) by selecting the
"Load a profile data file from a previous run" option. The resulting
file selection dialogue can then be used to specify the `.map` file.


### Connecting with the remote client

If one starts the Forge client on e.g., a laptop, one should see the main window as
shown above. Select "Remote Launch" and then "Configure" from the
pull-down menu. In the "Configure Remote Connections" dialgoue
click "Add". The following window should be displayed. Fill
in the fields as shown. The "Connection Name" is just a tag
for convenience (useful if a number of different accounts are
in use). The "Host Name" should be as shown with the appropriate
`userid`. The "Remote Installation Directory" should be exactly as
shown. The "Remote Script" is optional, but can be useful if you
need to execute additional environment commands when connecting.
Such a script could be placed in e.g., `${WORK}/.allinea`.

Other settings can be as shown. Remember to click "OK when done.

![Remote Launch Settings](./forge-remote-launch.png)


From the "Remote Launch" menu you should now see the new Connection
Name. Select this, and enter the relevant ssh passphase and machine
password to connect. A remote connection will allow you to debug,
or view a profile, as discussed above.



## Useful links


  - [Arm Forge User Guide](https://developer.arm.com/documentation/101136/latest/)
  - More information on [X-window connections to ARCHER2](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in).
