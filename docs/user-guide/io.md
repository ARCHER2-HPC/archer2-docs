# I/O and file systems

## Using the ARCHER2 file systems

Different file systems are configured for different purposes and
performance. ARCHER2 has three file systems available to users:

| Node type | Available file systems |
| --------- | ---------------------- |
| Login     | /home, /work           |
| Compute   | /work                  |

!!! warning
    Any data used in a parallel jobs should be located on `/work` (Lustre).

### Home file systems

Home directories provide a convenient means for a user to have access to
files such as source files, input files or configuration files. This
file system is only mounted on the login nodes. The home directory for
each user is located at:

    /home/[project code]/[group code]/[username]

where

   - `[project code]` is the code for your project (e.g., x01);
   - `[group code]` is the code for your project group, if your project has
     groups, (e.g. x01-a) or the same as the project code, if not;
   - `[username]` is your login name.

Each project is allocated a portion of the total storage available, and
the project PI will be able to sub-divide this quota among the groups
and users within the project. As is standard practice on UNIX and Linux
systems, the environment variable `$HOME` is automatically set to point
to your home directory.

It should be noted that the home file system is not designed, and does
not have the capacity, to act as a long term archive for large sets of
results.

### Work file system

!!! warning
    There is no backup of data on any of the work file systems,
    which means that in the event of a major hardware failure, or if a user
    accidently deletes essential data, it will not be possible to recover
    the lost files.

A high-performance Lustre file system is mounted on the compute nodes. All
parallel calculations must be run from directories on the `/work` file
system and all files required by the calculation (apart from the
executable) must reside on `/work`. Each project will be assigned space
on a particular Lustre partition with the assignments chosen to balance
the load across the available infrastructure.

The work directory for each user is located at:

    /work/[project code]/[group code]/[username]

where

   - `[project code]` is the code for your project (e.g., x01);
   - `[group code]` is the code for your project group, if your project has
     groups, (e.g. x01-a) or the same as the project code, if not;
   - `[username]` is your login name.

Links from the `/home` file system to directories or files on `/work`
are strongly discouraged. If links are used, executables and data files
on `/work` to be used by applications on the compute nodes (i.e. those
executed via the `aprun` command) should be referenced directly on
`/work`.

## Common I/O patterns

There is a number of I/O patterns that are frequently used in
applications:

### Single file, single writer (Serial I/O)

A common approach is to funnel all the I/O through a single master
process. Although this has the advantage of producing a single file, the
fact that only a single client is doing all the I/O means that it gains
little benefit from the parallel file system.

### File-per-process (FPP)

One of the first parallel strategies people use for I/O is for each
parallel process to write to its own file. This is a simple scheme to
implement and understand but has the disadvantage that, at the end of
the calculation, the data is spread across many different files and may
therefore be difficult to use for further analysis without a data
reconstruction stage.

### Single file, multiple writers without collective operations

There are a number of ways to achieve this. For example, many processes
can open the same file but access different parts by skipping some
initial offset; parallel I/O libraries such as MPI-IO, HDF5 and NetCDF
also enable this.

Shared-file I/O has the advantage that all the data is organised
correctly in a single file making analysis or restart more
straightforward.

The problem is that, with many clients all accessing the same file,
there can be a lot of contention for file system resources.

### Single Shared File with collective writes (SSF)

The problem with having many clients performing I/O at the same time is
that, to prevent them clashing with each other, the I/O library may have
to take a conservative approach. For example, a file may be locked while
each client is accessing it which means that I/O is effectively
serialised and performance may be poor.

However, if I/O is done collectively where the library knows that all
clients are doing I/O at the same time, then reads and writes can be
explicitly coordinated to avoid clashes. It is only through collective
I/O that the full bandwidth of the file system can be realised while
accessing a single file.

## Achieving efficient I/O

This section provides information on getting the best performance out of
the parallel `/work` file systems on ARCHER2 when writing data,
particularly using parallel I/O patterns.

### Lustre

The ARCHER2 `/work` file systems use Lustre as a parallel file system
technology. The Lustre file system provides POSIX semantics (changes on
one node are immediately visible on other nodes) and can support very
high data rates for appropriate I/O patterns.

### Striping

One of the main factors leading to the high performance of Lustre file
systems is the ability to stripe data across multiple Object Storage
Targets (OSTs) in a round-robin fashion. Files are striped when the data
is split up in chunks that will then be stored on different OSTs across
the Lustre system. Striping might improve the I/O performance because it
increases the available bandwith since multiple processes can read and
write the same files simultaneously. However striping can also increase
the overhead. Choosing the right striping configuration is key to obtain
high performance results.

Users have control of a number of striping settings on Lustre file
systems. Although these parameters can be set on a per-file basis they
are usually set on directory where your output files will be written so
that all output files inherit the settings.

#### Default configuration

 The `/work` file systems on ARCHER2 have the same default stripe
 settings:

  - A default stripe count of 1
  - A default stripe size of 1 MiB (1048576 bytes)

These settings have been chosen to provide a good compromise for the
wide variety of I/O patterns that are seen on the system but are
unlikely to be optimal for any one particular scenario. The Lustre
command to query the stripe settings for a directory (or file) is `lfs
getstripe`. For example, to query the stripe settings of an already
created directory `res_dir`:

    [auser@archer2]$ lfs getstripe res_dir/
    res_dir
    stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1 

#### Setting Custom Striping Configurations

Users can set stripe settings for a directory (or file) using the
`lfs setstripe` command. The options for `lfs setstripe` are:

  - `[--stripe-count|-c]` to set the stripe count; 0 means use the
    system default (usually 1) and -1 means stripe over all available
    OSTs.
  - `[--stripe-size|-s]` to set the stripe size; 0 means use the system
    default (usually 1 MB) otherwise use k, m or g for KB, MB or GB
    respectively
  - `[--stripe-index|-i]` to set the OST index (starting at 0) on which
    to start striping for this file. An index of -1 allows the MDS to
    choose the starting index and it is strongly recommended, as this
    allows space and load balancing to be done by the MDS as needed.

For example, to set a stripe size of 4 MiB for the existing directory
`res_dir`, along with maximum striping count you would use:

    [auser@archer2]$ lfs setstripe -s 4m -c -1 res_dir/

### Recommended ARCHER2 I/O settings

With the default settings, parallel I/O on multiple nodes can
currently give poor perfomance. We recommend always setting the
following environment variable in your SLURM batch script:

    export FI_OFI_RXM_SAR_LIMIT=64K

Although I/O requirements vary significantly between different
applications, the following settings should be good in most cases:

  - If each process is writing to its own individual file then the default settings should give good performance.

  - If processes are writing to a single shared file (e.g. using
    MPI-IO, HDF5 or NetCDF), set the appropriate
    directories to be fully striped: `lfs setstripe -c -1 directory/`

## I/O Profiling

!!! note
    We will add advice on I/O profiling soon.
