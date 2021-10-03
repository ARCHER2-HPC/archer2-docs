# Data management and transfer

This section covers best practice and tools for data management on
ARCHER2.

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
   - RDFaaS (RDF as a Service) file systems (`/epsrc` and `/general`)

Each type of storage has different characteristics and policies, and is
suitable for different types of use.

There are also two different types of node available to users:

   - Login nodes
   - Compute nodes

Each type of node sees a different combination of the storage types.
The following table shows which storage options are avalable on 
different node types:

| Storage | Login Nodes | Compute Nodes | Notes | 
|---------|-------------|---------------|-------|
| /home   | yes         | no            | Backed up |
| /work   | yes         | yes           | Not backed up, high performance |
| RDFaaS  | yes         | no            | Backed up, high performance. Only currently available for projects that moved from ARCHER to ARCHER2. Currently read-only. |

### Home file systems

There are four independent home file-systems. Every project has an
allocation on one of the four. You do not need to know which one your
project uses as your projects space can always be accessed via the path
`/home/project-code`. Each home file-system is approximately 60TB in
size and is implemented using standard Network Attached Storage (NAS)
technology. This means that these disks are not particularly high
performance but are well suited to standard operations like compilation
and file editing. These file systems are visible from the ARCHER2 login
nodes.

The home file systems **are fully backed up**. Full backups are taken
weekly (for each of the past two weeks), daily (for each of the past
two days) and hourly (for each of the last 6 hours). You can access the
snapshots at the `/home1/.snapshot`, `/home2/.snapshot`, `/home3/.snapshot`
and `/home4/.snapshot` depending on which of the file systems you have
your home directories on. You can find out which file system your
home directory is on with the command:

```
readlink -f $HOME
```

These file-systems are a good location to keep source-code, copies of
scripts and compiled binaries. Small amounts of important data can also
be copied here for safe keeping though the file systems are not fast
enough to manipulate large datasets effectively.

!!! warning
    Files with filenames that contain non-ascii characters and/or
    non-printable characters cannot be backed up using our automated process
    and so will be omitted from all backups.

### Work file systems

There is one work file-system:

   - /work 3.4 PB

Every project has an allocation on the file system.

This is a high-performance, Lustre parallel file system. It is
designed to support data in large files. The performance for data stored
in large numbers of small files is probably not going to be as good.

This is the only file system that is available on the compute nodes
so all data read or written by jobs running on the compute nodes has to
be hosted here.

!!! warning
    There are no backups of any data on the work file system. You should
    not rely on these file systems for long term storage.

Ideally, this file system should only contain data that is:

   - actively in use;
   - recently generated and in the process of being saved elsewhere; or
   - being made ready for up-coming work.

In practice it may be convenient to keep copies of datasets on the work
file system that you know will be needed at a later date. However, make
sure that important data is always backed up elsewhere and that your
work would not be significantly impacted if the data on the work file
system was lost.

Large data sets can be moved to the RDF storage or transferred off the
ARCHER2 service entirely.

If you have data on the work file system that you are not going to need
in the future please delete it.


### RDFaaS file system

The data that was available on the RDF `/epsrc` and `/general` file systems
is available on ARCHER2 via the RDFaaS (RDF as a Service) file system. If 
you have requested the same username in the same project as you had on 
ARCHER then you will be able to access your data at either:

```
/epsrc/<project-code>
```

or

```
/general/<project-code>
```

depending on which file system it was in on the RDF file systems. For example,
if your username is `auser` and you are in the `e05` project, then your RDF data
will be on the RDFaaS file system at:

```
/epsrc/e05/e05/auser
```

The RDFaaS file systems are only available on the ARCHER2 login nodes.

!!! important
    The data on the RDFaaS file system is currently available in **read-only
    mode**. You need to transfer data from the RDFaaS file system to `/work`
    (or `/home` if it is a small amount of data) if you wish to alter it or
    use it on the compute nodes. You can, of course, also use `scp` to transfer
    data from the RDFaaS file system to another system.

!!! note
    We plan to make the RDFaaS file system read/write once the full ARCHER2
    service is available.

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
groups of their own, called subprojects, which can be assigned disk space
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

Putting these together:

    tar -cvWlf mydata.tar mydata

will create and verify an archive.

To extract files from a tar file, the option `-x` is used. For example:

    tar -xf mydata.tar

will recover the contents of `mydata.tar` to the current working
directory.

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

    scp [options] source user@login-4c.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

In the above example, the `[destination]` is optional, as when left out
`scp` will copy the source into your home directory. Also, the `source`
should be the absolute path of the file/directory being copied or the
command should be executed in the directory containing the source
file/directory.

If you want to request a different encryption algorithm add the `-c
[algorithm-name]` flag to the `scp` options. For example, to use the
(usually faster) *arcfour* encryption algorithm you would
    use:

    scp [options] -c aes128-ctr source user@login-4c.archer2.ac.uk:[destination]

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

    rsync [options] -e ssh source user@login-4c.archer2.ac.uk:[destination]

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

    rsync [options] -e "ssh -c arcfour" source user@login-4c.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

!!! tip
    Further information on using `rsync` can be found in the `rsync` manual
    (accessed via `man rsync` or at [man
    rsync](https://linux.die.net/man/1/rsync)).

### Data transfer via GridFTP

ARCHER2 provides a module for grid computing, `gct/6.2`, otherwise known
as the Globus Grid Community Toolkit v6.2.20201212. This toolkit provides
a command line interface for moving data to and from GridFTP servers. 

Data transfers are managed by the `globus-url-copy` command. Full details
concerning this command's use can be found in the [GCT 6.2 GridFTP User's Guide](https://gridcf.org/gct-docs/6.2/gridftp/user/index.html).

Please note, the GCT module does *not* yet support parallel streams.
We anticipate having this feature available soon. Please consult the
module help (`module help gct/6.2`) for confirmation of when this work
has been completed.

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

    rsync -Pv -e"ssh -c aes128-gcm@openssh.com -i $HOME/.ssh/id_RSA_A2" ./all_my_files.tar.gz otbz19@login-4c.archer2.ac.uk:/work/z19/z19/otbz19/

Note the use of the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. The `-e` flag allows specification of the ssh command - we
have used this to add the location of the identity file. 
The `-c` option specifies the cipher to be used as `aes128-gcm` which has been found to increase performance
Unfortunately
the `~` shortcut is not correctly expanded, so we have specified the
full path. We move our research archive to our project work directory on
ARCHER2.

!!! note
    Remember to replace `otbz19` with your username on ARCHER2.

If we were unconcerned about being able to restart an interrupted
transfer, we could instead use the `scp` command,

    scp -c aes128-gcm@openssh.com -i ~/.ssh/id_RSA_A2 all_my_files.tar.gz otbz19@login-4c.archer2.ac.uk:/work/z19/z19/otbz19/

but `rsync` is recommended for larger transfers.
