# Data migration from ARCHER to ARCHER2

## Data management

We strongly recommend that you give some thought to how you use the
various data storage facilities that are part of the ARCHER2 service.
This will not only allow you to use the machine more effectively but
also to ensure that your valuable data is protected.

### ARCHER2 storage

The ARCHER2 service, like many HPC systems has a complex structure.
There are a number of different data storage types available to users:

   - Home file systems
   - Work file systems

Each type of storage has different characteristics and policies, and is
suitable for different types of use.

There are also two different types of node available to users:

   - Login nodes
   - Compute nodes

Each type of node sees a different combination of the storage types.

#### Home file systems

There are four independent home file-systems. Every project has an
allocation on one of the four. You do not need to know which one your
project uses as your projects space can always be accessed via the path
`/home/project-code`. Each home file-system is approximately 60TB in
size and is implemented using standard Network Attached Storage (NAS)
technology. This means that these disks are not particularly high
performance but are well suited to standard operations like compilation
and file editing. These file systems are visible from the ARCHER2 login
nodes and the pre-/post-processing nodes.

The home file systems **are fully backed up**. Full backups are taken
weekly with incremental backups added every day in between. Backups are
kept for disaster recovery purposes only. If you have accidentally lost
data from a backed-up file-system and have no other way of recovering
the data then contact us as quickly as possible but we may be unable to
assist.

These file-systems are a good location to keep source-code, copies of
scripts and compiled binaries. Small amounts of important data can also
be copied here for safe keeping though the file systems are not fast
enough to manipulate large datasets effectively.

!!! warning
    Files with filenames that contain non-ascii characters and/or
    non-printable characters cannot be backed up using our automated process
    and so will be omitted from all backups.

#### Work file systems

There is one work file-systems:

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

#### SSH from ARCHER to ARCHER2

For transfers which involve a 'push' from ARCHER to ARCHER2, it can be beneficial to transfer speed to take a small number of simple measures:

Use the address transfer.dyn.archer2.ac.uk in preference to login.archer2.ac.uk. The former provides a route with a higher peak bandwidth. The same ssh key pair can be used for either.

Use an up-to-date version of ssh on Archer, e.g., via
 
    module load ssh

Use a less heavyweight encryption cypher, e.g., via option

    scp -c aes128-gcm@openssh.com <file> user@transfer.dyn.archer2.ac.uk:<path>

For full details of Archer to Archer2 transfers, please see  the [ssh data transfer example](#ssh-data-transfer-example) below 

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
(usually faster) *arcfour* encryption algorithm you would
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

    rsync [options] -e "ssh -c arcfour" source user@login.archer2.ac.uk:[destination]

(Remember to replace `user` with your ARCHER2 username in the example
above.)

!!! tip
    Further information on using `rsync` can be found in the `rsync` manual
    (accessed via `man rsync` or at [man
    rsync](https://linux.die.net/man/1/rsync)).

#### SSH data transfer example

Here we have a short example demonstrating transfer of data directly
from ARCHER to ARCHER2. The first step will be to set up an SSH key for
access to ARCHER2 directly from ARCHER.

First log in to ARCHER, and generate a new SSH key. To do this we use
the following
    command:

    ssh-keygen -b 4096 -C "otbz01@archer -> otbz19@archer2" -f ~/.ssh/id_RSA_A2

This generates a new 4096 bit RSA SSH key with the comment
`otbz01@archer -> otbz19archer2`, and stores the private key in the file
`~/.ssh/id_RSA_A2`, and the public key in a corresponding `.pub` file.
During key generation process we are asked to enter a passphrase. SSH
keys should *always* be passphrase protected, but this is especially
important when they are kept on a publicly accessible machine such as
ARCHER. Please *do not* leave the passphrase blank when setting up your
SSH key.

Next we must add the new public key to ARCHER2 through SAFE. We can
either open the `id_RSA_A2.pub` file in our preferred text editor on
ARCHER, or use `cat id_RSA_A2.pub` to output the file contents to
screen. Either way we should carefully copy the full SSH key and
comment, and then in SAFE go to \*Login accounts - \<user\>@archer2 -
Add Credential\* to add the new SSH key. Once the new key is active you
can test that this has worked by attempting to ssh to ARCHER2 from
ARCHER.

All being well, we are now ready to transfer data directly between the
two machines. We begin by combining our important research data in to a
single archive file using the following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt file3.txt

We then initiate the data transfer from ARCHER to ARCHER2, here using
`rsync` to allow the transfer to be recommenced without needing to start
again, in the event of a loss of connection or other failure.

    rsync -Pv -e"ssh -c aes128-gcm@openssh.com -i /home/z01/z01/otbz01/.ssh/id_RSA_A2" ./all_my_files.tar.gz otbz19@login1.archer2.ac.uk:/work/z19/z19/otbz19/

Note the use of the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. The `-e` flag allows specification of the ssh command - we
have used this to add the location of the identity file. 
The `-c` option specifies the cipher to be used as `aes128-gcm` which has been found to increase performance
Unfortunately
the `~` shortcut is not correctly expanded, so we have specified the
full path. We move our research archive to our project work directory on
ARCHER2.

If we were unconcerned about being able to restart an interrupted
transfer, we could instead use the `scp` command,

    scp -c aes128-gcm@openssh.com -i ~/.ssh/id_RSA_A2 all_my_files.tar.gz otbz19@login1.archer2.ac.uk:/work/z19/z19/otbz19/

but `rsync` is recommended for larger transfers.
