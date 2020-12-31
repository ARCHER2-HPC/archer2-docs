# Data migration from ARCHER to ARCHER2

This short guide explains how to move data from the ARCHER service to the ARCHER2 service.

We have also created a [walkthrough video](https://youtu.be/gDTJy1nrQZk  "YouTube video walkthrough") to guide you.

!!! note
    This section assumes that you have an active ARCHER and ARCHER2 account, 
    and that you have successfully logged in to both accounts.
    
!!! tip
    Unlike normal access, ARCHER to ARCHER2 transfer has been set up to require 
    only one form of authentication. You will not need to generate a new SSH key
    pair to transfer data from ARCHER to ARCHER2 as your password will suffice.

First, login to the ARCHER(1) (making sure to change `auser` to your username):

    ssh auser@login.archer.ac.uk

Then, combine important research data into a single archive file using the 
following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt directory1/
    
Please be selective -- the more data you want to transfer, the more time it 
will take.

From ARCHER in particular, in order to get the best transfer performance,
we need to access a newer version of the SSH program. We do this by loading
the `openssh` module:

    module load openssh

## Transferring data using `rsync` (recommended)

Begin the data transfer from ARCHER to ARCHER2 using `rsync`:

    rsync -Pv -e"ssh -c aes128-gcm@openssh.com" \
           ./all_my_files.tar.gz a2user@transfer.dyn.archer2.ac.uk:/work/t01/t01/a2user

!!! important
    Notice that the hostname for data transfer from ARCHER to ARCHER2
    is not the usual login address. Instead, you use 
    `transfer.dyn.archer2.ac.uk`. This address has been configured to 
    allow higher performance data transfer and to allow access to 
    ARCHER with password only with no SSH key required.

When running this command, you will be prompted to enter your **ARCHER2**
password. Enter it and the data transfer will begin. Also, remember to 
replace `a2user` with your ARCHER2 username, and `t01` with the budget 
associated with that username.

The use of the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. The `-e` flag allows specification of the ssh command - we
have used this to add the location of the identity file. 
The `-c` option specifies the cipher to be used as `aes128-gcm` which has 
been found to increase performance. Unfortunately the `~` shortcut is not 
correctly expanded, so we have specified the full path. We move our research 
archive to our project work directory on ARCHER2.

## Transferring data using scp

If you are unconcerned about being able to restart an interrupted
transfer, you could instead use the `scp` command,

    scp -c aes128-gcm@openssh.com all_my_files.tar.gz \
        a2user@transfer.dyn.archer2.ac.uk:/work/t01/t01/a2user/

but `rsync` is recommended for larger transfers.

!!! important
    Notice that the hostname for data transfer from ARCHER to ARCHER2
    is not the usual login address. Instead, you use 
    `transfer.dyn.archer2.ac.uk`. This address has been configured to 
    allow higher performance data transfer and to allow access to 
    ARCHER with password only with no SSH key required.
