# ParaView

[ParaView](https://www.paraview.org) ParaView is a data visualisation and analysis package.
Whilst ARCHER2 compute or login nodes do not have graphics cards installed 
in them paraview is installed so the visualisation libraries and applications 
can be used to post-process simulation data. The ParaView server (``pvserver``),
batch application (``pvbatch``) and the Python interface (``pvpython``)
are all available. Users are able to run the server on the compute nodes
and connect to a local ParaView client running on their own computer.

## Useful links

  - [Paraview webpage](https://www.paraview.org)
  - [ParaView quick-start guide](https://kitware.github.io/paraview-docs/latest/python/quick-start.html)


## Using ParaView on ARCHER2

ParaView is available through the `paraview` module.

```
module load paraview
```

Once the module has been added the ParaView executables, tools, 
and libraries will be able available.

### Connecting to pvserver on ARCHER2

For doing visualisation you should connect to pvserver from a local
paraview client running on your own computer.

!!! note 
    You should make sure the version of ParaView you have installed locally is the same as 
    the one on ARCHER2 (version 5.9.1).

The following instructions are for running pvserver in an interactive job. 
Start an iteractive job using:

```
srun --nodes=1 --exclusive --time=00:20:00 \
               --partition=standard --qos=short --reservation=shortqos \
               --pty /bin/bash
```

Once the job starts the commannd prompt will change to show you are now
on the compute node e.g.

```
auser@nid001023:/work/t01/t01/auser> 
```

Then load the ParaView module and start pvserver with the srun command,

```
auser@nid001023:/work/t01/t01/auser> module load paraview
auser@nid001023:/work/t01/t01/auser> srun -n 4 pvserver --mpi --force-offscreen-rendering
Waiting for client...
Connection URL: cs://nid001023:11111
Accepting connection(s): nid001023:11111
 
```

In a separate terminal you can now set up an ssh tunnel with the node
ID and port number which the pvserver is using, e.g.

```
ssh -L 11111:nid001023:11111 auser@login.archer2.ac.uk 
```

enter your password and phasephrase as usual.


You can then connect from your local client using the following connection
settings:

```
Name:           archer2 
Server Type:    Client/Server 
Host:           localhost 
Port:           11111
```

!!! note 
    The Host from the local client should be set to "localhost" when using the
    ssh tunnel. The "Name" field can be set to a name of your choosing. 
    11111 is the default port for pvserver.

If it has connected correctly you should see the following:

```
Waiting for client...
Connection URL: cs://nid001023:11111
Accepting connection(s): nid001023:11111
Client connected.
```

### Using batch-mode (pvbatch)

A pvbatch script can be run in a standard job script. For example
the following will run on a single node:


```
#!/bin/bash

# Slurm job options (job-name, compute nodes, job time)
#SBATCH --job-name=example_paraview_job
#SBATCH --time=0:20:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1

# Replace [budget code] below with your budget code (e.g. t01)
#SBATCH --account=[budget code]             
#SBATCH --partition=standard
#SBATCH --qos=standard

module load epcc-job-env
module load paraview

srun --mpi=pmi2 pvbatch pvbatchscript.py
```

## Compiling ParaView

The latest instructions for building ParaView on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for Paraview on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/ParaView)
