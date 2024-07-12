# I/O performance and tuning

This section describes common IO patterns, best practice for
I/O and how to get good performance on the ARCHER2 storage.

Information on the file systems, directory layouts, quotas,
archiving and transferring data can be found in the
[Data management and transfer section](data.md).

The advice here is targeted at use of the parallel file
systems available on the compute nodes on ARCHER2 (i.e. **Not**
the home and RDFaaS file systems).

## Common I/O patterns

There are number of I/O patterns that are frequently used in
parallel applications:

### Single file, single writer (Serial I/O)

A common approach is to funnel all the I/O through one controller
process (e.g. rank 0 in an MPI program). Although this has the
advantage of producing a single file, the fact that only one client is
doing all the I/O means that it gains little benefit from the parallel
file system. In practice this severely limits the I/O rates, e.g. when
writing large files the speed is not likely to significantly exceed 1
GB/s.

### File-per-process (FPP)

One of the first parallel strategies people use for I/O is for each
parallel process to write to its own file. This is a simple scheme to
implement and understand and can achieve high bandwidth as, with many
I/O clients active at once, it benefits from the parallel Lustre
filesystem. However, it has the distinct disadvantage that the data
is spread across many different files and may therefore be very
difficult to use for further analysis without a data reconstruction
stage to recombine potentially thousands of small files.

In addition, having thousands of files open at once can overload the
filesystem and lead to poor performance.

!!! tip
    The ARCHER2 solid state file system can give very high performance
    when using this model of I/O

The [ADIOS 2](https://adios2.readthedocs.io/en/latest/) I/O library
uses an approach similar to file-per-process and so can achieve very
good performance on modern parallel file systems.

### File-per-node (FPN)

A simple way to reduce the sheer number of files is to write a file
per node rather than a file per process; as ARCHER2 has 128 CPU-cores
per node, this can reduce the number of files by more than a factor of
100 and should not significantly affect the I/O rates. However, it
still produces multiple files which can be hard to work with in
practice.

### Single file, multiple writers without collective operations

All aspects of data management are simpler if your parallel
program produces a single file in the same format as a serial code,
e.g. analysis or program restart are much more straightforward.

There are a number of ways to achieve this. For example, many
processes can open the same file but access different parts by
skipping some initial offset, although this is problematic when
writing as locking may be needed to ensure consistency. Parallel I/O
libraries such as MPI-IO, HDF5 and NetCDF allow for this form of
access and will implement locking automatically.

The problem is that, with many clients all individually accessing the
same file, there can be a lot of contention for file system resources,
leading to poor I/O rates. When writing, file locking can effectively
serialise the access and there is no benefit from the parallel
filesystem.

### Single Shared File with collective writes (SSF)

The problem with having many clients performing I/O at the same time
is that the I/O library may have to restrict access to one client at
a time by locking. However if I/O is done collectively, where the
library knows that all clients are doing I/O at the same time, then
reads and writes can be explicitly coordinated to avoid clashes and no
locking is required.

It is only through collective I/O that the full bandwidth of the file
system can be realised while accessing a single file.  Whatever I/O
library you are using, it is essential to use collective forms of the
read and write calls to achieve good performance.

## Achieving efficient I/O

This section provides information on getting the best performance out of
the parallel `/work` file systems on ARCHER2 when writing data,
particularly using parallel I/O patterns.

### Lustre technology

The ARCHER2 `/work` file systems use Lustre as a parallel file system
technology. It has many disk units (called Object Storage Targets or
OSTs), all under the control of a single Meta Data Server (MDS) so
that it appears to the user as a single file system.  The Lustre file
system provides POSIX semantics (changes on one node are immediately
visible on other nodes) and can support very high data rates for
appropriate I/O patterns.

In order to achieve good performance on the ARCHER2 Lustre file systems,
you need to make sure your IO is configured correctly for the type
of I/O you want to do. In the following sections we describe how to
do this.

### Summary: achieving best I/O performance

The configuration you should use depends on the type of I/O you are
performing. Here, we summarise the settings for two of the I/O patterns
described above: File-Per-Process (FPP, including using ADIOS2) and
Single Share File with collective writes (SSF).

Following sections describe the settings in more detail.

#### File-Per-Process (FPP)

- Optimal file system: NVMe-based Lustre
- Stripe count: single stripe (`-c 1`), this is the default on ARCHER2
- Stripe size: little impact on performance - default (1 MiB) is fine
- Environment variables: none required
- MPI transport protocol choice: N/A

#### Single Shared File with collective writes (SSF)

- Optimal file system: no difference between NVMe and HDD-based file systems
- Stripe count: Maximum striping (`-c -1`)
- Stripe size: little impact on performance - default (1 MiB) is fine
- Environment variables:
    * `export FI_OFI_RXM_SAR_LIMIT=64K`
    * `export MPICH_MPIIO_HINTS="*:cray_cb_write_lock_mode=2,*:cray_cb_nodes_multiplier=4”`
- MPI transport protocol choice: UCX, if possible

### Summary: typical I/O performance on ARCHER2

#### File-Per-Process (FPP)

We regularly run tests of FPP write performance on ARCHER2 `/work`` Lustre file
systems using the [benchio](https://github.com/EPCCed/epcc-reframe/tree/main/tests/synth/benchio) software in the following configuration:

- Number of MPI processes writing: 2048 (16 nodes each with 128 processes)
- Amount of data written: 65,536 MiB (32 MiB per process)
- Striping: stripe count = 1; stripe size = 1 MiB
- Environment variables: none
- MPI transport protocol: OFI

Typical write performance:

- NVMe-based file system:
    - Maximum: 200 GiB/s
    - Upper quartile: 180 GiB/s
    - Median: 150 GiB/s
    - Lower quartile: 135 GiB/s
- HDD-based file system:
    - Maximum: 170 GiB/s
    - Upper quartile: 160 GiB/s
    - Median: 90 GiB/s
    - Lower quartile: 30 GiB/s

#### Single Shared File with collective writes (SSF)

We regularly run tests of FPP write performance on ARCHER2 `/work`` Lustre file
systems using the [benchio](https://github.com/EPCCed/epcc-reframe/tree/main/tests/synth/benchio) software in the following configuration:

- Number of MPI processes writing: 2048 (16 nodes each with 128 processes)
- Amount of data written: 65,536 MiB (32 MiB per process)
- Striping: stripe count = -1 (maximum striping); stripe size = 1 MiB
- Environment variables: `FI_OFI_RXM_SAR_LIMIT=64K`, `MPICH_MPIIO_HINTS="*:cray_cb_write_lock_mode=2,*:cray_cb_nodes_multiplier=4”`
- MPI transport protocol: UCX

Typical write performance:

- NVMe-based file system:
    - Maximum: 20 GiB/s
    - Upper quartile: 11 GiB/s
    - Median: 10 GiB/s
    - Lower quartile: 9 GiB/s
- HDD-based file system:
    - Maximum: 22 GiB/s
    - Upper quartile: 12 GiB/s
    - Median: 10 GiB/s
    - Lower quartile: 8 GiB/s

### Striping

One of the main factors leading to the high performance of Lustre file
systems is the ability to store data on multiple OSTs. For many small
files, this is achieved by storing different files on different OSTs;
large files must be *striped* across multiple OSTs to benefit from the
parallel nature of Lustre.

When a file is striped it is split into chunks and stored across
multiple OSTs in a round-robin fashion. Striping can improve the I/O
performance because it increases the available bandwidth: multiple
processes can read and write the same file simultaneously by accessing
different OSTs. However striping can also increase the
overhead. Choosing the right striping configuration is key to obtain
high performance results.

Users have control of a number of striping settings on Lustre file
systems. Although these parameters can be set on a per-file basis they
are usually set on the directory where your output files will be
written so that all output files inherit the same settings.

#### Default configuration

 The `/work` file systems on ARCHER2 have the same default stripe
 settings:

  - A default stripe count of 1
  - A default stripe size of 1 MiB (2<sup>20</sup> = 1048576 bytes)

These settings have been chosen to provide a good compromise for the
wide variety of I/O patterns that are seen on the system but are
unlikely to be optimal for any one particular scenario. The Lustre
command to query the stripe settings for a directory (or file) is `lfs
getstripe`. For example, to query the stripe settings of an already
created directory `resdir`:

    auser@ln03:~> lfs getstripe resdir/
    resdir
    stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1

#### Setting custom striping configurations

Users can set stripe settings for a directory (or file) using the
`lfs setstripe` command. The options for `lfs setstripe` are:

  - `[--stripe-count|-c]` to set the stripe count; 0 means use the
    system default (usually 1) and -1 means stripe over all available
    OSTs.
  - `[--stripe-size|-S]` to set the stripe size; 0 means use the system
    default (usually 1 MB) otherwise use k, m or g for KB, MB or GB
    respectively
  - `[--stripe-index|-i]` to set the OST index (starting at 0) on which
    to start striping for this file. An index of -1 allows the MDS to
    choose the starting index and it is strongly recommended, as this
    allows space and load balancing to be done by the MDS as needed.

For example, to set a stripe size of 4 MiB for the existing directory
`resdir`, along with maximum striping count you would use:

    auser@ln03:~> lfs setstripe -S 4m -c -1 resdir/

### Environment variables

The following environment variables typically only have an impact for the case
when you using Single Shared Files with collective communications.
As mentioned above, it is very important to use collective calls when
doing parallel I/O to a single shared file.

However, with the default settings, parallel I/O on multiple nodes can
currently give poor performance. We recommend always setting these
environment variables in your SLURM batch script when
you are using the SSF I/O pattern:

    export FI_OFI_RXM_SAR_LIMIT=64K
    export MPICH_MPIIO_HINTS="*:cray_cb_write_lock_mode=2,*:cray_cb_nodes_multiplier=4”

<!-- TODO: add description of what these settings do -->

### MPI transport protocol

Setting the environment variables described above can improve
the performance of MPI collectives when handling large amounts of
data, which in turn can improve collective file I/O. An alternative is
to use the non-default UCX implementation of the MPI library as an
alternative to the default OFI version.

To switch library version see [the Application Development Environment section of the User Guide](../user-guide/dev-environment.md).

!!! note
    This will affect all your MPI calls, not just those related to
    I/O, so you should check the overall performance of your program
    before and after the switch. It is possible that other functions
    may run slower even if the I/O performance improves.

## I/O profiling

If you are concerned about your I/O performance, you should quantify
your transfer rates in terms of GB/s of data read or written to
disk. Small files can achieve very high I/O rates due to data caching
in Lustre. However, for large files you should be able to achieve a
maximum of around 1 GB/s for an unstriped file, or up to 10 GB/s for a
fully striped file (across all 12 OSTs).

!!! warning
    You share `/work` with all other users so I/O rates can be
    very variable, especially if the machine is heavily loaded.

If your I/O rates are poor then you can get useful summary information
about how the parallel libraries are performing by setting this
variable in your Slurm script

    export MPICH_MPIIO_STATS=1

Amongst other things, this will give you information on how many
independent and collective I/O operations were issued. If you see a
large number of independent operations compared to collectives, this
indicates that you have inefficient I/O patterns and you should check
that you are calling your parallel I/O library correctly.

Although this information comes from the MPI library, it is still
useful for users of higher-level libraries such as HDF5 as they all
call MPI-IO at the lowest level.

## Tips and advice for I/O

### Set an optimum blocksize when untar'ing data

When you are expanding a large tar archive file to the Lustre file systems
you should specify the `-b 2048` option to ensure that tar writes out data
in blocks of 1 MiB. This will improve the performance of your tar command
and reduce the impact of writing the data to Lustre on other users.
