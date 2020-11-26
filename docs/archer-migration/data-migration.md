# Data migration from ARCHER to ARCHER2

This short guide explains how to move data from the ARCHER service to the ARCHER2 service.

Before we can transfer of data from ARCHER to ARCHER2 there are a couple of steps required:

1. Create an SSH key on the origin system (ARCHER in this case) to allow
   the data transfer to happen.
2. Add the public part of the SSH key you create to your ARCHER2 account 
   in SAFE.
   
!!! tip
    Remember that you will need to use both a key and your password to
    transfer data to ARCHER2.
    
The first step will be to set up an SSH key for access to ARCHER2 directly from ARCHER.

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
comment, and then in SAFE go to *Login accounts - [username]@archer2 -
Add Credential* to add the new SSH key. Once the new key is active you
can test that this has worked by attempting to ssh to ARCHER2 from
ARCHER.

All being well, we are now ready to transfer data directly between the
two machines. We begin by combining our important research data in to a
single archive file using the following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt file3.txt

From ARCHER in particular, in order to get the best transfer performance,
we need to access a newer version of the SSH program. We do this by loading
the `openssh` module:

    module load openssh
    

We then initiate the data transfer from ARCHER to ARCHER2, here using
`rsync` to allow the transfer to be recommenced without needing to start
again, in the event of a loss of connection or other failure.

    rsync -Pv -e"ssh -c aes128-gcm@openssh.com -i /home/z01/z01/otbz01/.ssh/id_RSA_A2" ./all_my_files.tar.gz otbz19@transfer.dyn.archer2.ac.uk:/work/z19/z19/otbz19/

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
    Remember to replace `otbz19` with your username on ARCHER2 and `otbz01`
    with your username on ARCHER

If we were unconcerned about being able to restart an interrupted
transfer, we could instead use the `scp` command,

    scp -c aes128-gcm@openssh.com -i ~/.ssh/id_RSA_A2 all_my_files.tar.gz otbz19@transfer.dyn.archer2.ac.uk:/work/z19/z19/otbz19/

but `rsync` is recommended for larger transfers.