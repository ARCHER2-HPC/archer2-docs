# Data migration from the ARCHER2 4-cabinet system to the ARCHER2 full system

!!! important
    The full ARCHER2 system is not yet available so you will not be able to
    transfer any data yet. Users will be informed once the full system 
    is available and they can start transferring data and using the system.

This short guide explains how to move data from from the work file system on the ARCHER2 4-cabinet system
to the ARCHER2 full system. Your space on the home file system is shared between the ARCHER2 4-cabinet system 
and the ARCHER2 full system so everything from your home directory is already effectively
transferred.

!!! note
    This section assumes that you have an active ARCHER2 4-cabinet system 
    and ARCHER2 full system account, and that you have successfully logged 
    in to both accounts.
    
!!! tip
    Unlike normal access, ARCHER2 4-cabinet system to ARCHER2 full system transfer 
    has been set up to require only one form of authentication. You will only need
    one factor to authenticate from the 4-cab to the full system or vice versa. This
    factor can be either an SSH key (that has been registered against your account in 
    SAFE) or you can use your passowrd. If you have a large amount of data to transfer
    you may want to setup a passphrase-less SSH key on ARCHER2 full system and
    [use the data analysis nodes](../user-guide/data.md) to run transfers via a
    Slurm job.

## Transferring data interactively from the 4-cabinet system to the full system

First, login to the ARCHER2 4-cabinet system (making sure to change `auser` 
to your username):

    ssh auser@login-4c.archer2.ac.uk

Then, combine important research data into a single archive file using the 
following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt directory1/
    
Please be selective -- the more data you want to transfer, the more time it 
will take.

Unpack the archive file in the destination directory

    tar -xzf all_my_files.tar.gz

### Transferring data using `rsync` (recommended)

Begin the data transfer from the ARCHER2 4-cabinet system to the ARCHER2 full 
system using `rsync`:

    rsync -Pv all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user

When running this command, you will be prompted to enter your ARCHER2
password -- this is the same password for the ARCHER2 4-cabinet system 
and the ARCHER2 full system. Enter it and the data transfer will begin. 
Remember to replace `a2user` with your ARCHER2 username, and `t01` with 
the budget associated with that username.

We use the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. We move our research archive to our project work directory 
on the ARCHER2 full system.

### Transferring data using scp

If you are unconcerned about being able to restart an interrupted
transfer, you could instead use the `scp` command,

    scp all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user/

but `rsync` is recommended for larger transfers.
