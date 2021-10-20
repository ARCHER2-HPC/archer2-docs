# Data migration from ARCHER2-4cab to ARCHER2

This short guide explains how to move data from from `/work` on the ARCHER2-4cab service to the ARCHER2 full service.
Your `/home` directory is shared between ARCHER2-4cab and ARCHER2 so everything from your `/home` directory is 
already effectively transferred.

!!! note
    This section assumes that you have an active ARCHER2-4cab and ARCHER2 
    account, and that you have successfully logged in to both accounts.
    
!!! tip
    Unlike normal access, ARCHER2-4cab to ARCHER2 transfer has been set up to require 
    only one form of authentication. You will not need to generate a new SSH key
    pair to transfer data from ARCHER2-4cab to ARCHER2 as your password will suffice.

First, login to the ARCHER2-4cab (making sure to change `auser` to your username):

    ssh auser@login-4c.archer2.ac.uk

Then, combine important research data into a single archive file using the 
following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt directory1/
    
Please be selective -- the more data you want to transfer, the more time it 
will take.

## Transferring data using `rsync` (recommended)

Begin the data transfer from ARCHER2-4cab to ARCHER2 using `rsync`:

    rsync -Pv all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user

When running this command, you will be prompted to enter your ARCHER2
password -- this is the same password for the ARCHER2-4cab and ARCHER2 
full service. Enter it and the data transfer will begin. Remember 
to replace `a2user` with your ARCHER2 username, and `t01` with the 
budget associated with that username.

The use of the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. We move our research archive to our project work directory 
on ARCHER2 full service.

## Transferring data using scp

If you are unconcerned about being able to restart an interrupted
transfer, you could instead use the `scp` command,

    scp all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user/

but `rsync` is recommended for larger transfers.
