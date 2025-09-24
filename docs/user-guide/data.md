# Data management and transfer

This section covers best practice and tools for data management on
ARCHER2 along with a description of the different storage available
on the service.

The [IO section](io.md) has information on achieving good performance
for reading and writing data to the ARCHER2 storage along with information
and advice on different IO patterns.

!!! information
    If you have any questions on data management and transfer please do not
    hesitate to contact the ARCHER2 service desk at <support@archer2.ac.uk>.

## Useful resources and links

   - Harry Mangalam's guide on [How to transfer large amounts of data
     via
     network](https://hjmangalam.wordpress.com/2009/09/14/how-to-transfer-large-amounts-of-data-via-network/).
     This provides lots of useful advice on transferring data.

## Data management

We strongly recommend that you give some thought to how you use the
various data storage facilities that are part of the ARCHER2 service.
This will not only allow you to use the machine more effectively but
also to ensure that your valuable data is protected.

Here are the main points you should consider:

* **Not all data are created equal, understand your data.** Know what data you have. What is your
  critical data that needs to be copied to a secure location? Which data do you need in a different
  location to analyse? Which data would it be easier to regenerate rather than transfer? You should
  create a brief data management plan laying this out as this will allow you to understand which
  tools to use and when.
* **Minimise the data you are transferring.** Transferring large amounts of data is costly in both
  researcher time and actual time. Make sure you are only transferring the data you need to transfer.
* **Minimise the number of files you are transferring.** Each individual file has a static overhead in
  data transfers so it is efficient to bundle multiple files together into a single large
  archive file for transfer.
* **Does compression help or hinder?** Many tools have the option to use compression (e.g. `rsync`,
  `tar`, `zip`) and generally encourage you to use them to reduce data volumes. However, in some cases,
  the time spent compressing the data can take longer than actually transferring the uncompressed
  data; particularly when transferring data between two locations that both have large data transfer
  bandwidth available.
* **Be aware of encryption overheads.** When transferring data using `scp` (and `rsync` over `scp`)
  your data will be encrypted introducing a static overhead per file. This issue can be minimised by
  reducing the number files to be transferred by creating archives. You can also change the encryption
  algorithm to one that involves minimal encryption. The fastest performing cipher that is commonly
  available in SSH at the moment is generally `aes128-ctr` as most common processors provide a
  hardware implementation.

## ARCHER2 storage

The ARCHER2 service, like many HPC systems, has a complex structure.
There are a number of different data storage types available to users:

   - Home file systems
   - Work file systems
   - Solid state (NVMe) file system
   - RDFaaS (RDF as a Service) file systems (`/epsrc` and `/general`)

Each type of storage has different characteristics and policies, and is
suitable for different types of use.


!!! important
    All users have a directory on one of the home file systems and on
    one of the work file systems. The directories are located at:

    - `/home/[project ID]/[project ID]/[user ID]` (this is also set as your home directory)
    - `/work/[project ID]/[project ID]/[user ID]`


There are also three different types of node available to users:

   - Login nodes
   - Compute nodes
   - Data analysis nodes

Each type of node sees a different combination of the storage types.
The following table shows which storage options are available on
different node types:

| Storage | Login Nodes | Compute Nodes | Data analysis nodes | Notes     |
|---------|-------------|---------------|---------------------|-----------|
| /home   | yes         | no            | yes                 | Incremental backup |
| /work   | yes         | yes           | yes                 | No backup, high performance |
| Solid state (NVMe)   | yes         | yes           | yes                 | No backup, high performance |
| RDFaaS  | yes         | no            | yes                 | Disaster recovery backup |

!!! important
    Only the work file systems and the solid state (NVMe) file system are visible on
    the compute nodes. This means that all data required by calculations at runtime
    (input data, application binaries, software libraries, etc.) must be placed on
    one of these file systems.

    You may see "file not found" errors if you try to access data on the /home
    or RDFaaS file systems when running on the compute nodes.

### Home file systems

There are four independent home file-systems. Every project has an
allocation on one of the four. You do not need to know which one your
project uses as your projects space can always be accessed via the path
`/home/[project ID]` with your personal directory at `/home/[project ID]/[project ID]/[user ID]`. Each home file-system is approximately 100 TB in
size and is implemented using standard Network Attached Storage (NAS)
technology. This means that these disks are not particularly high
performance but are well suited to standard operations like compilation
and file editing. These file systems are visible from the ARCHER2 login
nodes.

#### Accessing snapshots of home file systems

The home file systems are **fully backed up**. The home file systems retain snapshots which can be used to recover past versions of files. Snapshots are taken weekly (for each of the past two weeks), daily (for each of the past two days) and hourly (for each of the last 6 hours). You can access the snapshots at `.snapshot` from any given directory on the home file systems. Note that the `.snapshot` directory will not show up under any version of “ls” and will not tab complete.

These file systems are a good location to keep source code, copies of scripts and compiled binaries. Small amounts of important data can also be copied here for safe keeping though the file systems are not fast enough to manipulate large datasets effectively.


#### Quotas on home file systems

All projects are assigned a quota on the home file systems. The project
PI or manager can split this quota up between users or groups of users
if they wish.

You can view any home file system quotas that apply to your account by
logging into SAFE and navigating to the page for your ARCHER2 login
account.

1. [Log into SAFE](https://safe.epcc.ed.ac.uk)
2. Use the "Login accounts" menu and select your ARCHER2 login account
3. The "Login account details" table lists any user or group quotas that
   are linked with your account. (If there is no quota shown for a row
   then you have an unlimited quota for that item, but you may still may
   be limited by another quota.)

!!! tip
    Quota and usage data on SAFE is updated twice daily so may not be
    exactly up to date with the situation on the systems themselves.

### Work file systems

There are currently three work file systems on the full ARCHER2 service.
Each of these file systems is 3.4 PB and a portion of one of these file
systems is available to each project. You do not usually need to know which one your
project uses as your projects space can always be accessed via the path
`/work/[project ID]` with your personal directory at `/work/[project ID]/[project ID]/[user ID]`.

All of these are high-performance, Lustre parallel file systems. They are
designed to support data in large files. The performance for data stored
in large numbers of small files is probably not going to be as good.

These file systems are available on the compute nodes and are the default
location users should use for data required at runtime on the compute nodes.

!!! warning
    There are no backups of any data on the work file systems. You should
    not rely on these file systems for long term storage.

Ideally, these file systems should only contain data that is:

   - actively in use;
   - recently generated and in the process of being saved elsewhere; or
   - being made ready for up-coming work.

In practice it may be convenient to keep copies of datasets on the work
file systems that you know will be needed at a later date. However, make
sure that important data is always backed up elsewhere and that your
work would not be significantly impacted if the data on the work file
systems was lost.

Large data sets can be moved to the RDFaaS storage or transferred off the
ARCHER2 service entirely.

If you have data on the work file systems that you are not going to need
in the future please delete it.

#### Quotas on the work file systems

As for the home file systems, all projects are assigned a quota on the
work file systems. The project PI or manager can split this quota up
between users or groups of users if they wish.

You can view any work file system quotas that apply to your account by
logging into SAFE and navigating to the page for your ARCHER2 login
account.

1. [Log into SAFE](https://safe.epcc.ed.ac.uk)
2. Use the "Login accounts" menu and select your ARCHER2 login account
3. The "Login account details" table lists any user or group quotas that
   are linked with your account. (If there is no quota shown for a row
   then you have an unlimited quota for that item, but you may still may
   be limited by another quota.)

!!! tip
    Quota and usage data on SAFE is updated twice daily so may not be
    exactly up to date with the situation on the systems themselves.

You can also examine up to date quotas and usage on the ARCHER2 systems
themselves using the `lfs quota` command. To do this:

- Change directory to the work directory where you want to check the
   quota. For example, if I wanted to check the quota for user `auser` in
   project `t01` then I would:

   ```
   cd /work/t01/t01/auser
   ```

- To check your user quota, you would use the command:

   ```
   auser@ln03:/work/t01/t01/auser> lfs quota -hu auser .
   Disk quotas for usr auser (uid 5496):
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
              .  1.366G      0k      0k       -    5486       0       0       -
   uid 5496 is using default block quota setting
   uid 5496 is using default file quota setting
   ```

   the `quota` and `limit` of `0k` here indicate that no user quota is set for this
   user

- To check your project quota, you would use the command:

   ```
   auser@ln03:/work/t01/t01/auser> lfs quota -hp $(id -g) .
   Disk quotas for prj 1009 (pid 1009):
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
              .  2.905G      0k      0k       -   25300       0       0       -
   pid 1009 is using default block quota setting
   pid 1009 is using default file quota setting
   ```

### Solid state (NVMe) file system - scratch storage

!!! important
    The solid state storage system is configured as *scratch* storage with all files
    that have not been accessed in the last 28 days being automatically deleted. The
	script which performs this purge is run daily.

!!! important
    Automatic deletion will be paused from 22 Aug 2025 to 13 Oct 2025. When automatic
	deletion resumes on 14 Oct 2025, the usual policy will apply: all files
    that have not been accessed in the last 28 days being automatically deleted.

The solid state storage file system is a 1 PB high performance parallel Lustre file system
similar to the work file systems. However, unlike the work file systems, all of the
disks are based solid state storage (NVMe) technology. This changes the performance characteristics of the
file system compared to the work file systems. Testing by the ARCHER2 CSE team at
EPCC has shown that you may see I/O performance improvements from the solid state
storage compared to the standard work Lustre file systems on ARCHER2 if your I/O model
has the following characteristics or similar:

- You read/write lots of files in parallel (e.g. your software uses a file-per-process
  model or similar)
- You use the [ADIOS 2](https://adios2.readthedocs.io/en/latest/) I/O system

Data on the solid state (NVMe) file system is visible on the compute nodes

!!! important
    If you use MPI-IO approaches to reading/writing data - this includes parallel HDF5
    and parallel NetCDF - then you very unlikely to see any performance improvements
    from using the solid state storage over the standard parallel Lustre file systems
    on ARCHER2.

!!! warning
    There are no backups of any data on the solid state (NVMe) file system. You should
    not rely on this file system for long term storage.

#### Access to the solid state file system

Projects do not have access to the solid state file system by default. If your project does
not yet have access and you want access for your project, please [contact the Service Desk](mailto:support@archer2.ac.uk)
to request access.

#### Location of directories

You can find your directory on the file system at:

```
/mnt/lustre/a2fs-nvme/work/<project code>/<project code>/<username>
```

For example, if my username is `auser` and I am in project `t01`, I could find my
solid state storage directory at:

```
/mnt/lustre/a2fs-nvme/work/t01/t01/auser
```

#### Quotas on solid state file system

!!! important
    All projects have the same, large quota of 250,000 GiB on the solid state
    file system to allow them to use it as a scratch file system. Remember, any
    files that have not been accessed in the last 28 days will be automatically
    deleted.

You query quotas for the solid state file system in the same way as
[quotas on the work file systems](#quotas-on-the-work-file-systems).

!!! bug
    Usage and quotas of the solid state file system are not yet available
    in SAFE - you should use commands such as `lfs quota -hp $(id -g) .`
    to query quotas on the solid state file system.

#### Identifying files that are candidates for deletion

You can identify which files you own that are candidates for imminent deletion at the
next daily scratch file system purge using the `find` command in the following format:

```
find /mnt/lustre/a2fs-nvme/work/<project code> -atime +28 -type f -print
```

For example, if my account is in project `t01`, I would use:

```
find /mnt/lustre/a2fs-nvme/work/t01 -atime +28 -type f -print
```

You may also wish to find which files are not yet deletion candidates, but which
are getting older. For example, you can use the `-atime` flag to have the command
return instead files which have not been accessed for 21 or more days with:

```
find /mnt/lustre/a2fs-nvme/work/<project code> -atime +21 -type f -print
```

As you should not use the scratch storage long term but instead look to stage data
on and off as required, keeping it in long term storage elsewhere, this can
potentially help highlight files you have missed.

Finally, the command

```
ls -lu
```

runs `ls` with list output using the last access times.

### RDFaaS file systems

The RDFaaS file systems provide additional capacity for projects to store data
that is not currently required on the compute nodes but which is too large for
the Home file systems.

!!! warning
    The RDFaaS file systems are backed up for disaster recovery purposes only (e.g.
    loss of the whole file system) so it is not possible to recover individual files
    if they are deleted by mistake or otherwise lost.

!!! tip
    Not all projects on ARCHER2 have access to RDFaaS, if you do have access, this
    will show up in the login account page on SAFE for your ARCHER2 login account.

If you have access to RDFaaS, you will have a directory in one of two file systems:
either `/epsrc` or `/general`.

For example, if your username is `auser` and you are in the `e05` project, then
your RDFaaS directory will be at:

```
/epsrc/e05/e05/auser
```

The RDFaaS file systems are not available on the ARCHER2 compute nodes.

!!! tip
    If you are having issues accessing data on the RDFaaS file system then
    please [contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html)

#### Copying data from RDFaaS to Work file systems

You should use the standard Linux `cp` command to copy data from the RDFaaS file
system to other ARCHER2 file systems (usually `/work`). For example, to
transfer the file `important-data.tar.gz` from the RDFaaS file system to
`/work` you would use the following command (assuming you are user `auser`
in project `e05`):

```
cp /epsrc/e05/e05/auser/important-data.tar.gz /work/e05/e05/auser/
```

(remember to replace the project code and username with your own username
and project code. You may also need to use `/general` if your data was
there on the RDF file systems).

### Subprojects

Some large projects may choose to split their resources into multiple subprojects.
These subprojects will have identifiers appended to the main project ID. For example,
the `rse` subgroup of the `z19` project would have the ID `z19-rse`. If the main
project has allocated storage quotas to the subproject the directories for this
storage will be found at, for example:
```
/home/z19/z19-rse/auser
```

Your Linux home directory will generally not be changed when you are made a member
of a subproject so you must change directories manually (or change the ownership of
files) to make use of this different storage quota allocation.

## Sharing data with other ARCHER2 users

How you share data with other ARCHER2 users depends on whether or not
they belong to the same project as you. Each project has two shared
folders that can be used for sharing data.

### Sharing data with ARCHER2 users in your project

Each project has an *inner* shared folder.

    /work/[project code]/[project code]/shared

This folder has read/write permissions for all project members. You can
place any data you wish to share with other project members in this
directory. For example, if your project code is x01 the inner shared
folder would be located at `/work/x01/x01/shared`.

### Sharing data with  ARCHER2 users within the same project group

Some projects have [subprojects](#subprojects) (also often referred to as a 'project groups' or sub-budgets)   e.g. project e123 might have a project group e123-fred  for a sub-group of researchers working with Fred.

Often project groups do not have a disk quota set, but if the project PI [does set up a group disk quota](https://epcced.github.io/safe-docs/safe-for-managers/#how-can-i-create-a-quota-for-a-project-group-or-move-space-between-quotas) e.g. for /work then additional directories are created:

	/work/e123/e123-fred
	/work/e123/e123-fred/shared
	/work/e123/e123-fred/<user> (for every user in the group)

and all members of the ```/work/e123/e123-fred``` group will be able to use the ```/work/e123/e123-fred/shared``` directory to share their files.

!!! Note
    If files are copied from their usual directories they will keep the original ownership. To grant ownership to the group:

	```chown -R $USER:e123-fred /work/e123/e123-fred/ ...```


### Sharing data with all ARCHER2 users

Each project also has an *outer* shared folder.:

    /work/[project code]/shared

It is writable by all project members and readable by any user on the
system. You can place any data you wish to share with other ARCHER2
users who are not members of your project in this directory. For example,
if your project code is x01 the outer shared folder would be located
at `/work/x01/shared`.

### Permissions

You should check the permissions of any files that you place in the shared area,
especially if those files were created in your own ARCHER2 account. Files of the
latter type are likely to be readable by you only.

The `chmod` command below shows how to make sure that a file placed in the outer
shared folder is also readable by all ARCHER2 users.

    chmod a+r /work/x01/shared/your-shared-file.txt

Similarly, for the inner shared folder, `chmod` can be called such that read
permission is granted to all users within the x01 project.

    chmod g+r /work/x01/x01/shared/your-shared-file.txt

If you're sharing a set of files stored within a folder hierarchy the `chmod`
is slightly more complicated.

    chmod -R a+Xr /work/x01/shared/my-shared-folder
    chmod -R g+Xr /work/x01/x01/shared/my-shared-folder

The `-R` option ensures that the read permission is enabled recursively and
the `+X` guarantees that the user(s) you're sharing the folder with can access
the subdirectories below `my-shared-folder`.

### Sharing data between projects and subprojects

Every file has an *owner* group that specifies access permissions for users
belonging to that group. It's usually the case that the group id is synonymous
with the project code. Somewhat confusingly however, projects can contain
groups of their own, called [subprojects](#sharing-data-with-archer2-users-within-the-same-project-group), which can be assigned disk space
quotas distinct from the project.

    chown -R $USER:x01-subproject /work/x01/x01-subproject/$USER/my-folder

The `chown` command above changes the owning group for all the files within
`my-folder` to the `x01-subproject` group. This might be necessary if previously
those files were *owned* by the x01 group and thereby using some of the x01
disk quota.

## Archiving and data transfer

Data transfer speed may be limited by many different factors so the best
data transfer mechanism to use depends on the type of data being
transferred and where the data is going.

   - **Disk speed** - The ARCHER2 /work file system is highly parallel,
     consisting of a very large number
     of high performance disk drives. This allows it to support a
     very high data bandwidth. Unless the remote system has a similar
     parallel file-system you may find your transfer speed limited by
     disk performance.
   - **Meta-data performance** - Meta-data operations such as opening
     and closing files or listing the owner or size of a file are much
     less parallel than read/write operations. If your data consists of
     a very large number of small files you may find your transfer
     speed is limited by meta-data operations. Meta-data operations
     performed by other users of the system will interact strongly with
     those you perform so reducing the number of such operations you
     use, may reduce variability in your IO timings.
   - **Network speed** - Data transfer performance can be limited by
     network speed. More importantly it is limited by the slowest
     section of the network between source and destination.
   - **Firewall speed** - Most modern networks are protected by some
     form of firewall that filters out malicious traffic. This
     filtering has some overhead and can result in a reduction in data
     transfer performance. The needs of a general purpose network that
     hosts email/web-servers and desktop machines are quite different
     from a research network that needs to support high volume data
     transfers. If you are trying to transfer data to or from a host on
     a general purpose network you may find the firewall for that
     network will limit the transfer rate you can achieve.

The method you use to transfer data to/from ARCHER2 will depend on how
much you want to transfer and where to. The methods we cover in this
guide are:

   - **scp/sftp/rsync** - These are the simplest methods of
     transferring data and can be used up to moderate amounts of data.
     If you are transferring data to your workstation/laptop then this
     is the method you will use.
   - **GridFTP** - It is sometimes more convenient to transfer large
     amounts of data (> 100 GBs) using GridFTP servers.

Before discussing specific data transfer methods, we cover *archiving*
which is an essential process for transferring data efficiently.

### Archiving

If you have related data that consists of a large number of small files
it is strongly recommended to pack the files into a larger "archive"
file for ease of transfer and manipulation. A single large file makes
more efficient use of the file system and is easier to move and copy and
transfer because significantly fewer meta-data operations are required.
Archive files can be created using tools like `tar` and `zip`.

#### tar

The `tar` command packs files into a "tape archive" format. The command
has general form:

    tar [options] [file(s)]

Common options include:

   - `-c` create a new archive
   - `-v` verbosely list files processed
   - `-W` verify the archive after writing
   - `-l` confirm all file hard links are included in the archive
   - `-f` use an archive file (for historical reasons, tar writes its
     output to stdout by default rather than a file).
   - `-b 2048` use a 1 MiB block size (better performance and less contention
     on Lustre compared to the default block size)

Putting these together:

    tar -cvWlf mydata.tar mydata

will create and verify an archive.

To extract files from a tar file, the option `-x` is used. For example:

    tar -b 2048 -xf mydata.tar

will recover the contents of `mydata.tar` to the current working
directory (using a block size of 1 MiB to improve Lustre performance and
reduce contention).

To verify an existing tar file against a set of data, the `-d` (diff)
option can be used. By default, no output will be given if a
verification succeeds and an example of a failed verification follows:

    $> tar -df mydata.tar mydata/*
    mydata/damaged_file: Mod time differs
    mydata/damaged_file: Size differs

!!! note
    tar files do not store checksums with their data, requiring
    the original data to be present during verification.



!!! tip
    Further information on using `tar` can be found in the `tar` manual
    (accessed via `man tar` or at [man
    tar](https://linux.die.net/man/1/tar)).

#### zip

The zip file format is widely used for archiving files and is supported
by most major operating systems. The utility to create zip files can be
run from the command line as:

    zip [options] mydata.zip [file(s)]

Common options are:

   - `-r` used to zip up a directory
   - `-#` where "\#" represents a digit ranging from 0 to 9 to specify
     compression level, 0 being the least and 9 the most. Default
     compression is -6 but we recommend using -0 to speed up the
     archiving process.

Together:

    zip -0r mydata.zip mydata

will create an archive.

!!! note
    Unlike tar, zip files do not preserve hard links. File data will be
    copied on archive creation, *e.g.* an uncompressed zip archive of a
    100MB file and a hard link to that file will be approximately 200MB in
    size. This makes zip an unsuitable format if you wish to precisely
    reproduce the file system layout.

The corresponding `unzip` command is used to extract data from the
archive. The simplest use case is:

    unzip mydata.zip

which recovers the contents of the archive to the current working
directory.

Files in a zip archive are stored with a CRC checksum to help detect
data loss. `unzip` provides options for verifying this checksum against
the stored files. The relevant flag is `-t` and is used as follows:

    $> unzip -t mydata.zip
    Archive:  mydata.zip
        testing: mydata/                 OK
        testing: mydata/file             OK
    No errors detected in compressed data of mydata.zip.

!!! tip
    Further information on using `zip` can be found in the `zip` manual
    (accessed via `man zip` or at [man
    zip](https://linux.die.net/man/1/zip)).

### Data transfer via SSH

The easiest way of transferring data to/from ARCHER2 is to use one of
the standard programs based on the SSH protocol such as `scp`, `sftp` or
`rsync`. These all use the same underlying mechanism (SSH) as you
normally use to log-in to ARCHER2. So, once the the command has been
executed via the command line, you will be prompted for your password
for the specified account on the *remote machine* (ARCHER2 in this
case).

To avoid having to type in your password multiple times you can set up a
*SSH key pair* and use an *SSH agent* as documented in the User Guide at
`connecting`.

#### SSH data transfer performance considerations

The SSH protocol encrypts all traffic it sends. This means that file
transfer using SSH consumes a relatively large amount of CPU time at
both ends of the transfer (for encryption and decryption). The ARCHER2
login nodes have fairly fast processors that can sustain about 100 MB/s
transfer. The encryption algorithm used is negotiated between the SSH
client and the SSH server. There are command line flags that allow you
to specify a preference for which encryption algorithm should be used.
You may be able to improve transfer speeds by requesting a different
algorithm than the default. The `aes128-ctr` or `aes256-ctr` algorithms
are well supported and fast as they are implemented in hardware.
These are not usually the default choice when using `scp` so
you will need to manually specify them.

A single SSH based transfer will usually not be able to saturate the
available network bandwidth or the available disk bandwidth so you may
see an overall improvement by running several data transfer operations
in parallel. To reduce metadata interactions it is a good idea to
overlap transfers of files from different directories.

In addition, you should consider the following when transferring data:

   - Only transfer those files that are required. Consider which data
     you really need to keep.
   - Combine lots of small files into a single *tar* archive, to reduce
     the overheads associated in initiating many separate data
     transfers (over SSH, each file counts as an individual transfer).
   - Compress data before transferring it, *e.g.* using `gzip`.

#### scp

The `scp` command creates a copy of a file, or if given the `-r` flag, a
directory either from a local machine onto a remote machine or from a
remote machine onto a local machine.

For example, to transfer files to ARCHER2 from a local machine:

    scp [options] source user@login.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

In the above example, the `[destination]` is optional, as when left out
`scp` will copy the source into your home directory. Also, the `source`
should be the absolute path of the file/directory being copied or the
command should be executed in the directory containing the source
file/directory.

If you want to request a different encryption algorithm add the `-c
[algorithm-name]` flag to the `scp` options. For example, to use the
(usually faster) *aes128-ctr* encryption algorithm you would
    use:

    scp [options] -c aes128-ctr source user@login.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

#### rsync

The `rsync` command can also transfer data between hosts using a `ssh`
connection. It creates a copy of a file or, if given the `-r` flag, a
directory at the given destination, similar to `scp` above.

Given the `-a` option rsync can also make exact copies (including
permissions), this is referred to as *mirroring*. In this case the
`rsync` command is executed with `ssh` to create the copy on a remote
machine.

To transfer files to ARCHER2 using `rsync` with `ssh` the command has
the form:

    rsync [options] -e ssh source user@login.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

In the above example, the `[destination]` is optional, as when left out
rsync will copy the source into your home directory. Also the `source`
should be the absolute path of the file/directory being copied or the
command should be executed in the directory containing the source
file/directory.

Additional flags can be specified for the underlying `ssh` command by
using a quoted string as the argument of the `-e` flag.
    e.g.

    rsync [options] -e "ssh -c aes128-ctr" source user@login.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

!!! tip
    Further information on using `rsync` can be found in the `rsync` manual
    (accessed via `man rsync` or at [man
    rsync](https://linux.die.net/man/1/rsync)).

### Data transfer via Globus

The ARCHER2 filesystems have a Globus Collection (formerly known as an endpoint) with the name "Archer2 file systems".
See the [ARCHER2 Globus documentation](../../data-tools/globus) for more information on using Globus to transfer data to/from ARCHER2 (this includes a full step-by-step guide on transferring data).

The Globus Command Line Interface (CLI) is also available on ARCHER2, see the Globus page linked above for more information.

### Data transfer via GridFTP

ARCHER2 provides a module for grid computing, `gct/6.2`, otherwise known
as the Globus Grid Community Toolkit v6.2.20201212. This toolkit provides
a command line interface for moving data to and from GridFTP servers.

Data transfers are managed by the `globus-url-copy` command. Full details
concerning this command's use can be found in the [GCT 6.2 GridFTP User's Guide](https://gridcf.org/gct-docs/6.2/gridftp/user/index.html).

!!! info
    Further information on using GridFTP on ARCHER2 to transfer
    data to the [JASMIN facility](https://www.jasmin.ac.uk) can be found
    in [the JASMIN user documentation](https://help.jasmin.ac.uk/article/4997-transfers-from-archer2).

### Data transfer using `rclone`

[Rclone](https://rclone.org/) is a command-line program to manage files on cloud
storage. You can transfer files directly to/from cloud storage services, such as
MS OneDrive and Dropbox. The program preserves timestamps and verifies checksums
at all times.

First of all, you must download and unzip `rclone` on ARCHER2:
```bash
wget https://downloads.rclone.org/v1.62.2/rclone-v1.62.2-linux-amd64.zip
unzip rclone-v1.62.2-linux-amd64.zip
cd rclone-v1.62.2-linux-amd64/
```

The previous code snippet uses rclone v1.62.2, which was the latest version when
these instructions were written.

Configure rclone using `./rclone config`. This will guide you through an
interactive setup process where you can make a new remote (called `remote`).
See the following for detailed instructions for:

   - [Microsoft OneDrive.](https://rclone.org/onedrive/)
   - [Dropbox.](https://rclone.org/dropbox/)

Please note that a token is required to connect from ARCHER2 to the cloud service.
You need a web browser to get the token. The recommendation is to run rclone
in your laptop using `rclone authorize`, get the token, and then copy the token
from your laptop to ARCHER2. The rclone website contains further instructions on
[configuring rclone on a remote machine without web browser.](https://rclone.org/remote_setup/)

Once all the above is done, you're ready to go. If you want to copy a directory,
please use:

```rclone copy <archer2_directory> remote:<cloud_directory>```

Please note that "remote" is the name that you have chosen when running
`rclone config`. To copy files, please use:

```rclone copyto <archer2_file> remote:<cloud_file>```

!!! note
    If the session times out while the data transfer takes place, adding the
    `-vv` flag to an rclone transfer forces rclone to output to the terminal and
    therefore avoids triggering the timeout process.

### Batch data transfer

While compute nodes in the standard partition are not connected to the
internet and cannot be used for data transfer, the methods discussed
above (e.g. `rclone`) are available from the serial partition by specifying:

```
#SBATCH --partition=serial
#SBATCH --qos=serial
```

## SSH data transfer example: laptop/workstation to ARCHER2

Here we have a short example demonstrating transfer of data directly
from a laptop/workstation to ARCHER2.

!!! note
    This guide assumes you are using a command line interface to transfer
    data. This means the terminal on Linux or macOS, MobaXterm local terminal
    on Windows or Powershell.

Before we can transfer of data to ARCHER2 we need to make sure we have an
SSH key setup to access ARCHER2 from the system we are transferring data
from. If you are using the same system that you use to log into ARCHER2 then
you should be all set. If you want to use a different system you will need
to generate a new SSH key there (or use SSH key forwarding) to allow you to
connect to ARCHER2.

!!! tip
    Remember that you will need to use both a key and your password to
    transfer data to ARCHER2.

Once we know our keys are setup correctly, we are now ready to transfer data directly between the
two machines. We begin by combining our important research data in to a
single archive file using the following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt file3.txt

We then initiate the data transfer from our system to ARCHER2, here using
`rsync` to allow the transfer to be recommenced without needing to start
again, in the event of a loss of connection or other failure. For example, using
the SSH key in the file `~/.ssh/id_RSA_A2` on our local system:

    rsync -Pv -e"ssh -c aes128-ctr -i $HOME/.ssh/id_RSA_A2" ./all_my_files.tar.gz otbz19@login.archer2.ac.uk:/work/z19/z19/otbz19/

Note the use of the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. The `-e` flag allows specification of the ssh command - we
have used this to add the location of the identity file.
The `-c` option specifies the cipher to be used as `aes128-ctr` which has been found to increase performance
Unfortunately
the `~` shortcut is not correctly expanded, so we have specified the
full path. We move our research archive to our project work directory on
ARCHER2.

!!! note
    Remember to replace `otbz19` with your username on ARCHER2.

If we were unconcerned about being able to restart an interrupted
transfer, we could instead use the `scp` command,

    scp -c aes128-ctr -i ~/.ssh/id_RSA_A2 all_my_files.tar.gz otbz19@login.archer2.ac.uk:/work/z19/z19/otbz19/

but `rsync` is recommended for larger transfers.
